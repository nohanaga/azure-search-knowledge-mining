# Cognitive Search UI テンプレート
このフォルダーには、検索結果のビューをすばやく作成するために使用できる基本的なWebフロントエンドが含まれています。いくつかの簡単な手順で、このテンプレートUIを構成して、新しく作成した検索インデックスをクエリできます。

Cognitive Searchテンプレートには、2つのプロジェクトが含まれています。

1. **CognitiveSearch.UI** - これは、検索インデックスをクエリするためのテンプレートUIとして使用される .NET Core MVC Webアプリです。これが本READMEの焦点です。
2. **CognitiveSearch.Skills** - これは、C＃カスタムスキルの作成に使用されるAzure Functionプロジェクトです。カスタムスキルの詳細については、**03 - データサイエンスとカスタムスキル**のドキュメントを参照してください。

ほんの数ステップで、このテンプレートUIを構成して、検索インデックスをクエリできます。このテンプレートは、次のようなWebページをレンダリングします。

![web user interface](../images/WebUI.jpg)

## 前提条件

1. Visual Studio 2017以降

## 1. appsettings.jsonを更新する

Azureサービスに接続するようにWebアプリを構成するには、*appsettings.json*ファイルを更新するだけです。

このファイルには、以下で説明する必須フィールドとオプションフィールドがあります。

### 必須フィールド

```json
  // Required fields
  "SearchServiceName": "",
  "SearchApiKey": "",
  "SearchIndexName": "",
  "SearchIndexerName": "",
  "StorageAccountName": "",
  "StorageAccountKey": "",
  "StorageContainerAddress": "https://{storage-account-name}.blob.core.windows.net/{container-name}",
  "KeyField": "metadata_storage_path",
  "IsPathBase64Encoded": true,
```

1. **SearchServiceName** - Azure Cognitive Searchサービスの名前
2. **SearchApiKey** - Azure Cognitive SearchサービスのAPIキー
3. **SearchIndexName** - Azure Cognitive Searchインデックスの名前
4. **SearchIndexerName** - Azure Cognitive Searchインデクサーの名前
5. **StorageAccountName** - Azure Blob Storageアカウントの名前
6. **StorageAccountKey** - Azure Blob Storageアカウントのキー
7. **StorageContainerAddress** - ドキュメントが保存されているストレージコンテナーへのURL。これは次の形式である必要があります：*https://*storageaccountname*.blob.core.windows.net/*containername**
8. **KeyField** - 検索インデックスのキーフィールドです。これは、インデックスでキードキュメントIDとして指定されたフィールドに設定する必要があります。デフォルトでは、これは*metadata_storage_path*です。
9. **IsPathBase64Encoded** - デフォルトでは、metadata_storage_pathがキーであり、base64でエンコードされるため、デフォルトでtrueに設定されています。キーがエンコードされていない場合は、これをfalseに設定します。

### オプションのフィールド

一部のフィールドはオプションですが、考えられるエラーを回避するために *appsettings.json* から削除しないことをお勧めします。

```json
  // オプションのインストルメンテーションキー
  "InstrumentationKey": "",

  // 複数のインデクサーを使用する場合のオプションのコンテナーアドレス：
  "StorageContainerAddress2": "https://{storage-account-name}.blob.core.windows.net/{container-name}",
  "StorageContainerAddress3": "https://{storage-account-name}.blob.core.windows.net/{container-name}",

  // マップにgeoLocationフィールドを表示する場合は、Azure Mapsアカウントへのオプションのキー
  "AzureMapsSubscriptionKey": "",

  // グラフとして表現するファセットテーブルフィールドの名前に設定します。
  // グラフ上に複数のファセットタイプが必要な場合は、ファセット名のコンマ区切りのリストを設定することもできます。
  "GraphFacet": "keyPhrases, locations",

  // 追加のカスタマイズ
  "Customizable": "true",
  "OrganizationName": "Microsoft",
  "OrganizationLogo": "~/images/logo.png",
  "OrganizationWebSiteUrl": "https://www.microsoft.com"

```

1. **InstrumentationKey** - Application Insightsのオプションのインストルメンテーションキー。インストルメンテーションキーは、WebアプリをApplication Insightsに接続して、Power BIレポートにデータを入力します。
2. **StorageContainerAddress2** & **StorageContainerAddress3** - 複数のインデクサーを使用する場合のオプションのコンテナーアドレス
3. **AzureMapsSubscriptionKey** - ドキュメントの詳細の地図に地理的位置を表示する場合は、Azure Mapsアカウントを提供するオプションがあります。コードは、タイプ Edm.GeographyPoint の *geolocation* というフィールドを想定しています。この動作を変更したい場合（たとえば、別のフィールドを使用したい場合）、details.jsを変更できます。

![geolocation](../images/geolocation.png)

4. **GraphFacet** - GraphFacetは、関係グラフの生成に使用されます。これは、UIで編集できるようになりました。
5. **Customizable** - ユーザーがWebアプリを「カスタマイズ」できるかどうかを決定します。カスタマイズには、ドキュメントのアップロード、Webアプリの色/ロゴの変更が含まれます。**OrganizationName**、**OrganizationLogo**、および**OrganizationWebSiteUrl**は、簡単なカスタマイズを可能にする追加のフィールドです。

## 2. SearchModel.csを更新する

この時点で、Webアプリが構成され、実行する準備ができています。デフォルトでは、すべてのファセット、タグ、およびフィールドがUIで使用されます。

UIをさらにカスタマイズする場合は、*Search\SearchModel.cs*の次のフィールドを更新できます。ファセットできるフィルター、結果とともに表示されるタグ、および検索で返されるフィールドを選択できます。

![searchmodel](../images/SearchModel.png)

**Facets** - UIで選択可能なフィルターとして表示されるファセットテーブルフィールドを定義します。デフォルトでは、すべてのfacetableフィールドが含まれています。

**Tags** - 結果カードと詳細ビューにボタンとして追加されるフィールドを定義します。デフォルトでは、すべてのfacetableフィールドが含まれています。

**ResultFields** - 結果ビューで返されるフィールドを定義します。ここでは、UIに使用されるフィールドのみを含めて、大きなドキュメントによる遅延を減らす必要があります。デフォルトでは、すべてのフィールドが含まれています。

## 3. さらなるカスタマイズを追加する

このテンプレートは、Cognitive Searchソリューションの優れたベースラインとして機能しますが、ユースケースによっては、追加の更新を行うことができます。

*translated_text*というフィールドがある場合、特別な動作があります。UIは、元のテキストと翻訳されたテキストをUIに自動的に表示します。これは便利です。この動作を変更する（無効にする、またはフィールドの名前を変更する）場合は、details.js（GetTranscriptHTMLメソッド）で変更できます。

![geolocation](../images/translated.png)

### 主要ファイル

UIの多くはJavaScriptによって動的にレンダリングされます。UIに変更を加えるときに知っておくべき重要なファイルは次のとおりです。

1. **wwroot/js/results.js** - UIに検索結果をレンダリングするために使用されるコードが含まれています

2. **wwroot/js/details.js** - 結果が選択されたときに詳細ビューをレンダリングするためのコードが含まれています

3. **Search/DocumentSearchClient.cs** - Azure Cognitive SearchのAPIと通信するためのコードが含まれています。このファイルにブレークポイントを設定することは、デバッグに最適な方法です。
