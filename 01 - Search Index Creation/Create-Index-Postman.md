# Postmanを使用した検索インデックスの作成

このフォルダーには、プログラムで検索インデックスを作成するために使用できるPostmanコレクションと環境が含まれています。Postmanを使用すると、API呼び出しをパラメーター化し、API呼び出しを編集してパイプラインをカスタマイズし、変更を簡単に共有および保持できます。

このコレクションは、すぐに使えるCognitive Search機能の大部分を利用できるように事前構成されています。

このコレクションを使用して初期インデックスを作成し、その後、Postmanコレクションを編集し、必要に応じてカスタムスキルを追加して反復することをお勧めします。このコレクションは、Azure Blobストレージがプライマリデータソースであることを前提としています。別のデータソースを使用する場合に必要な変更点については、この[ドキュメント](https://docs.microsoft.com/rest/api/searchservice/create-data-source)を参照してください。

## 前提条件

1. Postmanがお使いのオペレーティングシステムにインストールされていること
2. 初期ドキュメントセットがAzure Blobストレージコンテナーにアップロードされていること

## セットアップ手順

1. **Postmanのインストール** - https://www.getpostman.com/apps に移動して、対象オペレーティングシステムのPostmanをダウンロードしてインストールします（まだインストールしていない場合）。
2. **ファイルをインポートする** - Postman UIを使用して、*Cognitive Search Pipeline APIs.postman_collection.json* および *Cognitive Search Pipeline Environment.postman_environment.json* をインポートします。

    ![import files](../images/postman_import_files.png)

3. **環境変数を更新する** - 以下で強調表示されているドロップダウンを使用して環境を設定し、**目のボタン**を選択してから編集を選択して環境変数を更新します。これらの変数は、API呼び出しをパラメーター化し、呼び出しごとに変数を編集する必要がないようにするために使用されます。

    ![edit environment variables](../images/postman_edit_environment_variables.png)

    以下の変数が必要です。
    * **search_service** - Azure Searchサービスリソースの名前
    * **index_name** - 検索インデックスの名前。これはあなたが設定したいものにできます。
    * **env_search_api_key** - Azure SearchサービスのAPIキー。これはAzureポータルで見つけることができます。
    * **env_storage_connection_string** - blobストレージアカウントの接続文字列。これはAzureポータルで見つけることができます。
    * **env_storage_container** - ドキュメントが含まれているblobコンテナーの名前
    * **cog_services_key** - Cognitive Servicesのキー。これはAzureポータルで見つけることができます。

4. **API呼び出しを実行する**

   Cognitive Searchインデックスを作成するには、次のAPI呼び出しを順番に実行します。

    1. Create a datasource
    2. Create index
    3. Create a skillset
    4. Create indexer

    次に、**インデクサーのステータス**をチェックして、ドキュメントが処理中かどうか、またはエラーがないかどうかを確認できます。インデクサーが自動的に実行されない場合は、手動でインデクサーを実行できます。

## 追加のリソース

Postmanでの作業の詳細については、Postman Webサイトの[ドキュメント](https://learning.postman.com/docs/postman/launching-postman/sending-the-first-request/)を参照してください。