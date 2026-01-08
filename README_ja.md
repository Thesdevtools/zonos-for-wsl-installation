# 非公式 Zonos WSL インストールガイド

> [!WARNING]
> これはコミュニティによってメンテナンスされている **非公式** ガイドです。
> Zonosは現在活発に開発されており（ベータ版）、手順が変更される可能性があります。
> 正確な情報は [公式Zonosリポジトリ](https://github.com/Zyphra/Zonos) をご確認ください。

> [!NOTE]
> **English Version**: [README.md](README.md)

このリポジトリでは、**WSL2 (Windows Subsystem for Linux)** 環境に **Zonos** をインストールするための手順をまとめています。

---

## 📋 前提条件

- **OS**: Windows 10 または 11 (WSL2有効化済み)
- **Linux**: WSL上の Ubuntu, Debian 等
- **GPU**: NVIDIA GPU (VRAM 6GB以上推奨)
  - **Hybridモデル**: RTX 3000シリーズ以降が必要
  - **ドライバ**: Windows側に最新のNVIDIAドライバが必要

---

## 🔧 公式手順との違い

WSL環境向けにいくつか手順を調整しています：

| 項目 | 公式ガイド | 本ガイドの対応 | 理由 |
|------|------------|----------------|------|
| **uvインストール** | `pip install -U uv` | 公式スクリプトを使用 | Debian/Ubuntu系ではPEP 668により `pip` インストールが失敗するため。 |
| **環境変数** | なし | `source ~/.local/bin/env` | スクリプトでインストールした `uv` にパスを通すため。 |
| **CUDA Toolkit** | 記載なし | `nvidia-cuda-toolkit` | Hybridモデル用のパッケージ（mamba-ssm等）をビルドするために必要。 |

---

## 🚀 インストール手順

### Step 0: WSLを起動

Windowsのターミナル（PowerShell または コマンドプロンプト）で以下を実行します：

```powershell
wsl
```

**⚠️ 注意: これ以降のコマンドはすべて WSL (Linux) 側で実行してください。**

---

### Step 1: システムパッケージのインストール

`espeak-ng` に加え、`git` や `curl` もインストールします。

```bash
sudo apt update
sudo apt install -y espeak-ng git curl
```

---

### Step 2: リポジトリのクローン

```bash
cd ~
git clone https://github.com/Zyphra/Zonos.git
cd Zonos
```

---

### Step 3: uvのインストール

公式推奨の `pip` ではなく、スクリプト経由でインストールします。

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.local/bin/env
```

---

### Step 4: Python依存関係のインストール

```bash
uv sync
uv pip install -e .
```

---

### Step 5: Hybridモデル用パッケージ（オプション）

**Hybridモデルを使用する場合のみ実行してください。**
（RTX 3000シリーズ以降のGPUが必要です）

**Debianユーザーへの注意**:
`nvidia-cuda-toolkit` は `non-free` リポジトリに含まれています。

```bash
# non-freeリポジトリを追加（Debianの場合のみ）
sudo sed -i 's/ main$/ main contrib non-free non-free-firmware/g' /etc/apt/sources.list
sudo apt update

# CUDA Toolkitのインストール
sudo apt install -y nvidia-cuda-toolkit

# パッケージのビルド
uv sync --extra compile
```

---

### Step 6: 動作確認

サンプルスクリプトを実行して確認します。

```bash
uv run sample.py
```

エラーなく完了し、`sample.wav` が生成されれば成功です。

---

### Step 7: Gradio WebUIの起動

```bash
uv run gradio_interface.py
```

ブラウザで `http://localhost:7860` を開いて使用できます。

---

## 📝 次回以降の起動方法

```bash
wsl
cd ~/Zonos
source ~/.local/bin/env
uv run gradio_interface.py
```

---

## 💡 トラブルシューティング

- **`pip install -U uv` でエラーになる**:
  - Step 3 の公式スクリプトを使用してください。
- **`uv sync --extra compile` でビルドエラー**:
  - `nvidia-cuda-toolkit` が正しくインストールされているか確認してください。
- **GPUが認識されない**:
  - Windows側のNVIDIAドライバを最新にしてください。
  - WSL内で `nvidia-smi` コマンドを実行して認識されているか確認してください。

---

## 📚 参考リンク

- [公式 Zonos リポジトリ](https://github.com/Zyphra/Zonos)
- [uv ドキュメント](https://docs.astral.sh/uv/)
