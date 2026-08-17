# 開発履歴 (history.md)

## 2026-08-17
### 実施内容: `zmk-v0.4_inertial-scroll` ブランチの作成 (ZMK v0.4 / Zephyr 4.1 & PMW3610 Inertial Scroll)
- **目的**: AroundForty-AAA にて ZMK v0.4 (Zephyr 4.1) および `razilyis/zmk-pmw3610-driver` (`Dev-v0.4_inertial-scroll`) への対応を実施。
- **ブランチ**: `zmk-v0.4_inertial-scroll` (ベース: `main`)
- **主な変更点**:
  - `config/west.yml`: `zmk` を `6e2ef41e` (ZMK main / Zephyr 4.1)、`zmk-pmw3610-driver` を `Dev-v0.4_inertial-scroll`、`zmk-rgbled-widget` を `e6b4677` に更新。
  - `build.yaml`: ボード ID を `xiao_ble//zmk` に更新。
  - `around_forty_aaa.zmk.yml`: `requires: [seeed_xiao]` に更新。
  - `around_forty_aaa.dtsi`: `&uicr { nfct-pins-as-gpios; };` を追加。
  - `around_forty_aaa_left.conf` / `around_forty_aaa_right.conf`: `CONFIG_NFCT_PINS_AS_GPIOS=y` を削除、`CONFIG_BT_HCI_TX_STACK_SIZE_WITH_PROMPT=y` を追加、Kconfig シンボルを `CONFIG_PMW3610_ALT_*` へ更新。
  - **維持事項**: 単四Ni-MH電池用電源管理 (`CONFIG_ZMK_NON_LIPO_*` 320〜448mV)、左右デュアルトラックボール制御構造。
  - **ドライバー補正**: `zmk-pmw3610-driver` (`Dev-v0.4_inertial-scroll`) にて Peripheral ビルド時の `zmk_layer_state_changed` 未定義リンカーエラー回避を適用。
  - `around_forty_aaa_left.overlay` / `around_forty_aaa_right.overlay`: PMW3610 compatible を `pixart,pmw3610-alt` へ更新。右トラックボールに `low-speed-stabilizer;` を追加。
  - `around_forty_aaa.keymap`: PMW3610 制御 behavior dtsi インクルードを追加。

## 2026-07-31
### 実施内容: 左スクロールの異常値上限を追加
- **目的**: 左PMW3610の中規模な異常デルタが、Central側のスクロール変換で大きなホイール移動になることを防ぐ。
- 左PMW3610へ`max-motion-delta = <128>`と`max-report-delta = <128>`を追加。
- westのPMW3610慣性スクロール版を`e3d60ed`へ更新し、SPIタイミング修正と蓄積デルタ保護を取り込み。
- 左スクロールの`1/32`感度とXY変換、右手のカーソル・スクロール設定は維持。
- **影響範囲**:
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_left`（Peripheralの左PMW3610）
  - 変換処理: `around_forty_aaa_right`（Centralの`trackball_listener_L`、設定変更なし）
  - レイヤー、キー配置、BLE接続数、スタック、ACLバッファは変更なし。

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
