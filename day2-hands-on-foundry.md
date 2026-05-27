# Fabric AI Ready Data Day2 ハンズオン手順

## 目次

- [Fabric AI Ready Data Day2 ハンズオン手順](#fabric-ai-ready-data-day2-ハンズオン手順)
  - [目次](#目次)
    - [Day2 のゴール](#day2-のゴール)
    - [事前確認](#事前確認)
    - [Foundry リソースを作成し、モデルをデプロイする](#foundry-リソースを作成しモデルをデプロイする)
    - [Agent を作成する](#agent-を作成する)
    - [Agent を公開する](#agent-を公開する)
    - [Teams で動作確認する](#teams-で動作確認する)

### Day2 のゴール

- Foundry 上で `gpt-5.4-mini` を利用する Agent を作成する。
- Day1 で作成した Fabric Data Agent を Foundry Agent から呼び出せるようにする。
- Agent を Teams に公開し、Teams から問い合わせできるようにする。


### 事前確認

1. Day1 の完了を確認する。
   - ワークスペース: `aidataws＜No＞`
   - セマンティックモデル: `aidatasemantic＜No＞`
   - Fabric Data Agent: `aidatadataagent＜No＞`（公開済み）
2. Foundry を利用するアカウントに、対象サブスクリプション/リソースグループへの作成権限があることを確認する。
3. Teams にアプリを公開できる権限（または管理者承認フロー）があることを確認する。

### Foundry リソースを作成し、モデルをデプロイする

Azure portal で Foundry リソースを作成する（既存がある場合は再利用可）。
1. `https://portal.azure.com` を開いてサインインします。
2. **Microsoft Foundry** を検索し、 **Foundry** → **+ 作成** を選択します。
3. **基本情報**で次を設定する。
   - **リソース グループ**: Day1 と同一または運営指定
   - **名前**: `foundry-aidata-＜unique＞`
   - **リージョン**: Day1 と同一リージョン推奨
  ![Crete foundry basics](image/day2-hands-on-foundry/create-foundry-basics.png)
4. **ストレージ**で次を設定する。
   - **Key Vault**: **Azure Key Vault の選択** →　**追加**
   - **Application Insights**: **Application Insights の選択** → **追加**
  ![Crete foundry storage](image/day2-hands-on-foundry/create-foundry-storage.png)
1. [確認と作成] -> [作成] を実行する。
![create foundry ](image/day2-hands-on-foundry/create-foundry-create.png)
デプロイ完了後:
1. Foundry リソースを開き、**Foundry ポータルに移動**、対象プロジェクトに移動する。
2. **ビルド** → **モデル** → **基本モデルをデプロイする** を開き、`gpt-5.4-mini` を検索する。
3. `gpt-5.4-mini` を選択し、**デプロイ** → **既定の設定** を選びます。

![deploy model gpt54mini 01](image/day2-hands-on-foundry/deploy-gpt-model.png)

### Agent を作成する

1. Foundry プロジェクトを開き、**エージェント** → **エージェントの作成** を選択し、**エージェント名**を `aidata-agent-app＜No＞` にして作成する。
2. 次の情報を設定します。
   - **モデル**: `gpt-5.4-mini`
   - **手順**: 次を設定する。

```text
あなたは Fabric Data Agent を利用して、業務データに基づく回答を返すアシスタントである。
回答には対象期間と集計観点を明示し、可能な限り根拠を示すこと。
```

![create agent](image/day2-hands-on-foundry/create-agent.png)

3. **ツール**:
   1. **Web search** ツールを削除します。
   2. **Fabric Data Agent** ツールを追加します。
      a. **追加** → **すべてのツールを参照する** を選択
      ![すべてのツールを参照](image/day2-hands-on-foundry/agent-add-tool.png)
      b. **Fabric Data Agent** を選択し、**ツールを追加**
      ![Fabric Data Agent ツールの選択](image/day2-hands-on-foundry/agent-select-fabric-data-agent.png)
      c. **Fabric Data Agent に接続する**
      - **ワークスペース ID**: `<workspace_id>`
      - **アーティファクト ID**: `<artifact_id>`
      > Fabric データ エージェントの URL から artifact_id の値をコピーします。URL のパスは、.../groups/<workspace_id>/aiskills/<artifact_id>... のようになっています。値は GUID です。
      ![ワークスペース ID と　アーティファクト ID](image/day2-hands-on-foundry/workspace-and-artiface-id.png)
      d. **接続** を選択します。
      ![Fabric Data Agent 接続](image/day2-hands-on-foundry/connect-fabric-data-agent.png)

4. **メモリ** → **作成** → **メモリ ストアを作成する** を選択します。
5. Agent を保存する。
6. Agent をテストする。
   **プレイグラウンド** に次のメッセージを入力します。ツール呼び出しの承認を求められたら許可します。
   `Emploee 毎のProfitの合計を年毎に出して`
   ![Chat result response](image/day2-hands-on-foundry/agent-chat-response.png)

### Web検索機能を追加する

1. **ツール**:
   1. **Web search** ツールを追加します
   ![web検索ツール追加](image/day2-hands-on-foundry/add-web-tool.png)
   2. Web Search ツールの追加画面で ** Bing 検索で Web を検索する** がチェックされていることを確認して追加
   ![web検索ツール追加](image/day2-hands-on-foundry/web-tool-config.png)
2. **手順**に下記の一文を追加
```text
業務データが見当たらない情報はWeb検索を利用して回答しなさい。
```
3. Agent をテストする。
   **プレイグラウンド** に次のメッセージを入力します。ツール呼び出しの承認を求められたら許可します。
   `2016年に最も売り上げが大きなCityを特定して、また、そのCityの人口増加傾向を調べて？`
   ![統合された回答](image/day2-hands-on-foundry/agent-unified-answer.png)

### Agent を公開する

1. Agentで、**発行する**  → **Teams と Microsoft365に対して発行する**
2. **Teams と Microsoft 365 に対して発行する**にて以下の値を指定する。
   - **短い説明**: `aidata ハンズオンで作成した Foundry Agent`
   - **説明**: `aidata ハンズオンで作成した Foundry Agent`
   - **開発者**: <your name>
  ![publish agent](image/day2-hands-on-foundry/publish-agent.png)
3. **発行オプション** にて以下の値を指定し、→ **発行**。
   - **直接発行**
   - **組織内のユーザー**
   ![publish agent options](image/day2-hands-on-foundry/publish-agent-options.png)
4. 発行が成功したら、[Microsoft 365 管理センター](https://admin.cloud.microsoft/?#/agents/all/requested) を開く。
   ![publish agent ](image/day2-hands-on-foundry/publish-agent-finished.png)
5. 発行されたAgent `foundry-aidata-＜unique＞`を選択し、**Publish to store**をクリックする。
   ![publish agent ](image/day2-hands-on-foundry/publish-agent-to-store.png)

6. Agentを利用できるユーザーを選択し、次へ。
    ![publish agent ](image/day2-hands-on-foundry/publish-agent-select-users.png)
7. 既定のテンプレートを選択し、次へ。
   ![publish agent ](image/day2-hands-on-foundry/publish-agent-select-template.png)
8. 次へ。
9. 発行。
   ![publish agent ](image/day2-hands-on-foundry/publish-agent-publish.png)

### Teams で動作確認する

1. Teams を開いて、**Copilot** → **All Agents**, `aidata-agent-app＜No＞`で検索し、選択する。
2. **Add** をクリックする。
3. 開いたChat　Windowsにて、以下の問い合わせを実施し、回答されることを確認する。
   `2013年と2014年で、EmployeeごとのProfit合計を比較して。`
   >　必要に応じて、ログインを実行する。

  ![chat response example](image/day2-hands-on-foundry/teams-chat-reposnse-example.png)


以上、お疲れさまでした。