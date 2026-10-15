# Fabric AI Ready Data ハンズオン ラボ

このリポジトリは、Microsoft Fabric で AI Ready Data プラットフォームを構築し、Microsoft Foundry と Foundry IQ で業務データ・SharePoint 文書・公開 Web 情報を活用する 2 日間のハンズオン ラボである。検証済みの環境では Microsoft Teams への公開も行う。

## ラボ概要

- Day1: Fabric で AI Ready Data プラットフォームと Ontology を構築し、セマンティックモデルの集計基準値と比較用 Data Agent を準備する
- Day2: SharePoint Remote、Fabric Ontology、Web の順に Foundry IQ の知識庫へ接続し、サイト権限切り替え、数値集計、文書 RAG、複合質問を検証する

## ドキュメント

- Day1 手順書: [day1-hands-on-fabric.md](day1-hands-on-fabric.md)
- Day2 手順書（Foundry IQ 版）: [day2-hands-on-foundry.md](day2-hands-on-foundry.md)
- SharePoint 実験文書: [V1（サイト 1 用）](data/sharepoint/v1/営業改善施策ガイド.docx) / [V2（サイト 2 用）](data/sharepoint/v2/営業改善施策ガイド.docx)。2 つのテストサイトへ分けて配置し、ユーザーが両方を同時に読める状態にしない。

## 推奨の学習フロー

1. Day1 でデータ基盤・セマンティックモデル・Ontology を準備する。Fabric Data Agent も比較用として作成し、集計結果を確認する。
2. Day2 で `gpt-5.6-luna` を Global Standard、**80,000 TPM を手動設定**して 1 リソースあたり 1 つデプロイし、Agent と IQ で共用する。Foundry ポータルから AI Search を Serverless で作成・接続する。
3. SharePoint Remote 単独構成で、サイト 1 の V1 に対する権限で 8% を確認する。権限操作は講師が admin アカウントで行い、サイト 1 の権限を削除してサイト 2 の V2 へ権限を付与した後、参加者は同じユーザー アカウントで 12% を確認する。
4. 同じ知識庫の SharePoint Remote を残したまま Ontology を追加し、数値と社内ガイドの複合回答を確認する。最後に Web 知識源を追加し、3 知識源を使う複合回答を確認する。
5. 元の Fabric Data Agent は削除せず保持する。代替として使う場合は別の知識庫／Agent で実施し、Ontology の成功とは分けて記録する。
6. 公開後のユーザー ID 透過を事前検証した環境では、Agent を Teams に公開し、知識源ごとに動作確認する。

## 前提条件

- Azure サブスクリプションへのアクセス権と、リソース作成権限があること。
- Microsoft Fabric ポータルへアクセスできること。
- 必要なライセンス（環境に応じた Fabric / Power BI ライセンスなど）が付与されていること。
- Foundry IQ 用の AI Search を Foundry ポータルから作成できる権限、対応するモデル、作成後の ID・RBAC 設定に必要な権限があること。AI Search の事前作成は不要で、運営指定の既存リソースも再利用できる。
- モデルは `gpt-5.6-luna` の同一デプロイを Agent と IQ で共有し、IQ 用の別モデルは追加しない。IQ は対応する `2026-08-01-preview` を使用し、ポータルの対応とモデル接続を事前確認する。80,000 TPM と RPM は両方の呼び出しで共有する。
- 運営は同じサブスクリプション・リージョン・モデル・デプロイ種類のクォータから、`80,000 TPM × デプロイ数` と既存の割り当てを確保できることを確認する。既定の TPM 値を使用せず、人数に合わせて割り当てを計画する。
- 本ラボは AI Search の Serverless（プレビュー）を使用する。運営は対応リージョン、モデル接続、利用者 ID 透過、共有時の同時利用を事前検証する。Basic は必要な場合の代替構成とし、Serverless からの直接の価格レベル変更ではなく、別サービスで再検証する。
- Fabric Ontology のテナント設定、データバインディング、参照元データへの利用者権限を確認できること。Ontology 接続の Retrieval reasoning effort は low または medium を使用する。
- Azure と同一テナントの SharePoint、実験文書を管理するアカウント、一般利用者のテストアカウントを用意できること。
- SharePoint を検索するユーザーに Microsoft 365 Copilot ライセンス、または前提条件を満たす Retrieval API の従量課金設定があること。
- 実際の機密情報ではなく、公開可能なサンプルデータと架空の文書を使用すること。
- Teams 拡張を実施する場合は、必要なリソース作成権限とアプリ公開権限、または管理者承認フローを利用できること。

