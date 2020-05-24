# モジュール2：デモのフロントエンドで結果を視覚化する
検索インデックスが作成できたので、少し時間をかけて、最初の検索結果を視覚化できるようにする簡単なWebページを作成して展開します。ラボ全体でこのビューに戻り、機能を追加したときに検索結果がどのように変化するかについて、特に注意します。

この演習を開始するために、[ナレッジマイニングソリューションアクセラレーター](https://github.com/nohanaga/azure-search-knowledge-mining) を使用し、これに慣れてから、フロントエンドエクスペリエンスを作成します。このアクセラレーターは、最小限の実用的なナレッジマイニングソリューションを構築するために必要なすべての手順とツールを開発者に提供するために公開されました。少し時間をとって、以下のモジュールが含まれていることに注意してください。必要なAzureリソースをデプロイする。カスタムスキルを構築します。シンプルでありながらエレガントなフロントエンドで結果を提示します。このラボが終了すると、結果は次のようになります。

![](images/intresults.png)

## 1. リポジトリをクローンする
```
git clone https://github.com/nohanaga/azure-search-knowledge-mining.git
```

## 2. プロジェクトを開始する

**CognitiveSearch.UI.csproj** (02-Web UI Template\CognitiveSearch.UI) を Visual Studio で開きます。

## 3. appsettings.json を更新する

*appsettings.json* ファイルの次のフィールドを更新して、Webアプリをストレージアカウント、検索インデックス、Application Insights アカウントに接続します。

```json
  "SearchServiceName": "Your Search Service Name",
  "SearchApiKey": "Your Search Service key",
  "SearchIndexName": "clinical-trials-small",
  "InstrumentationKey": "",
  "StorageAccountName": "Your storage Account Name",
  "StorageAccountKey": "Your Storage Account Key",
  "StorageContainerAddress": "Your Storage Container Address",
  "KeyField": "metadata_storage_path",
  "IsPathBase64Encoded": true,
  "GraphFacet": "diseases"
```
 
### Notes
1. **SearchServiceName** 検索サービスの名前に設定する必要があります。(例："myservice")
1. **SearchApiKey** 検索サービスのキーにする必要があります。(例："B8365AC95521089B7E3FA4CC98435")
1. **SearchIndexName** インデックスの名前に設定する必要があります。 (例："clinical-trials-small")
1. **StorageAccountName** ストレージアカウントの名前に設定する必要があります。(例："mystorageaccount")
1. **StorageAccountKey** ストレージアカウントのアクセスキーにする必要があります。(例："xatMbKSaPcsII36SyZgmXAsY0kWCo/waIo55i8MsMYgLEbMDY1PEBn+c9O6WnEEDhfOGJvYKAsjspvZDgRLGzr==")
1. **StorageContainerAddress** 次の形式にする必要があります: *"https://*storageaccountname*.blob.core.windows.net/*containername*"*
1. **InstrumentationKey** はオプションのフィールドです。インストルメンテーションキーは、Power BIレポートにデータを入力するために、Webアプリを Application Insights に接続します。
1. **KeyField** インデックスでキードキュメントIDとして指定されたフィールドに設定する必要があります。(例："metadata_storage_path")
1. 場合によっては、metadata_storage_pathがキーとなり、base64でエンコードされます。その場合、**IsPathBase64Encoded** をtrueに設定します。
1. **GraphFacet** はリレーションシップグラフの生成に使用され、使用するファセットの名前(例："diseases")に設定します。ノードグラフを使用しない場合は、空白のままにします。

###
**重要:**
このチュートリアルは、結果を表示してコードを調査できるように効率を最適化していますが、コードに資格情報を入力することは、実行するのに適した方法ではないことに注意してください。これを行うには、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) などのサービスを使用することをお勧めします。

## 3. **スタートアップ アイテム**として指定
 
 ![](images/setstart.png)
 
## 4. プロジェクトを実行して結果を確認する
 
![](images/intresults.png)

## 5. コードを調査する

UIの多くは JavaScript によって動的にレンダリングされます。UIに変更を加えるときに知っておくべき重要なファイルは次のとおりです。

1. **wwroot/js/results.js** - UIに検索結果をレンダリングするために使用されるコードが含まれています

2. **wwroot/js/details.js** - 結果が選択されたときに詳細ビューをレンダリングするためのコードが含まれています

### 次：[モジュール3：Azure Functions とカスタムスキルの概要](Module&#32;3.md)

