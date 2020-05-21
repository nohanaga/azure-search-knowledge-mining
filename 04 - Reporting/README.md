# レポーティング

PowerBIテンプレートファイルが作成されたため、Cognitive SearchテンプレートUIに基づいてレポートをすばやく起動できます。レポートを作成するには、PowerBI Desktopを使用して **Cognitive Search.pbit** を開くだけです。

## 前提条件

1. Application Insightsは、これらのレポートのテレメトリデータをキャプチャするために使用されます。Application Insightsをプロビジョニングせず、Webアプリの *appsettings.json* ファイルの *InstrumentationKey* を更新することを選択した場合、このリポジトリでレポートを実行するために使用できるデータがありません。
2. PowerBI Desktopがコンピューターにインストールされていること

## Getting Started

テンプレートを開くと、**Application InsightsアプリケーションID**の入力を求められます。 このアプリケーションIDは、Azureポータルに移動し、[App Insights]リソースに移動して、[APIアクセス]をクリックすると見つかります。

アプリケーションIDを入力したら、**Load**をクリックします。

<img src="../images/pbi1.png" alt="Application Id Load" width="800"/>

<!-- ![](../images/pbi1.png) -->


次に、資格情報を要求します。

<img src="../images/pbi2.png" alt="Credentials" width="800"/>

<!-- ![](../images/pbi2.png) -->


資格情報を入力すると、Power BIレポートを以下のように準備できます。

<img src="../images/pbi3.JPG" alt="PowerBi sample report" width="800"/>

<!-- ![](../images/pbi3.jpg) -->

## 追加資料
PowerBIの詳細とPowerBIでのレポートの開発については、[Power BIとは](https://docs.microsoft.com/power-bi/power-bi-overview)を参照してください。

## Special Thanks 
このPBIテンプレートを作成してくれた Emilio D'Angelo に特に感謝します。