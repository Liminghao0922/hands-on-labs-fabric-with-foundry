# Fabric AI Ready Data Day2 ハンズオン手順

## 目次

- [Fabric AI Ready Data Day2 ハンズオン手順](#fabric-ai-ready-data-day2-ハンズオン手順)
  - [目次](#目次)
    - [Day2 のゴール](#day2-のゴール)
    - [事前確認](#事前確認)
    - [Foundry リソースを作成し、モデルをデプロイする](#foundry-リソースを作成しモデルをデプロイする)
    - [Agent を作成する](#agent-を作成する)
    - [Fabric Data Agent を接続する](#fabric-data-agent-を接続する)
    - [Agent を公開し、エンドポイントを確認する](#agent-を公開しエンドポイントを確認する)
    - [Teams へ公開する](#teams-へ公開する)
    - [Playground と Teams で動作確認する](#playground-と-teams-で動作確認する)
    - [補足](#補足)

### Day2 のゴール

- Foundry 上で `gpt-5.4-mini` を利用する Agent を作成する。
- Day1 で作成した Fabric Data Agent を Foundry Agent から呼び出せるようにする。
- Agent を Teams に公開し、Teams から問い合わせできるようにする。

![day2 overview 01](images/day2-overview-01.png)

### 事前確認

1. Day1 の完了を確認する。
   - ワークスペース: `aidataws＜No＞`
   - セマンティックモデル: `aidatasemantic＜No＞`
   - Fabric Data Agent: `aidatadataagent＜No＞`（公開済み）
2. Foundry を利用するアカウントに、対象サブスクリプション/リソースグループへの作成権限があることを確認する。
3. Teams にアプリを公開できる権限（または管理者承認フロー）があることを確認する。

### Foundry リソースを作成し、モデルをデプロイする

1. Azure portal で Foundry リソースを作成する（既存がある場合は再利用可）。
2. 基本情報で次を設定する。
   - リソース グループ: Day1 と同一または運営指定
   - 名前: `foundry-aidata-＜unique＞`
   - リージョン: Day1 と同一リージョン推奨
3. [確認と作成] -> [作成] を実行する。

![day2 foundry resource 01](images/day2-foundry-resource-01.png)

4. 作成後、Foundry ポータルを開き、対象プロジェクトに移動する。
5. [ビルド] -> [モデル] から `gpt-5.4-mini` を検索し、既定設定でデプロイする。

![day2 deploy model gpt54mini 01](images/day2-deploy-model-gpt54mini-01.png)

### Agent を作成する

1. Foundry プロジェクトで [エージェント] -> [エージェントの作成] を選択する。
2. 次の内容で Agent を作成する。
   - エージェント名: `aidata-agent-app＜No＞`（任意）
   - モデル: `gpt-5.4-mini`

![day2 create agent 01](images/day2-create-agent-01.png)

3. Instructions に次を設定する。

```text
あなたは Fabric Data Agent を利用して、業務データに基づく回答を返すアシスタントである。
回答には対象期間と集計観点を明示し、可能な限り根拠を示すこと。
```

![day2 agent instructions 01](images/day2-agent-instructions-01.png)

### Fabric Data Agent を接続する

1. 作成した Agent の [Tools]（または [接続]）を開く。
2. `Microsoft Fabric` / `Fabric Data Agent` 系コネクタを追加する。
3. Day1 で作成した Data Agent を選択して接続する。
   - ワークスペース: `aidataws＜No＞`
   - Data Agent: `aidatadataagent＜No＞`
4. 接続後、Agent を保存する。

![day2 connect fabric data agent 01](images/day2-connect-fabric-data-agent-01.png)
![day2 connect fabric data agent 02](images/day2-connect-fabric-data-agent-02.png)

### Agent を公開し、エンドポイントを確認する

1. Agent の [Deploy]（または [公開]）を実行する。
2. 公開後、以下を控える。
   - Project endpoint
   - Agent ID（または Deployment 名）
3. 認証方式（推奨: Microsoft Entra ID）を確認する。

![day2 publish agent endpoint 01](images/day2-publish-agent-endpoint-01.png)

### Teams へ公開する

1. Agent のチャネル連携で [Microsoft Teams] を選択する。
2. 次の公開情報を設定して公開する。
   - アプリ表示名
   - 説明
   - アイコン（任意）
3. 公開後、配布リンクまたは Teams アプリパッケージを取得する。
4. 管理者承認が必要な環境では、管理者に承認依頼を行う。

![day2 publish teams channel 01](images/day2-publish-teams-channel-01.png)

### Playground と Teams で動作確認する

1. Foundry Playground で次を実行し、Fabric Data Agent 経由で回答されることを確認する。
   - `2023年と2024年で、EmployeeごとのProfit合計を比較して。`

![day2 playground test 01](images/day2-playground-test-01.png)

2. Teams で対象アプリをインストールする。

![day2 teams install app 01](images/day2-teams-install-app-01.png)

3. Teams の 1:1 チャットまたはチャネルで同じ問い合わせを実行する。
4. Playground と同じ傾向の結果（期間、増減、根拠）が返ることを確認する。

![day2 teams chat test 01](images/day2-teams-chat-test-01.png)

### 補足

- 本ハンズオンの Day2 は、Foundry から Fabric Data Agent を利用する流れに限定する。
- 旧手順書の `TodoManagement` / `Azure Cosmos DB` / `MCP Toolkit` 系手順は対象外。
- ポータル UI 名称が異なる場合は、`Agent` / `Tools` / `Fabric Data Agent` / `Teams` のキーワードで同等機能を探す。

以上、お疲れさまでした。