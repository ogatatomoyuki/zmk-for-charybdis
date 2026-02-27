# Charybdis Nano Wireless — ZMK Firmware

## アクティブブランチ

**`main`** — `migration/official-zmk` はマージ済み・削除済み（2026-02-27）。

## キーマップファイル

- `config/charybdis.keymap` — これが唯一の正しいキーマップ

## ビルド・フラッシュ

1. コミット・プッシュで GitHub Actions が自動ビルド（`.github/workflows/build.yml`）
2. `gh run download <run_id> --dir firmware/` でファームウェア取得
3. フラッシュ順序: 右手 → 左手（Nice Nano をダブルリセットでマウント → `cp *.uf2 /Volumes/NICENANO/`）
4. 起動順序: 右手先 → 10秒待ち → 左手

## 関連ノート

- HANDOFF: `Notes/40_Projects/プライベート_キーボードカスタマイズ設定/charybdis nano wireless/`
- 設計書: `Notes/40_Projects/プライベート_キーマップ変更/docs/keymap_35.md`（参考。実装と差分あり）
