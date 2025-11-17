# スパコン利用メモ（ABCI環境ベース）

これは、ABCIのような共有リソース（スパコン）でAI学習などを行う際の基本的なワークフローをまとめたメモです。

## 1\. 基本思想：ログインノード / 計算ノード

スパコンは「**ログインノード**」と「**計算ノード**」で役割が完全に分かれています。

  * **ログインノード**
      * SSHで最初に接続する場所。
      * **禁止事項:** 重い計算（`python train.py` など）の実行。
      * **許可事項:** ファイル編集、コードのコンパイル、`pip install`、ジョブの投入（`qsub`, `qrsh`）。
  * **計算ノード**
      * 実際の計算（AI学習）が行われる場所。A100 GPUなどが搭載されています。
      * `qsub` や `qrsh` を通じてのみ利用できます。
      * **特徴:** ジョブ開始時は「まっさらな状態」です。ログインノードの環境（`module load` 履歴など）は引き継ぎません。

## 2\. 環境構築：`module` と `venv`

環境構築は、ABCIが提供する `module`（基盤）と、ユーザーが作る `venv`（Pythonライブラリ）の2段階で行います。**`conda` は非推奨**です。

`module` が提供するCUDA/Python（基盤）と、`pip` でインストールする `torch` などのライブラリ（上物）をクリーンに分離・管理できるため、スパコン環境と非常に相性が良いため`conda`ではなく`venv`を推奨。

### 2-1. `module` コマンド

`module` は、システムにインストールされた基盤ソフトウェア（Python本体, CUDAなど）を管理するコマンドです。

  * **`module avail`**

      * **意味:** 利用可能なモジュール（ソフトウェア）の一覧を表示します。
      * **例:** `$ module avail` (cuda, pythonなどのバージョンを確認できる)

  * **`module load [モジュール名]`**

      * **意味:** 指定したモジュールを、現在の環境（シェル）に読み込みます。
      * **例:** `$ module load python/3.12/3.12.9 cuda/12.1/12.1.1`

  * **`module list`**

      * **意味:** 現在ロードされているモジュールの一覧を表示します。

  * **`module purge`**

      * **意味:** 現在ロードされているモジュールを**すべて解除**し、まっさらな状態に戻します。
      * **注意:** ログインノードで実行すると、`ls` などの基本コマンドまでパスが切れ、動かなくなる場合があります。通常はジョブスクリプトの先頭で使い、環境をリセットします。

### 2-2. 主要モジュール解説

`module avail` で表示される `/gpgpu` や `/devtools` カテゴリのモジュールが重要です。

  * **`python/...`**: AI学習の土台となるPython本体です。
  * **`cuda/...`**: GPU (A100) を動かすための基本プラットフォームです。インストールするPyTorchのCUDAバージョン（例: `cu121`）と**一致させる**必要があります。
  * **`cudnn/...`**: ディープラーニングの計算（特に畳み込み）を高速化するライブラリ。`cuda` とセットでロードします。
  * **`nccl/...`**: 複数GPU間（例: 8GPU）の通信を高速化するライブラリ。**分散学習（`torchrun` など）に必須**です。

### 2-3. 【手順】環境構築フロー（ログインノードで1回だけ実行）

```bash
# 1. ベースとなるPythonとCUDAを選択してロード
module load python/3.12/3.12.9 cuda/12.1/12.1.1

# 2. venv (仮想環境) を作成 (例: .venv)
python -m venv /path/to/my_project/.venv

# 3. venv を有効化
source /path/to/my_project/.venv/bin/activate

# 4. 必要なライブラリをpipでインストール
# (例: PyTorch CUDA 12.1 対応版)
(venv) $ pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
(venv) $ pip install -r requirements.txt

# 5. 完了したら無効化
(venv) $ deactivate
```

-----

## 3\. ジョブ実行：計算ノードの使い方

