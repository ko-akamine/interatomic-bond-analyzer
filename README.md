# Interatomic Bond Analyzer (E3D / Allegro)

同変ニューラルネットワークポテンシャル（Equivariant GNN: NequIP / Allegro）を用いて、原子間の結合エネルギー寄与（$D_{ij}$）および核間反発（ZBL ポテンシャル）を原子スケールで直接算出・可視化するための解析リポジトリです。

有機分子、金属ナノクラスター、周期性結晶構造（CIF / POSCAR）など、水素（H）からプルトニウム（Pu）までの幅広い元素系に対応しています。

---

## 主な機能

- **原子間結合エネルギー分解 ($D_{ij}$)**:
  系全体のポテンシャルエネルギーから、任意の原子ペア間の結合エネルギー寄与（引力項 + 核間反発 ZBL 項）を高精度に算出。
- **マルチスケール・マルチマテリアル対応**:
  ASE（Atomic Simulation Environment）を統合し、内蔵有機分子（162種以上）、金属ナノクラスター（Icosahedron等）、外部結晶構造ファイル（CIF, POSCAR）を一元的に解析可能。
- **インタラクティブ 3D 可視化**:
  `py3Dmol` を用いた構造と原子インデックス（ラベル）の Jupyter Notebook 上での直感的確認。
- **特定ペア深掘り機能**:
  多重結合の比較（単結合 vs 二重結合）や、界面・表面吸着における相互作用の個別定量評価。

---

## ディレクトリ構成

interatomic-bond-analyzer/
├── bond_analysis_suite.ipynb               # 汎用解析用メインノートブック
├── E3D_tutorial_beginner_atomID_pair.ipynb # チュートリアル・検証用ノートブック
├── PARAMETERS.md                           # パラメータ設定・出力項目の詳細仕様書
├── README.md                               # 本ドキュメント
├── .gitignore                              # Git除外設定
└── s128_t64.nequip.ckpt                     # 事前学習済みモデル重み（非追跡）

---

## 環境構築 (Setup)

### 前提条件

* OS: Ubuntu / WSL2
* パッケージ管理: Conda (Miniconda / Anaconda)
* Python: 3.10

### 依存関係のセットアップ

# リポジトリのクローン
git clone [https://github.com/](https://github.com/)ko-akamine/interatomic-bond-analyzer.git
cd interatomic-bond-analyzer

# Conda 環境の作成と有効化
conda create -n bond-analyzer python=3.10 -y
conda activate bond-analyzer

# 依存ライブラリのインストール
pip install torch torchvision torchaudio --index-url [https://download.pytorch.org/whl/cpu](https://download.pytorch.org/whl/cpu)
pip install nequip ase py3Dmol ipykernel


### 学習済みモデルの配置

本プロジェクトのルートディレクトリに、事前学習済みモデルファイル（`s128_t64.nequip.ckpt`）を配置してください。
*(※ 容量削減のため、チェックポイントファイルは Git 追跡対象外に設定されています)*

---

## クイックスタート (Usage)

1. VS Code で本フォルダを開き、カーネルに `bond-analyzer` を選択します。
2. `bond_analysis_suite.ipynb` を開きます。
3. **セル 1 & 2**（モデルロード・関数定義）を実行します。
4. 実験設定セル（セル 3）で解析したい対象を選択して実行（`Shift + Enter`）します。

```python
# 例 1: 有機分子（酢酸）
out, atoms = inspect_and_analyze(molecule("CH3COOH"), name="AceticAcid")

# 例 2: 白金ナノクラスター (Pt13)
out, atoms = inspect_and_analyze(Icosahedron("Pt", noshells=2), name="Pt13", cutoff=3.5)

# 例 3: 結晶構造ファイル (TiO2 CIFを2x2x2スーパーセル展開)
crystal_raw = read("TiO2.cif")
out, atoms = inspect_and_analyze(crystal_raw.repeat((2, 2, 2)), name="TiO2_supercell", cutoff=3.5)

```

5. 必要に応じて後続の「特定ペア解析セル」を実行し、着目する原子番号（例: `atom_i = 0`, `atom_j = 1`）の詳細なエネルギー内訳（Core 項、ZBL 項、$D_{ij}$）を確認します。

各パラメータの意味や出力結果の読み解き方は、[PARAMETERS.md](PARAMETERS.md) を参照してください。

---

## 参考文献・クレジット

* **NequIP / Allegro**: Equivariant Graph Neural Networks for interatomic potentials.
* **ASE (Atomic Simulation Environment)**: Tools for atomistic simulations.
