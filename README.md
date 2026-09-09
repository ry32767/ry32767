<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:03111d,45:0b4f66,100:2dd4bf&height=190&section=header&text=ry32767&fontSize=54&fontColor=ffffff&fontAlignY=34&desc=underwater%20localization%20%C2%B7%20PCB%20%C2%B7%20topology%20optimization&descAlignY=54&descSize=15&animation=fadeIn" alt="header" />

<img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=20&pause=1200&color=FFD166&center=true&vCenter=true&width=680&height=45&lines=Finding+where+you+are%2C+underwater.;Optics+for+bearing%2C+acoustics+for+range.;Simulate+first%2C+then+solder.;Let+the+load+decide+the+shape." alt="typing" />

</div>

---

## 📣 近日公開 — 水中自己位置推定の OSS

> **水中で「自分がどこにいるか」を推定するためのライブラリを OSS として公開します。**
>
> GPS が届かない水中で、**親機カメラの方位角（光学）× 音響測距（距離）**を組み合わせて
> 子機の 3D 位置・軌道を推定する仕組みを、シミュレーションから実機まで一貫して扱える形で出す予定です。
> 濁りでビーコンを見失う領域では **距離 + IMU + 深度** のフォールバックへ自動で切り替わり、
> SBL / USBL / VSLAM といった既存手法との横並び比較も同じコードベースで回せます。
>
> 現在は [`aquabeacon-sim`](https://github.com/ry32767/aquabeacon-sim) で
> 数式仕様・推定器・統計検証（CRLB / NEES / ブートストラップ CI）を固めているところです。
> ⭐ / Watch していただけると公開時に気づけます。

<br>

## 🌊 いま作っているもの

### 🛰️ AquaBeacon — 光学 × 音響の水中測位
[`aquabeacon-sim`](https://github.com/ry32767/aquabeacon-sim) — **実機を作る前に数式で確かめる**ための MBD 環境。

- **観測モデル**: 親機カメラの方位角・仰角（光学）+ 音響距離 + IMU + 深度センサをバッチ最小二乗で融合
- **統計的な裏取り**: 経験 RMSE が **CRLB に漸近**（効率 ≈ 0.98–1.01）、**NEES ≈ 3**（報告共分散が較正済み）
- **落ちない設計**: 濁り・水深で光学が劣化する領域を検出し、距離 + IMU + 深度へ**自動切替**
  （プルーム通過シナリオで 200 mm → **33 mm**）
- **外れ値耐性**: マルチパス・見失いに対して Huber / Cauchy の M 推定（411 mm → **30 mm**）
- **比較手法も実装**: SBL・USBL・VSLAM 再測位・広域 SLAM（閉ループで利得 ~9.5×）・水面 GNSS カメラ
- **設計スペックシート**: 「測位 RMSE ≤ 100 mm には 距離 ≤ 15 m / 角度ノイズ ≤ 0.36°」のように、
  **目標精度から設計要求を逆算**する

### 🔌 基板設計 — シミュレーションを実機に降ろす
AquaBeacon を水に入れるための回路・基板を **KiCad** で設計中。
電源まわり、センサ・音響フロントエンド、マイコン周辺を、
LTspice で定数を詰め → 回路図 → アートワーク → 実装、の順に進めています。
シミュレーションが出した**設計要求（ノイズ・帯域・同期精度）をそのまま基板の仕様に落とす**のが狙いです。

### 🧱 トポロジー最適化 × 3D プリント
[`TO_printing`](https://github.com/ry32767/TO_printing) — 「たぶん丈夫」ではなく**数値で確かめてから印刷する**ツール。

- STL → **ボクセル FEA**（たわみ・von Mises 応力・安全率）→ **トポロジー最適化** → 造形性判定 → 印刷
- **積層方向（直交異方性）を最適化に組み込み**。寝かせるか立てるかで安全率が 2 倍以上変わる
- **オーバーハング制約**: 45° 円錐の支持判定で、非支持ボクセル 460 → **0**、要サポート面積 4.7% → **3.0%**
- **幾何マルチグリッド + GPU** でソルバを刷新し、実部品の解析が **59.7 分 → 22 秒**（結果は 4 桁一致）
- **Gmsh + CalculiX による独立再解析**でクロスバリデーション
  （※メッシュ収束はまだ取れていないので、数値は「途中の値」として扱っています）

<br>

## 🗂️ そのほか

| | |
|---|---|
| 🔮 [**Grimoire Graph**](https://ry32767.github.io/Grimoire-Graph/) | 関数を「描いて」魔法に変える、ブラウザで動くターン制の関数バトル RPG（[repo](https://github.com/ry32767/Grimoire-Graph)） |
| ⛰️ [**GeoSection**](https://ry32767.github.io/GeoSection/) | 登山 GPX から断面図・傾斜角グラフ・3D 地形 STL を生成（[repo](https://github.com/ry32767/GeoSection)） |
| 🗺️ [**YamaGuessr**](https://github.com/ry32767/YamaGuessr) | 山の風景から場所を当てるゲーム |
| 🐜 [**Langton-Ant-Siege**](https://github.com/ry32767/Langton-Ant-Siege) | ラングトンのアリを題材にしたシミュレーション／ゲーム |
| ⚡ [**logic-circuit-sim**](https://github.com/ry32767/logic-circuit-sim) | ブラウザで動く論理回路シミュレータ |

数式・地形・回路みたいな、**計算がそのまま形になるもの**が好きです。

<br>

## 🛠️ Tech Stack

<div align="center">

**Languages**<br>
![C](https://img.shields.io/badge/C-A8B9CC?style=for-the-badge&logo=c&logoColor=000000) ![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white) ![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white) ![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white) ![MATLAB](https://img.shields.io/badge/MATLAB-0076A8?style=for-the-badge&logo=mathworks&logoColor=white)

**Numerics / Simulation**<br>
![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white) ![SciPy](https://img.shields.io/badge/SciPy-8CAAE6?style=for-the-badge&logo=scipy&logoColor=white) ![PyTorch](https://img.shields.io/badge/PyTorch%20(CUDA)-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white) ![CalculiX](https://img.shields.io/badge/CalculiX-2E6E9E?style=for-the-badge&logoColor=white) ![Gmsh](https://img.shields.io/badge/Gmsh-B23A48?style=for-the-badge&logoColor=white)

**Hardware / EDA / CAD**<br>
![KiCad](https://img.shields.io/badge/KiCad-314CB0?style=for-the-badge&logo=kicad&logoColor=white) ![LTspice](https://img.shields.io/badge/LTspice-9B1B30?style=for-the-badge&logoColor=white) ![OpenSCAD](https://img.shields.io/badge/OpenSCAD-F9D72C?style=for-the-badge&logo=openscad&logoColor=000000) ![Fusion 360](https://img.shields.io/badge/Fusion%20360-F47320?style=for-the-badge&logo=autodesk&logoColor=white) ![Bambu Studio](https://img.shields.io/badge/Bambu%20Studio-00AE42?style=for-the-badge&logoColor=white)

**Frameworks & Tools**<br>
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB) ![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white) ![Node.js](https://img.shields.io/badge/Node.js-5FA04E?style=for-the-badge&logo=nodedotjs&logoColor=white) ![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white) ![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)

</div>

<br>

## 📊 GitHub Stats

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github-readme-stats.vercel.app/api?username=ry32767&show_icons=true&count_private=true&hide_border=true&bg_color=041521&title_color=2dd4bf&text_color=d7f5ee&icon_color=ffd166">
  <img src="https://github-readme-stats.vercel.app/api?username=ry32767&show_icons=true&count_private=true&hide_border=true&bg_color=f7fdfc&title_color=0f766e&text_color=0b2530&icon_color=b45309" height="165" alt="stats" />
</picture>
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github-readme-stats.vercel.app/api/top-langs/?username=ry32767&layout=compact&langs_count=8&hide_border=true&bg_color=041521&title_color=2dd4bf&text_color=d7f5ee">
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=ry32767&layout=compact&langs_count=8&hide_border=true&bg_color=f7fdfc&title_color=0f766e&text_color=0b2530" height="165" alt="top languages" />
</picture>

<br><br>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://streak-stats.demolab.com?user=ry32767&hide_border=true&background=041521&ring=2dd4bf&fire=ffd166&currStreakLabel=2dd4bf&sideLabels=d7f5ee&currStreakNum=d7f5ee&sideNums=d7f5ee&dates=7fdbcd&stroke=0b4f66">
  <img src="https://streak-stats.demolab.com?user=ry32767&hide_border=true&background=f7fdfc&ring=0f766e&fire=b45309&currStreakLabel=0f766e&sideLabels=0b2530&currStreakNum=0b2530&sideNums=0b2530&dates=0f766e&stroke=cdeae4" height="165" alt="streak" />
</picture>

</div>

<br>

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:2dd4bf,55:0b4f66,100:03111d&height=110&section=footer" alt="footer" />

</div>
