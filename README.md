---
page_type: sample
languages:
- csharp
- javascript
- html
- powershell
products:
- azure
description: "Welcome to the Knowledge Mining Solution Accelerator!"
urlFragment: azure-search-knowledge-mining
---

![ナレッジマイニング ソリューションアクセラレーター](images/kmheader.png)

# ナレッジマイニング ソリューションアクセラレーター

## このレポジトリについて

ナレッジマイニング ソリューションアクセラレーターへようこそ！ このアクセラレーターは、[Azure Cognitive Search](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) を使用して初期ナレッジマイニングプロトタイプをすばやく構築するために必要なすべてのリソースを開発者に提供します。このアクセラレーターを使用して、独自のデータで開発作業を開始したり、学習ツールとして使用して、Cognitive Search を使用してビジネスの固有のニーズを満たす方法をよりよく理解できます。

このリポジトリでは、適切な Azure リソースをデプロイするためのテンプレート、最初の検索インデックスを作成するためのアセット、カスタムスキルを使用するためのテンプレート、基本的な Web など、Cognitive Search ソリューションをすばやく作成するために必要なすべての作成物が提供されています アプリ、および PowerBI レポートで検索ソリューションのパフォーマンスを監視します。ドキュメント全体にベストプラクティスを盛り込み、ガイドを提供します。Cognitive Search を使用すると、デジタルデータ（ドキュメントやテキストファイルなど）とアナログデータ（画像やスキャンしたドキュメントなど）の両方に簡単にインデックスを付けることができます。

> Note: このガイドでは、Cognitive Search のAIエンリッチメント機能を使用します。AIエンリッチメントを使用すると、さまざまな種類のデータ（ドキュメント、テキストファイル、画像、スキャンしたドキュメントなど）を取り込み、それらのコンテンツを抽出し、エンリッチおよび変換してから、検索目的でインデックスを作成できます。この機能の詳細については、[AI in Cognitive Search](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)ドキュメントを参照してください。

完了すると、データを検索するための Web アプリが準備できます。

![A web app showing several resources and their lists of searchable tags](images/ui.PNG)

## 前提条件

ソリューションを正常に完了するには、次のリソースにアクセスしてプロビジョニングする必要があります。

