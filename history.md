# 開発履歴 (history.md)

## 2026-04-14
### 実施内容: Dev-v0.3_inertial-scroll-v2 の適用
- **目的**: AroundForty-AAA 右手トラックボールに PMW3610 driver の慣性スクロール対応ブランチを適用する。
- **ブランチ**: `dev-main-v2`

#### 技術的詳細
1. **west manifest 更新**:
   - `config/west.yml`: `zmk-pmw3610-driver` の revision を `dev-v0.3_against-runaway` から `Dev-v0.3_inertial-scroll-v2` に変更。
2. **右手 PMW3610 の慣性スクロール有効化**:
   - `config/boards/shields/around_forty_aaa/around_forty_aaa_right.overlay`: `trackball_R` に `inertial-scroll` を追加。
   - `inertial-scroll-layers = <6 7>;` として、既存の通常スクロール layer 6 と縦限定スクロール layer 7 を対象に設定。
   - tuning は driver 側 README と同じ `gain=130`, `decay=99`, `interval=10ms`, `threshold=4` を明示。
3. **トグル behavior node の定義**:
   - `config/around_forty_aaa.keymap`: `#include <behaviors/pmw3610_inertia_toggle.dtsi>` を追加。
   - 実キー配置は未実施。Keymap Editor 側で `&pmw3610_inertia_toggle` を配置できるようにするため、driver 側 dtsi から behavior node を取り込む。

#### 影響範囲
- 右手 Central 側 PMW3610 (`trackball_R`)。
- レイヤー 6 / 7 のスクロール入力。
- トグル behavior の keymap 配置は未実施。