Day2 の詳細な認証・ライセンス条件は [手順書の事前確認](day2-hands-on-foundry.md#事前確認)を参照する。SharePoint Remote は独自の文書取り込み処理を不要にするが、権限変更の即時反映を保証するものではない。

## 命名規則

特定のイベントや顧客に依存しないよう、リソース名の共通要素を **`aidata`（AI Ready Data）** とする。Day1、Day2 ともに次の名前を使う。

| 対象 | 名前 |
| --- | --- |
| リソース グループ | `rg-aidatahandson＜No＞` |
| Fabric 容量 | `aidatafabricsku＜No＞` |
| Storage アカウント | `aidatastorage＜No＞` |
| Event Hubs 名前空間 / Event Hub | `aidataeventhubnamespace＜No＞` / `aidataeventhubname＜No＞` |
| SQL 論理サーバー / データベース | `aidatasqldbserver＜No＞` / `aidatadb` |
| Fabric ワークスペース | `aidataws＜No＞` |
| Lakehouse / Warehouse | `aidatalakehouse＜No＞` / `aidatadwh＜No＞` |
| Eventhouse / Eventstream | `aidataeventhouse＜No＞` / `aidataeventstream＜No＞` |
| セマンティックモデル / Ontology | `aidatasemantic＜No＞` / `aidataontology＜No＞` |
| 比較用 Fabric Data Agent | `aidatadataagent＜No＞` |
| Foundry / Azure AI Search | `foundry-aidata-＜unique＞` / `srch-aidata-＜unique＞` |
| Foundry Agent / IQ 知識庫 | `aidata-agent-app＜No＞` / `aidata-kb-＜No＞` |
| IQ 知識源 | `aidata-ontology-＜No＞` / `aidata-sp-remote-＜No＞` / `aidata-web-＜No＞` |

- `＜No＞` は運営が割り当てた参加者番号（例: `01`）に置き換え、同じ参加者は全手順で同じ番号を使う。
- `＜unique＞` は運営指定の一意な小文字英数字に置き換える。Storage、SQL サーバー、Event Hubs 名前空間などで名前が使用済みの場合は、サービスの命名制約を満たす一意な接尾辞を付け、その実名を以降の URL、接続、コマンドでも使う。Storage 名は小文字英数字のみ、全体で 3～24 文字とする。
- 配布データは [aidatadb.bacpac](data/aidatadb.bacpac) と [fact_Sale.parquet](data/fact_Sale.parquet) を使用し、ファイル名の大文字・小文字も一致させる。既存のデータファイルをリネームする必要はない。
- 既存環境で別の名前を使用している場合は、リソースの改名・再作成を要求しない。対応する実名と ID を記録して読み替える。
- 画像や既存の説明用 PDF に旧名称が残っている場合、本文の名前を正とする。画面で選択するときは自分の実際のリソースを確認する。

## 画像とプレースホルダー

- すべてのスクリーンショット用プレースホルダーは [image](image) 配下に配置している。
- ファイル名は差し替えしやすいように、英語で意味の分かる命名に統一している。
- Markdown 側のリンクはそのまま使えるため、同名ファイルで置き換えるだけで反映できる。
- Day2 の既存画像は操作位置の参考である。IQ の新しい操作は本文に従い、実際のテナントで取得した画像を追加する場合も本文の設定値・検証条件を優先する。
