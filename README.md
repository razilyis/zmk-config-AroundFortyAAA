# zmk-config-AroundFortyAAA

Around Forty AAA 用の ZMK ファームウェア設定です。  
Split 構成（Right: Central / Left: Peripheral）で、トラックボールと ZMK Studio に対応しています。

## このリポジトリの位置づけ

- `main`: 安定運用向け
- `dev-main`: 開発・調整中（`main` と一部動作が異なる）

---

## 主な特徴

- ZMK Firmware `v0.3` 対応
- PMW3610 ドライバに `badjeff/zmk-pmw3610-driver` を採用
- ZMK Studio 対応
- IME 切り替えマクロを搭載
  - Windows: `Alt + Grave (~)`
  - macOS: `Ctrl + Space`
- RGB LED ウィジェット連携
- Non-LiPo Battery Management（AAA 電池運用）対応

---

## `dev-main` の特徴（`main` との差分）

### 1. トラックボールのレイヤー別挙動を強化

`dev-main` では、右側トラックボールにレイヤー別プロセッサを追加しています。

- Layer `2`, `3`: Slow Cursor（低速・精密カーソル）
- Layer `6`: Scroll（通常スクロール）
- Layer `7`: Vertical Scroll（縦スクロール限定）

> `main` では上記のレイヤー別挙動は未実装（通常カーソル挙動のみ）。

### 2. AAA 電池向けの電圧レンジを再調整

Non-LiPo バッテリー設定を、単四 1 本 + TPS61021A 昇圧構成に合わせて更新。

- `CONFIG_ZMK_NON_LIPO_MIN_MV=320`
- `CONFIG_ZMK_NON_LIPO_MAX_MV=480`
- `CONFIG_ZMK_NON_LIPO_LOW_MV=304`

> `main` の旧設定（352/448/0）より、実運用に寄せた閾値へ調整。

---

## 現在の注意点

- Prospector Scanner 対応は見送り中です。  
  理由: Bluetooth 接続が不安定になりやすいため。

---

## 補足

- キーマップは Windows / macOS の両系統レイヤーを用意
- 設定レイヤーから Bluetooth プロファイル切り替え・クリア・リセット操作が可能
