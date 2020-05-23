# モジュール4：オブジェクトモデルを学ぶ
**注:** この一連の演習では、モジュール1で作成したインデックスを変更します。モジュール1をすべて完了するまで、これらの手順を完了することはできません。

このモジュールでは、データソースからインデックスにデータを取得できる4つの主要なオブジェクトについて学習します。
+ プログラムでインデックス（オブジェクト）を作成および変更する方法
+ モジュール1で作成したインデックスを変更する方法

モジュール1のデータインポートウィザードの説明に沿って、4つのリソースを作成しました。
 
![](images/objectmodel.png)

1. **データソース**は、データのソースに関する情報を定義します（データソースの種類、データの読み取り方法、資格情報など）。
2. **スキルセット**は、データに適用するエンリッチメントステップ（またはスキル）を定義します。
3. **インデックス**は、検索可能なインデックスの形状を定義します。
4. 最後に、これら3つのオブジェクトは、データソースからのデータの取り込み、スキルセットの実行、およびエンリッチされたデータをインデックスにマップする方法を調整する**インデクサー**によって接続されます。

このモジュールでは、これらを変更して、システムに少しインテリジェンスを追加します。

## インデックスの定義
インデックスの定義を変更してみましょう。インデックスには**追加**の変更しかできないため、十分に注意してください。間違えると編集できない場合がありますので、再確認してください。

### ポータルでJSONを検査します

![](images/jsonportal.png)

ポータルにより、編集が簡単になる場合があります。ただし、プログラムで行うこともできます。これは、2つの新しいフィールドを検索インデックスに追加することで行います。

追加する最初のフィールドは「diseases」と呼ばれ、単にテキストから抽出された疾患のコレクションを保持します。それがこのモジュールの残りの部分の焦点です。

「diseasesPhonetic」と呼ばれる2番目のフィールドにも抽出された疾患が保持されますが、Azure Cognitive Search がユーザーに提供する多くのカスタムアナライザーの1つである音声アナライザーと呼ばれるものを使用します。ご想像のとおり、音声分析では音声学的に類似していると思われる単語を検索できます。このフィールドを追加し、モジュール5でさらに音声分析を探索します。

まず Postman を開いて次の GET リクエストを行うことで、現在のインデックススキーマを取得できます。
```
GET https://{name of your service}.search.windows.net/indexes/clinical-trials-small?api-version=2019-05-06
```
以降のすべてのリクエストでは、次の2つのヘッダー値を設定する必要があります。
* api-key: [Azure Cognitive Search ポータルから管理者キーを入力します]
* Content-Type: application/json

![](images/get-index-schema.png)

GET リクエストで返されたインデックススキーマを[**Body**]にコピーします。新しいフィールドを追加して、[**Body**]のコピーされたコンテンツを更新します。

```json
"fields": [
   ...,
   {
	"name": "diseases",
	"type": "Collection(Edm.String)",
	"searchable": true,
	"filterable": true,
	"retrievable": true,
	"sortable": false,
	"facetable": true,
	"key": false,
	"indexAnalyzer": null,
	"searchAnalyzer": null,
	"analyzer": "en.microsoft",
	"synonymMaps": []
   },
   ...
]
```

次のリクエスト構造を持つリクエストを PUT に変更します。 インデックスを更新するリクエストを送信します。

```
PUT  https://{name of your service}.search.windows.net/indexes/clinical-trials-small?api-version=2019-05-06
``` 

PUT リクエストが成功すると、ステータスコード 204 が返されます。これは、インデックスが正常に更新されたことを示します。レスポンスの Body にはコンテンツはありません。

次に、モジュール3で構築した疾患抽出器を組み込むようにスキルセットを変更します。

まず、スキルセットの定義がどのようなものかを見てみましょう。Postman を呼び出し、次のリクエストを発行します。

```
GET  https://{name-of-your-service}.search.windows.net/skillsets/clinical-trials-small?api-version=2019-05-06-Preview
```

 ![](images/postman.png)

ポータルから取得できるキーに基づいて **api-key header** を設定してください。

また、ナレッジストアのプレビュー機能を使用しているため、**2019-05-06-Preview** バージョンを使用していることに注意してください。

まず、JSONを見てみましょう。多くのスキルが自動的に生成されたことがわかります。この一般的なパターンに従う4つのスキルがあるはずです。

 ![](images/4skills.png)
 
###	新しいカスタムスキルを追加します
疾患を抽出するステップをエンリッチメントパイプラインに追加し、そのフィールドがインデックスに保存される場所を定義し、インデクサーも変更します。

![](images/diseaseextractor.png)


スキルセットを編集するための PUT リクエストを作成します。

```
PUT    https://{your-service-here}.search.windows.net/skillsets/clinical-trials-small?api-version=2019-05-06-Preview 
```

スキルリストを変更し、病気をテーブル予測に追加します。

###	各ドキュメントの疾患リストを抽出するスキルに追加のスキルを追加します

GET リクエストから取得したレスポンスを貼り付けて、スキルを追加します。

URI を取得するには、モジュール3でテストした公開済みのスキルから取得する必要がありますが、これは私たちのテストスキルでは次のようになりました…

```
         {
            "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
            "name": "diseases",
            "description": "Disease Extraction Skill",
            "context": "/document",
            "uri": "<your custom skill URI here>",
            "httpMethod": "POST",
            "timeout": "PT90S",
            "batchSize": 1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content",
                    "inputs": []
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "diseases"
                }
            ]
        }
```
###  Shaper スキルに新しいレコードを追加し、新しいテーブルプロジェクションを追加する

#### Shaper スキルに新しい病気の入力を追加します

