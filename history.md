# 開発履歴

## 2026-07-31

### mainのGitHub Actions依存互換性修正

- **目的**: ZMK `v0.3.0`と互換性のないRGB LED module `main`による左右firmwareのビルド失敗を解消する。
- **変更内容**:
  - `zmk-rgbled-widget`をZMK v0.3向けの`v0.3-branch`へ変更。
  - `zmk-pmw3610-driver`を`razilyis/zmk-pmw3610-driver`の`main`へ変更。
  - PMW3610 `main`の現行APIに合わせ、左右のKconfigを`CONFIG_PMW3610_ALT_*`、devicetree compatibleを`pixart,pmw3610-alt`へ更新。
  - 未使用になった`badjeff` remoteを削除。
- **CI構成**:
  - workflow: `.github/workflows/build.yml`
  - reusable workflow: `zmkfirmware/zmk/.github/workflows/build-user-config.yml@v0.3.0`
  - board: `seeeduino_xiao_ble`
  - shield: `around_forty_aaa_right rgbled_adapter` / `around_forty_aaa_left rgbled_adapter` / `settings_reset`
  - West依存取得はreusable workflow内で実行。
- **確認内容**:
  - PMW3610 `main`とRGB LED `v0.3-branch`の組み合わせで左右をクリーンビルド。
  - 右Central: FLASH 29.91%、RAM 28.78%。
  - 左Peripheral: FLASH 23.22%、RAM 25.28%。
- **影響範囲**:
  - split: 右Central / 左Peripheral。
  - PMW3610ドライバとRGB LED moduleのビルド依存のみ。
  - レイヤー、キー配置、CPI、XY変換、SPI/IRQ配線、BLE接続数は変更なし。
