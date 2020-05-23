# モジュール5：高度な Azure Cognitive Search

このモジュールでは、2つの高度な検索トピックについて説明します。
* カスタムアナライザー
* スコアリングプロファイル

## カスタムアナライザー

前のモジュールでは、疾患と呼ばれるフィールドを含むインデックスを作成しました。これにより、このフィールドを検索できるだけでなく、特定の疾患が含まれているかどうかに基づいてファセット（ドキュメントの分類）とドキュメントのフィルタリングも可能になりました。病気の問題の1つは、綴りが難しいことが多いことです。珍しい病気「mucopolysaccharidosis」を取ります。人々がこれを検索するとき、彼らはそれをつづりの本当に悪い仕事をすることができると想像することができます。おそらく「mukopolisakaridosis」と入力するかもしれません。検索では[簡単なスペルミス](https://docs.microsoft.com/en-us/azure/search/query-lucene-syntax#bkmk_fuzzy
)を処理できますが、これは単なるスペルミスではありません。ユーザーの観点からすると、結果が0になるのは信じられないほどイライラします。幸いにも、カスタムアナライザーを使用してこれに対応できる方法があります。具体的には、作成するのは、検索エンジンが一致できるようにする「表音」[カスタムアナライザー](https://docs.microsoft.com/en-us/azure/search/index-add-custom-analyzers)です。音声的に似ている単語。これにより、ユーザーは「mukopolisakaridosis」と入力できますが、格納されている疾患が「mucopolysaccharidosis」である一致を見つけることができます。

表音検索が役立つ次のような数多くの例を想像してみてください。
* 人物名（たとえば、Cavanagh vs Kavanaugh）
* 音声文字変換の誤り（たとえば、SQL が sequel として文字変換される場合）

Azure Cognitive Search は、さまざまな方法で単語をエンコードするさまざまな音声エンコーダーを提供します。私たちが使用するものは doubleMetaphone と呼ばれ、これは mukopolisakaridosis と mucopolysaccharidosis の両方を MKPL のコードにエンコードします。これは MKPL としてインデックスに保存されるため、誰かが mukopolisakaridosis または mucopolysaccharidosis を検索すると、どちらも同じ値にエンコードされ、結果として一致します。

### インデックスに表音フィールドを追加する
前のモジュールで行ったようにインデックスを変更して、Phonetic Custom Analyzer を使用する DiseasesPhonetic という新しいフィールドを追加します。

Postman アプリケーションに戻り、最後に疾患フィールドでインデックスを更新した PUT リクエストを表示します。そこにない場合は、履歴リクエストで見つけることができます。

次のフィールドを追加して、PUT リクエストを変更します。
```json
"fields": [
   ...,
 {
	"name": "diseasesPhonetic",
	"type": "Collection(Edm.String)",
	"searchable": true,
	"filterable": true,
	"retrievable": true,
	"sortable": false,
	"facetable": true,
	"key": false,
	"indexAnalyzer": null,
	"searchAnalyzer": null,
	"analyzer": "my_phonetic",
	"synonymMaps": []
}...
]
```
フィールドが「my_phonetic」というアナライザーを使用していることに注意してください。これは、作成する必要があるカスタムアナライザになります。これを行うには、"analyzers": [ ]　を見つけて、次のものに置き換えます。

```json
  "analyzers": [
    {"name":"my_phonetic","@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer","tokenizer":"microsoft_language_tokenizer","tokenFilters": [ "lowercase", "asciifolding", "phonetic_token_filter" ]}
  ],

```
"tokenFilters"：[ ] を見つけて、次のものに置き換えます。
```json
"tokenFilters":[  
  {  
	  "name":"phonetic_token_filter",  
	  "@odata.type":"#Microsoft.Azure.Search.PhoneticTokenFilter",  
	  "encoder":"doubleMetaphone"
  }],
```

インデックススキーマを段階的に変更しているので、次のコードを追加して PUT リクエストを変更する必要があります。
```
&allowIndexDowntime=true
```
[Send]を押してインデックスを更新します

インデクサーを更新して、病気を取得してこの新しいフィールドに書き込むことを認識させる必要があります。

最後に疾患出力フィールドマッピングでインデクサーを更新した PUT リクエストを表示します。そこにない場合は、履歴リクエストで見つけることができます。

この outputFieldMapping をインデクサーに追加します。

```json
{
	  "sourceFieldName": "/document/diseases/*/name",
	  "targetFieldName": "diseasesPhonetic",
	  "mappingFunction": null
}
```
[Send]を押してインデクサーを更新し、ポータルに戻ります。

ポータルで、インデクサーを**リセット**して、再度インデクサーを**実行**します。

 ![](images/rerun.png)

### アナライザーのテスト
前のモジュールで作成された検索インデックスと音声アナライザー「my_phonetic」に対して Azure Cognitive Search Analyze API を使用して次の2つのリクエストを実行することにより、このエンコードがどのように見えるかを検証できます。

```
POST: https://{name of your service}.search.windows.net/indexes/[search index]/analyze?api-version=2019-05-06
BODY:
{
  "text": "mucopolysaccharidosis",
  "analyzer": "my_phonetic"
}
```
そして、
```
POST: https://{name of your service}.search.windows.net/indexes/[search index]/analyze?api-version=2019-05-06
BODY:
{
  "text": "mukopolisakaridosis",
  "analyzer": "my_phonetic"
}
```

どちらも MKPL という値のトークンになることに注意してください。

```json
{
   "token": "MKPL",
   "startOffset": 0,
   "endOffset": 21,
   "position": 0
}
```

さまざまな異なる語句を送信して、どのようにエンコードされるかを確認してください。

[正規表現](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#property-reference)(RegEx) など、使用できる他の多くの交差するカスタムアナライザーおよびトークナイザーがあります。これにより、パターンを利用して個別のトークンを見つけることができます。

## スコアリングプロファイルを使用したランキング

Azure Cognitive Search は、テキストに基づくいくつかの要素を使用して、返信する最も関連性の高いドキュメントを決定します。これは主に、TF/IDF と呼ばれるアルゴリズムに基づいています。 用語頻度（TF）は、単一のドキュメント内でクエリ用語が一致する頻度を調べます。逆ドキュメント頻度（IDF）は、クエリ用語が多くのドキュメントで一致する頻度を調べます（一般的な例には、「is」、「or」、「the」など）。

Azure Cognitive Search がデフォルトで提供する結果のランキングが最適でない場合があります。たとえば、過去1年間にモルキオ病の治療に最近の進展があったため、ユーザーが「モルキオ」病について論じている臨床試験を検索したい場合、昨年に更新された臨床試験に主に関心があるとします。結果のデフォルトのランキングでは、各臨床試験の lastUpdatePosted フィールドは考慮されません。

幸い、Azure Cognitve Search ではこれを詳細に制御できます。このシナリオを処理する1つの方法は、lastUpdatePosted フィールドで結果を降順に並べることです。これは、インデックスでそのフィールドを **ソート可能** にしたためです。特定のフィールドによる並べ替えが役立つシナリオもありますが、最近更新されたものの、結果がリストの下位にあるため、関連性の低い臨床試験がリストの上部に表示される場合があります。

これを処理する別の方法は、[スコアリングプロファイル](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)と呼ばれるものを作成することです。検索インデックスに対して1つ以上のスコアリングプロファイルを作成でき、各スコアリングプロファイルには、検索結果のデフォルトのスコアリング（および結果の順序）を調整するために使用できる重みと関数のセットがあります。この例では、フレッシュネス関数でスコアリングプロファイルを使用する場合があります。フレッシュネス関数を使用することにより、昨年内に lastUpdatePosted 日付を持つ臨床試験のスコアを上げることができます。

このためのスコアリングプロファイルを作成しましょう。

 * Azure ポータルを開き、インデックスを選択します
 * スコアリングプロファイルを選択し、[スコアリングプロファイルの追加]を選択します
 * 「recentlyUpdatedBoost」という名前を付け、「スコアリングプロファイルの追加」を選択して保存します

  ![](images/add_scoring_profile.png)
 
 * 結果のスコアリングプロファイルで、[関数の追加]を選択します
 * 「スコアリング関数を追加」を選択
 * 関数タイプを「鮮度」に設定します
 * フィールド名を「lastUpdatePosted」に設定します
 * 補間を「線形」に設定します
 * ブースト値を3に設定します
 * ブースティング期間を「P365D」に設定します
 * [OK]をクリックしてから、もう一度[OK]をクリックします
 * [保存]をクリックして、この更新されたスコアプロファイルを保存します

 ![](images/freshness_function.png)
 
### Notes
1. 「フレッシュネス」機能は、インデックスの日時フィールドに基づいてブーストしたい場合に使用されます。
1. 補間では、スコアリングに使用される勾配の形状を設定できます。
1. ブースト値は、生のスコアの乗数として使用される正の数です。
1. ブースティング期間は、特定のドキュメントのブースティングが停止するまでの有効期限を設定します。この例では、それを365日に設定しています。

次に、検索エクスプローラーを開いて、このスコアプロファイルを使用した場合と使用しない場合のいくつかのクエリを試してください。

```
&scoringProfile=recentlyUpdatedBoost
```

検索結果のスコアリングを調整するために使用できる追加の関数がいくつかありますが、それらには以下のものは含まれません。

- マグニチュードは、数値の高低に基づいてブーストする場合に使用する必要があります。この機能を必要とするシナリオには、利益率、最高価格、最低価格、またはダウンロード数によるブーストが含まれます。この関数は、double および integer フィールドでのみ使用できます。
マグニチュード機能では、逆のパターンが必要な場合（たとえば、高額のアイテムよりも低額のアイテムをブーストする場合など）に、範囲を高から低に逆にすることができます。価格の範囲が $100 から $1 の場合、boostingRangeStart を 100 に設定し、boostingRangeEnd を 1 に設定して、低価格のアイテムをブーストします。
- 距離または地理的位置によってブーストする場合は、距離を使用する必要があります。この関数は Edm.GeographyPoint フィールドでのみ使用できます。
- タグは、ドキュメントと検索クエリの間で共通のタグでブーストする場合に使用する必要があります。この関数は、Edm.String および Collection(Edm.String) フィールドでのみ使用できます。

### 次：[モジュール6：PowerBIを使用した抽出データの分析](Module&#32;6.md)