```
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "inputs": [
            	{
                    "name": "diseases",
                    "sourceContext": "/document/diseases/*",
                    "inputs": [
                        {
                            "name": "disease",
                            "source": "/document/diseases/*/name"
                        }
                    ]
                },
                {
                    "name": "lastUpdatePosted",
                    "source": "/document/lastUpdatePosted",
                    "sourceContext": null,
                    "inputs": []
                },
...
```
*document/diseases* は、次のような複合型の配列を指すことに注意してください。
```
"document/diseases": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    },
    ...
]

```

また、*/document/diseases/\** は、その配列の各メンバー（これらの複合型のそれぞれ）を指します。

このスキルは、多くのメンバーを持つ *tableprojection* と呼ばれる新しい複雑なオブジェクトを形成します。
*diseases* という新しいメンバーを追加しました。

この新しいメンバーの "sourceContext" は *"/document/diseases/\*"* なので、新しいメンバー自体はオブジェクトの配列になります。これらの各オブジェクトには、*disease* という単一のメンバーがあり、各疾患の名前が付いています。

JSON 表現は次のようになります。

```
"document/tableprojection" :
{
	"lastUpdatePosted": "August 10, 2017",
	"diseases" : [
	    {
	        "disease": "heart failure"
            },
	    {
	        "disease": "morquio"
            },
	    ...
	]
}
```

#### 以下に示すように、この新しいフィールドを含めるようにテーブルプロジェクションを変更します

PowerBI のような一部のツールは、JSON オブジェクトを一括で与えた場合よりも、テーブルとデータベースを取り込む方法をよく理解しています。これで、目的の形のオブジェクトができたので、相関させる一連のテーブルに**投影**します。

新しい病気のメンバーのリストにテーブルをもう1つ追加しましょう。

```
         "projections": [
            {
                "tables": [
                    {
                        "tableName": "clinicalTrialsSmallDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/tableprojection",
                        "sourceContext": null,
                        "inputs": []
                    },
                    {
                        "tableName": "clinicalTrialsSmallEntities",
                        "generatedKeyName": "Entitiesid",
                        "source": "/document/tableprojection/Entities/*",
                        "sourceContext": null,
                        "inputs": []
                    },
		    {
			"tableName": "clinicalTrialsSmallDiseases",
                    	"generatedKeyName": "Diseaseid",
                    	"source": "/document/tableprojection/diseases/*",
                    	"sourceContext": null,
                        "inputs": []
                	}
                ],
                "objects": []
            }
```
これを行うと、抽出された各疾患に一意の識別子(*Diseaseid*) が付与されます。"/document/tableprojection/diseases/\*" は "/document/tableprojection" の子であるため、病気のテーブルには「Documentid」という列も自動的に取得されます。

これらの変更を行った後、PUT リクエストを完了します。

```
PUT https://{your-service-name-goes-here}.search.windows.net/skillsets/clinical-trials-small?api-version=2019-05-06-Preview
```
ナレッジストアの投影はまだプレビューモードであるため、バージョン 2019-05-06-Preview を使用することが重要です。

*スキルセットで行ったのと同じように、REST APIを使用して各リソース（インデックス、データソース、インデクサー、スキルセット）を作成、取得、編集、および削除できます*。

### インデクサーの出力フィールドマッピングを更新する

次に、同様のプロセスに従ってインデクサーを取得および変更します。インデクサーは、すべてを一緒に接着する要素です。

**outputFieldMapping to the indexer** を追加します。これにより、抽出した疾患をインデックスのどこに保存するかが指定されます。これは、**フィールドマッピング**ではなく、**出力フィールドマッピング**で行ってください。**フィールドマッピング**はエンリッチメントの前に発生し、**出力フィールドマッピング**はエンリッチメントの後に発生します。

GET を実行し、次に PUT を使用して、

```
https://{your-service-name-goes-here}.search.windows.net/indexers/clinical-trials-small?api-version=2019-05-06
```

```json
    "outputFieldMappings": [
       ...,
       {
            "sourceFieldName": "/document/diseases/*/name",
            "targetFieldName": "diseases",
            "mappingFunction": null
        },
	...
 ```

これは、複雑な病気のタイプの名前のそれぞれをフラットな配列にマッピングします（つまり、\["diabetes", "morquio", ...\]）

次に、ドキュメントを再処理します。ポータルにアクセスしてインデクサーを**リセット**し、再度**実行**してください。

 ![](images/rerun.png)
 
## 検索結果のデータ
 
結果のコンテンツのインデックス作成には少し時間がかかりますが、ポータルから「検索エクスプローラー」に移動して、結果のデータを確認することができます。

Azure Cognitive Search サービスから[**インデックス**]を選択し、このモジュールで使用していたインデックスをクリックします。

クエリ文字列に、次のように入力します。

```
search=*&facet=diseases
```
これは、検索エンジンにすべてのドキュメント(*)を検索し、結果を疾患別にグループ化するように指示します。

この新しいフィールドを使用して、病気「morquio」について語っているドキュメントを厳密にフィルターできるようにします。
```
search=*&$filter=diseases/any(disease: disease eq 'morquio')&$select=metadata_title,diseases
```
このクエリを調整して、「morquio」について語り、「chest pain」について語らないドキュメントのみを含めるようにしてください。

<details>
  <summary>Click for the answer</summary>
  <p>

  ```
  search=*&$filter=diseases/any(disease: disease eq 'morquio') and diseases/all(disease: disease ne 'chest pain')&$select=metadata_title,diseases
```

  </p>
</details>

### 次：[モジュール5：高度なAzure Cognitive Search](Module&#32;5.md)
