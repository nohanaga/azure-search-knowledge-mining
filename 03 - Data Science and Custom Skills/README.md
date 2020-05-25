# データサイエンスとカスタムスキル
Cognitive Searchを使用すると、すぐに使える機能をカスタムスキルで拡張できます。この拡張性により、カスタムフィルターの作成、ドキュメントの分類、カスタムエンティティの抽出など、幅広い機能が可能になります。

このフォルダーには、独自のカスタムスキルを構築するために利用できるいくつかのサンプルテンプレートと、MLモデルを構築する場合に使用できるいくつかの便利なPythonノートブックが含まれています。

Cognitive Searchは、カスタムスキルの構築に使用するツールに依存せず、RESTFul APIとしてデプロイされます。カスタムスキルを構築するための唯一の要件は次のとおりです。

1. 安全なHTTPSエンドポイントを用意します。
2. [こちら](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface)に示されている定義済みの入力/出力スキーマに従います。

このリポジトリにあるコードは、シンプルなAzure Functionsを構築する場合でも、独自のカスタムMLモデルを構築する場合でも、これらのRESTful APIを正しい形式で構築およびデプロイするのに役立ちます。

## 前提条件
1. Azure Machine Learningを使用してソリューションのカスタムスキルを構築する場合は、追加のリソースをAzureサブスクリプションにデプロイする必要があります。詳細については、[AMLクイックスタート](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started)をご覧ください。

## カスタムスキルの種類
一般に、使用されるカスタムスキルには2つのタイプがあります。

### 1.0 Azure Functionsのカスタムスキル
Azure Functionsのデプロイは、カスタムスキルを作成する最も簡単な方法です。 [サンプルスキル](https://github.com/nohanaga/azure-search-power-skills)プロジェクトは、C#でのAzure Functionsのテンプレートといくつかの例を提供します。 Azure Functionsは、MLモデルを必要としないスキルをデプロイするための推奨アプローチです。

### 2.0 Azure Machine Learningのカスタムスキル
MLモデルを使用して、Cognitive Searchパイプラインを強化できます。このテンプレートでは、Azure Machine Learningを使用してモデルを構築およびデプロイします。AMLカスタムスキルテンプレートは、カスタムスキルとして使用されるモデルを迅速に展開するために必要なファイルを提供します。

## 役立つリンク
カスタムスキルの詳細については、製品のドキュメントを参照してください。
1. [カスタムスキルインターフェイスの定義](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface
)
2. [カスタムスキルの例](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example
)


