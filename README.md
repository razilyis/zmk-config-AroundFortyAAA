# zmk-config-AroundFortyAAA (zmk-v0.4_inertial-scroll)

Around Forty AAA の ZMK v0.4（Zephyr 4.1）対応＋**慣性スクロール・制御Behavior・単四電池電源管理** 搭載版ファームウェアです。

本ブランチは、**ZMK v0.4 (Zephyr 4.1)** 環境において `razilyis/zmk-pmw3610-driver`（`Dev-v0.4_inertial-scroll` ブランチ）を採用し、左右デュアルトラックボールおよび単四Ni-MH電池向け昇圧電源管理（`zmk-feature-non-lipo-battery-management`）を維持した最新統合開発ブランチです。

---

## 主な実装内容・特徴

### 🟢 慣性スクロール & 低速スタビライザー (ZMK v0.4 対応)
- **自然な慣性スクロール**: レイヤー 6/7 でのスクロール操作時に、指を離した後も心地よい減速を伴う慣性スクロールを実行
- **低速カーソル安定化 (`low-speed-stabilizer`)**: 微小な手振れやノイズを相殺し、精密なポインティングを実現
- **制御Behaviorのサポート**:
  - `&pmw3610_inertia_toggle`: 慣性スクロールの ON / OFF 切り替え
  - `&pmw3610_scroll_direction_toggle`: 縦スクロール方向の正転 / 反転切り替え
  - `&pmw3610_horizontal_scroll_direction_toggle`: 横スクロール方向の正転 / 反転切り替え

### 🟢 左右デュアルトラックボール (Left & Right Trackball)
- **右Centralトラックボール (`trackball_R`)**: 通常カーソル、Slow Cursor（Layer 2, 3）、通常スクロール（Layer 6）、縦限定スクロール（Layer 7）、矢印キー操作（Layer 10）
- **左Peripheralトラックボール (`trackball_L`)**: Central側で受け取りスクロール変換処理を実行

### 🟢 単四Ni-MH電池 昇圧電源管理 (Non-LiPo Battery Management)
- **単四電池 1本 + TPS61021A 昇圧 + ADC分圧監視**:
  - `CONFIG_ZMK_NON_LIPO_BATTERY_MANAGEMENT=y`
  - `CONFIG_ZMK_NON_LIPO_MIN_MV=320` (1.00V相当)
  - `CONFIG_ZMK_NON_LIPO_MAX_MV=448` (1.40V相当)
  - `CONFIG_ZMK_NON_LIPO_LOW_MV=304` (0.95V相当シャットダウン)

### 🟢 ZMK v0.4 (Zephyr 4.1) への移行
- **最新 Zephyr 4.1 対応**: ZMK main（Zephyr 4.1 系統）を pin し、新規格に適合
- **新ボード定義形式への対応**: ボード指定を `xiao_ble//zmk`、インターコネクト ID を `seeed_xiao` に更新
- **Devicetree での NFC ピン GPIO 化**: Zephyr 4.1 での Kconfig 廃止に伴い、P0.09 / P0.10 の GPIO 再利用指定を DTS（`&uicr`）へ移行
- **外部モジュールの Zephyr 4.1 追従**:
  - `razilyis/zmk-pmw3610-driver` (`Dev-v0.4_inertial-scroll`): Zephyr 4.1 上流との衝突回避のため `pixart,pmw3610-alt` / `CONFIG_PMW3610_ALT_*` に完全追従

---

## クレジット・謝辞 (Credits & Respect)

- **[badjeff](https://github.com/badjeff)**
  - ZMK 用 PMW3610 ドライバおよび慣性スクロールエンジンの開発者。
- **[sekigon-gonnoc](https://github.com/sekigon-gonnoc)**
  - Non-LiPo 電池管理モジュールの開発者。
