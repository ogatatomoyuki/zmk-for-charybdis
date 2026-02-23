# Charybdis Nano ZMK キーマップ設定ノート

## 現在の構成

- **コントローラー**: nice!nano v1
- **ファームウェア**: ZMK main ブランチ
- **キーマップ**: Callum式 One-Shot ベース（hold-tap 排除）
- **右側**: マスター/セントラル（トラックボール付き）

## レイヤー構成

```
BASE: QWERTY + 親指に NAV/SPC/SYM | BSPC/ENT
NAV:  ESC, Swapper, Tabber, CapsLock, 矢印, マクロ, マウスクリック, スクロール, ズーム
SYM:  数字, 記号, One-Shot Mods
FUN:  F1-F12, 音量, BT設定（NAV+SYM 同時押し）
```

## コンボ

| キー | 結果 |
|------|------|
| SPC + BSPC | LNG1（IME ON）|
| BSPC + ENT | LNG2（IME OFF）|
| D + F | Ctrl+B（tmux prefix）|

## 未解決タスク

### tmux コンボ（D+F → Ctrl+B）が動作しない

**日付**: 2026-02-21

**症状**:
- IME コンボ（SPC+BSPC, BSPC+ENT）は正常動作
- NAV レイヤーの変更も反映済み
- D+F コンボのみ発火しない

**調査済み**:
- キーポジション 27, 28 は matrix transform で D, F に正しくマッピング
- `timeout-ms = <80>`, `require-prior-idle-ms = <150>` に設定
- ファームウェアは最新ビルド（2026-02-21）で正しく書き込み済み

**次の調査ステップ**:
- `require-prior-idle-ms` を削除して再テスト（alpha キーに不向きの可能性）
- timeout をさらに広げてテスト（200ms等）
- 別のキーコンビネーション（J+K 等）でコンボが動くか検証
- 単純なバインディング（`&kp A` 等）に変えてコンボ自体の動作確認

## フラッシュ手順

**重要: `cp` ではなく `dd` を使うこと。**

```bash
# フラッシュ順序: 右（マスター）→ 左（ペリフェラル）
# settings_reset が必要な場合は両側に先に焼いてから本番ファーム

# リセットボタン2回押し → NICENANO ドライブがマウント
dd if=charybdis_right-nice_nano-zmk.uf2 of=/Volumes/NICENANO/firmware.uf2 bs=4096
dd if=charybdis_left-nice_nano-zmk.uf2 of=/Volumes/NICENANO/firmware.uf2 bs=4096
```

- `cp` は書き込みが不完全になる場合がある（2026-02-21 発見）
- I/O error は device のリブートによるもので正常
- フラッシュ後に BT 接続が切れた場合:
  1. Mac の Bluetooth 設定で古い「Charybdis Nano」を削除
  2. settings_reset を両側に焼く → 本番ファームを焼き直す
  3. Mac で再ペアリング

## ビルド方法

GitHub Actions で自動ビルド。

```bash
gh run list --limit 3
gh run download <run_id> -D firmware
```

## 変更履歴

### 2026-02-21
- tmux コンボの timeout を 50ms → 80ms に変更、require-prior-idle-ms 追加
- settings_reset + 本番ファーム再フラッシュで Mac BT ペアリング復旧
- dd フラッシュ方式に統一（cp の不具合発見）
