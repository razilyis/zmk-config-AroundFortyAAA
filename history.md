# 開発履歴 (history.md)

## 2026-07-31
### 実施内容: dev-mainの左手スクロール感度を2倍化
- **目的**: 左手トラックボールのスクロール移動量を従来の2倍にする。
- 左手入力を処理する`trackball_listener_L`の`zip_xy_scaler`を`1/64`から`1/32`へ変更。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right`（左手入力を処理する右Central）
  - split: 左Peripheralから届くトラックボール入力のCentral側変換のみ。
  - 左手の縦横スクロール感度のみ変更。右手のカーソル・スクロール、キー配置、BLE設定は変更なし。

### 実施内容: dev-mainの左手上下スクロール方向を反転
- **目的**: 左手トラックボールの上下スクロール方向だけを従来と逆にする。
- 右Centralの`trackball_listener_L`から`INPUT_TRANSFORM_Y_INVERT`を削除し、`INPUT_TRANSFORM_XY_SWAP`は維持。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right`（左手入力を処理する右Central）
  - split: 左Peripheralから届くトラックボール入力のCentral側変換のみ。
  - 左手の上下スクロール方向のみ変更。横スクロール、倍率、右手カーソル、レイヤー、キー配置、BLE設定は変更なし。

### 実施内容: dev-mainのPMW3610暴走対策版を再ビルド
- **目的**: `Dev-v0.3_inertial-scroll`の最新暴走対策をAAAのGitHub Actionsビルドへ反映する。
- `config/west.yml`の`zmk-pmw3610-driver`をコミット`c74b37c526547fa7931c9e855362176599fdeae1`へ更新。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right` / `around_forty_aaa_left`
  - split: 右Central / 左Peripheral。
  - レイヤー、キー配置、CPI、XY変換、SPI/IRQ配線、BLE設定は変更なし。

### 実施内容: dev-mainのPMW3610微小振動フィルタを明示
- **目的**: 打鍵・クリック時の微小振動をPMW3610のカーソル・スクロール入力として扱わないようにする。
- 左右PMW3610へ`motion-threshold = <1>`を明示。
- AAA `dev-main`では未指定時もドライバ既定値`1`だったため、動作を固定・可視化する変更。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right` / `around_forty_aaa_left`
  - split: 右Central / 左Peripheral。
  - 左右トラックボールを使用する全レイヤー。
  - CPI、XY変換、キー配置、SPI/IRQ、BLE設定は変更なし。

### 実施内容: dev-main単一カーソル構成のGitHub Actions再検証
- **目的**: `dev-dual_cursor`で発生したカーソル暴走を切り分けるため、左手をスクロール専用、右手をカーソルとする`dev-main`を再ビルドする。
- **PMW3610依存**:
  - `config/west.yml`は`razilyis/zmk-pmw3610-driver`の`Dev-v0.3_inertial-scroll`で検証した修正版を参照。
  - 再現可能なビルドのため、リモートコミット`fe219c7eb267050fff6743f00b51c7cf6b029839`へ固定。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right` / `around_forty_aaa_left`
  - split: 右Central / 左Peripheral。
  - 右PMW3610は通常カーソルとレイヤー6・7の慣性スクロール。
  - 左PMW3610はCentral側でスクロールへ変換し、カーソルとしては使用しない。
  - キー配置、CPI、SPI/IRQ、BLE設定は変更なし。

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