ジョブの実行方法は「インタラクティブジョブ（テスト用）」と「バッチジョブ（本番用）」の2種類です。

### 3-1. インタラクティブジョブ (`qrsh`) ：デバッグ・テスト用

短時間のテスト（`import`確認、`hello world`）やデバッグに使う方法です。

**重要:** GPUを使わないテスト（`import`確認など）は、課金レートが安い\*\*CPUノード（`rt_C.small`など）\*\*をリクエストしてください。

```bash
# 10分間、CPUノードを1つ借りて接続する
$ qrsh -g [グループ名] -l rt_C.small=1 -l h_rt=0:10:00

# --- (接続後、計算ノード上で操作) ---
(calc_node)$ module load python/3.12/3.12.9
(calc_node)$ source /path/to/my_project/.venv/bin/activate
(calc_node)$ python -c 'import torch; print("Success")'
(calc_node)$ exit
```

### 3-2. バッチジョブ (`qsub`) ：本番の学習用

本番の重い学習（`train.py`）に使う標準的な方法です。`run.sh` という「予約伝票（ジョブスクリプト）」を作成し、`qsub` で提出します。

```bash
# ログインノードでジョブを提出
$ qsub run.sh
```

-----

## 4\. テンプレート： `qsub` 用ジョブスクリプト（`run.sh`）

`qsub` で提出するスクリプトのテンプレートです。

```bash
#!/bin/bash

# --- 1. qsub リソース指定 ---
# (利用するノードの種類と数。例: A100 8GPUノードを1台)
#$ -l rt_G.large=1 

# (最大計算時間。例: 24時間)
#$ -l h_rt=24:00:00

# (エラー出力と標準出力を結合)
#$ -j y

# (ログ出力ファイル名。$JOB_ID は自動でジョブ番号に置き換わる)
#$ -o output.$JOB_ID.log

# (qsubを実行したディレクトリでジョブを実行)
#$ -cwd

# --- 2. 堅牢化設定 (重要) ---
# いずれかのコマンドが失敗したら、即座にスクリプト全体を停止する
set -eu -o pipefail

# --- 3. 計算ノードでの環境設定 ---
# (重要) venv作成時と「全く同じ」モジュールをロードする
echo "INFO: Loading modules..."
module load python/3.12/3.12.9 cuda/12.1/12.1.1 cudnn/9.13/9.13.0 nccl/2.28/2.28.3-1

# (重要) venvを有効化
echo "INFO: Activating venv..."
source .venv/bin/activate

# --- 4. 事前チェック (推奨) ---
echo "INFO: Running pre-flight check (import test)..."
python -c "import torch; import einops; print('SUCCESS: Pre-flight check passed.')"

# --- 5. 本番の実行コマンド ---
echo "INFO: Starting training..."
torchrun --nnodes=1 --nproc_per_node=8 train.py \
    --model DiT-XL/2 \
    --data-path /path/to/dataset \
    --batch-size 256

echo "INFO: Job finished successfully."
```

-----

## 5\. ジョブ管理

  * **`qstat`**

      * **意味:** ジョブの状況を確認します。
      * `qw` (Queued/Waiting): キューで待機中。
      * `r` (Running): 計算ノードで実行中。

  * **`qdel [ジョブID]`**

      * **意味:** 待機中または実行中のジョブをキャンセル（削除）します。

-----

## 6\. 重要Tips

  * **課金の仕組み:** 課金は「`-l h_rt=24:00:00`（リクエストした最大時間）」ではなく、「**ジョブが計算ノードで開始してから終了するまでの実時間**」に対して発生します。10秒で`ImportError`で失敗した場合、課金は**約10秒分**です（24時間分ではありません）。
  * **`set -e` の活用:** ジョブスクリプトの先頭に `set -e` を書くことで、`module load` のタイポや `ImportError` が発生した瞬間にジョブが停止し、無駄な待ち時間と計算資源の消費を防げます。