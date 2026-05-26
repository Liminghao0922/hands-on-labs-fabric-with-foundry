# Fabric AI Ready Data ハンズオン ラボ

このリポジトリは、Microsoft Fabric で AI Ready Data プラットフォームを構築し、Microsoft Foundry および Microsoft Teams から活用する 2 日間のハンズオン ラボである。

## ラボ概要

- Day1: Fabric で AI Ready Data プラットフォームを構築する
- Day2: Foundry から Fabric Data Agent を接続し、Teams へ公開してエンドツーエンドで動作確認する

## ドキュメント

- Day1 手順書: [day1-hands-on-fabric.md](day1-hands-on-fabric.md)
- Day2 手順書: [day2-hands-on-foundry.md](day2-hands-on-foundry.md)
- 統合版手順書: [hands-on-fabric.md](hands-on-fabric.md)

## 推奨の学習フロー

1. 先に Day1 を完了し、データ基盤・セマンティックモデル・Fabric Data Agent を準備する。
2. 続けて Day2 で Foundry Agent（gpt-5.4-mini）を作成・設定する。
3. Agent を Teams に公開し、Playground と Teams の両方で応答を確認する。

## 前提条件

- Azure サブスクリプションへのアクセス権と、リソース作成権限があること。
- Microsoft Fabric ポータルへアクセスできること。
- 必要なライセンス（環境に応じた Fabric / Power BI ライセンスなど）が付与されていること。
- Teams へのアプリ公開権限、または管理者承認フローを利用できること。

## 画像とプレースホルダー

- すべてのスクリーンショット用プレースホルダーは [image](image) 配下に配置している。
- ファイル名は差し替えしやすいように、英語で意味の分かる命名に統一している。
- Markdown 側のリンクはそのまま使えるため、同名ファイルで置き換えるだけで反映できる。