* Azure サブスクリプション - [無料で作成](https://azure.microsoft.com/free/)
* [Visual Studio 2017 以降](https://visualstudio.microsoft.com/downloads/)
* [Postman](https://www.getpostman.com/) API コール作成用
* Azure Search Indexers でサポートされている任意のデータソースにアップロードされたドキュメント。 これらの一覧については、[Azure Cognitive Search のインデクサー](https://docs.microsoft.com/azure/search/search-indexer-overview)を参照してください。このソリューションアクセラレーターは、Azure Blob Storage をソースデータファイルのコンテナーとして使用します。**sample_documents/** フォルダにサンプルドキュメントがあります。

このガイドで提供される指示は、Azure ポータル、Azure Functions、Azure Cognitive Search、Visual Studio および Postman の基本的な実用知識があることを前提としています。追加のトレーニングとサポートについては、以下を参照してください。

* [Knowledge Mining Bootcamp](https://github.com/Azure/LearnAI-KnowledgeMiningBootcamp)
* [AI in Cognitive Search documentation](https://docs.microsoft.com/azure/search/cognitive-search-resources-documentation)

## プロセスの概要

このリポジトリを複製またはダウンロードしてから、各 README ファイルで概説されている手順に従って、これらの各フォルダーを順番に移動します。すべての手順を完了すると、データソースとデータエンリッチメントスキルを組み合わせた実用的なエンドツーエンドのソリューション、Azure Cognitive Search を利用した Web アプリ、ユーザーの検索アクティビティに関するインテリジェントなレポートが完成します。

![the cognitive indexing pipelines used for processing unstructured data in Azure Search](images/architecture.jpg)

### [00 - リソースのデプロイ](./00%20-%20Resource%20Deployment)
このフォルダーの内容は、必要なリソースを Azure サブスクリプションにデプロイする方法を示しています。 これは、[Azure portal](https://portal.azure.com) または提供されている [PowerShell script](./00%20-%20Resource%20Deployment/deploy.ps1) を使用して実行できます。

または、このボタンを使用して、必要なリソースを自動的にデプロイすることもできます。

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-search-knowledge-mining%2Fmaster%2Fazuredeploy.json)

### [01 - 検索インデックスの作成](./01%20-%20Search%20Index%20Creation)
このフォルダーには、検索インデックスの作成に使用できる Postman コレクションが含まれています。コレクションは、すぐに使用できる Cognitive Search 機能を利用するように事前構成されています。

このコレクションを使用して初期検索インデックスを作成してから、Postman コレクションを編集し、必要に応じてカスタムスキルを追加して反復することをお勧めします。

### [02 - Web UI テンプレート](./02%20-%20Web%20UI%20Template)
このフォルダーには、.NET Core で記述された基本的な Web UI テンプレートが含まれており、検索インデックスを照会するように構成できます。[Web UI テンプレート README ファイル](./02%20-%20Web%20UI%20Template/README.md)で説明されている手順に従って、新しい検索インデックスを Web アプリに統合します。

### [03 - データサイエンスとカスタムスキル](./03%20-%20Data%20Science%20and%20Custom%20Skills)
このフォルダーには、独自のカスタムスキルをソリューションに追加するための例とテンプレートが含まれています。これらのカスタムスキルは、ソリューションを特定のユースケースのニーズに合わせるのに役立ちます。この手順は完全にオプションであり、不要な場合はスキップできます。

カスタムスキル開発のその他のサンプルと情報については、[カスタムスキルのドキュメント](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface)を参照してください。.NET Azure Functions のカスタムスキルは、[パワースキルリポジトリ](https://github.com/nohanaga/azure-search-power-skills)に移動しました。

#### [03.1 - 日本語OCR向けカスタムスキル](https://github.com/nohanaga/azure-search-japanese-ocr-tips/)
 このセクションでは、Azure Cognitive Search で日本語 OCR スキルを実行した場合に生じる文字分割問題を解決するカスタムスキルが含まれています。

### [04 - レポーティング](./04%20-%20Reporting)
このフォルダーには、ソリューションを監視し、ユーザーの検索動作を理解するために使用できるビルド済みの PowerBI レポートが含まれています。これらは、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) を通じてキャプチャされたデータを活用し、特定のビジネス目標に合わせて変更できます。この手順は完全にオプションであり、不要な場合はスキップできます。

### [サンプルドキュメント](./sample_documents)
このフォルダーには、さまざまなファイル形式の小さなデータセットが含まれており、別のデータセットがない場合にソリューションを構築するために使用できます。

### [ワークショップ](./workshops)
1日で Azure Cognitive Search のエキスパートになりましょう！
このフォルダには、あなたが知る必要のあるすべてを教えるセルフペースのワークショップが含まれています。Azure に精通しているほとんどの開発者は、以下のモジュールの大部分を8時間で完了することができます。

+ [Module 0 - 前提条件](./workshops/Module%200.md) (*先に進む前に完了する必要があります！*)
+ [Module 1 - Azure Portalを使用して検索インデックスとナレッジストアを構築する](./workshops/Module%201.md)
+ [Module 2 - デモのフロントエンドで結果を視覚化する](./workshops/Module%202.md)
+ [Module 3 - カスタムスキルとAzure Functionsの概要](./workshops/Module%203.md)
+ [Module 4 - オブジェクトモデルの学習](./workshops/Module%204.md)
+ [Module 5 - 高度なAzure Cognitive Search：アナライザーとスコアリングプロファイル](./workshops/Module%205.md)
+ [Module 6 - PowerBIを使用したデータの分析](./workshops/Module%206.md)
+ [Module 7 - Azure Cognitive Searchを使用して構造化データのインデックスを作成する](./workshops/Module%207.md) (オプション)


## License

Please refer to [LICENSE](./LICENSE.md) for all licensing information.
