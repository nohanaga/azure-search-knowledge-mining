# リソースのデプロイ

このソリューションアクセラレーターのリソースをAzureにデプロイするには、2通りの方法があります。

1. **PowerShellスクリプトを使用する**: `deploy.ps1`

    このスクリプトは、ソリューションを起動して実行する最速の方法であり、次のアクションを実行します。

    1. 必要なAzureリソースをプロビジョニングする
    2. サンプルデータをストレージアカウントにアップロードする
    3. 検索インデックスを作成する
    4. Webアプリの *appsettings.json* に必要な値とキーを出力します

    このスクリプトを実行する場合は、2で説明している検索インデックスの作成をスキップできます。

2. **ARMテンプレートを使用する**: `azuredeploy.json`

    このARMテンプレートをデプロイするには、下のボタンを押すだけです。

    > これはリソースのみをデプロイすることに注意してください。 次のステップで検索インデックスを作成する必要があります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fnohanaga%2Fazure-search-knowledge-mining%2Fmaster%2Fazuredeploy.json" target="_blank">
        <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

## 前提条件

1. Azureサブスクリプションへのアクセス

## PowerShellスクリプトの実行

> PowerShellを初めて使用する場合は、[Windows 10でPowerShellスクリプトファイルを実行する方法](https://www.windowscentral.com/how-create-and-run-your-first-powershell-script-file-windows-10)を使用して作業を開始できます。

[PowerShell script](./deploy.ps1)を実行するには、

1. PowerShellを開き、カレントフォルダーに移動します。
2. 次のコマンドを実行します。

    ```cmd
        ./deploy.ps1
    ```

3. スクリプトを実行すると、ログインして追加情報を提供するように求められます。

## デプロイされたリソース

PowerShellスクリプトは、Azureサブスクリプションに次のリソースをプロビジョニングします。

| リソース              | 用途                                                                                     |
|-----------------------|-------------------------------------------------------------------------------------------|
| [Azure Cognitive Search](https://azure.microsoft.com/services/search/)  | 検索インデックス、Cognitiveスキルセット、および検索インデクサーのホスティングサービス          |
| [Azure Cognitive Services](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)	| Cognitive Skillsパイプラインが非構造化データを処理するために使用します	|
|[Azure Storage Account](https://azure.microsoft.com/services/storage/?v=18.24) | 検索対象のファイルが保存されているデータソース                                                     |
| [Web App](https://azure.microsoft.com/services/app-service/web/)               | 検索UIのホスティングサービス                                                     |
| [Application Insights](https://azure.microsoft.com/services/monitor/)  | 検索UIのテレメトリ監視サービス (*オプション*)									|

デフォルトでは、このPowerShellスクリプトはソリューションの基本検索サービスをプロビジョニングします。サイズ制限、スケーリング制限、価格については、[Azure Cognitive Searchの価格](https://azure.microsoft.com/pricing/details/search/)を参照し、希望の階層を選択してください。

カスタムスキル開発のニーズによっては、追加のAzureリソースが必要になる場合があります。詳細については、[03 - Data Science & Custom Skills](../03%20-%20Data%20Science%20and%20Custom%20Skills/README.md)フォルダーのREADMEを参照してください。

## 注釈

最終的な検索インデックスのサイズを推定するために、データの代表的なサブセットを活用して初期プロトタイプソリューションを構築することをお勧めします。最終的なソリューションを構築する準備ができたら、見積もりの規模とパフォーマンスのニーズを満たすようにリソースのサイズを設定してプロビジョニングする必要があります。

サイジングに関する追加情報とベストプラクティスについては、[Azureサービスの制限](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)をご覧ください。