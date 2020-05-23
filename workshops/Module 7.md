# オプション：モジュール7：Azure SQLからのデータのインデックス作成

過去のモジュールでは、PDF などの非構造化ソースからのコンテンツのインデックス作成に重点を置いていました。多くの場合、Azure SQL や Cosmos DB などの構造化データソースには、フルテキスト検索を実行する機能を活用できる多くのデータが存在します。ここでの利点は、PDF のような非構造化データとは異なり、データがすでに構造化されているため、ファセットやフィルタリングを通じてこれを利用できることです。それでも、「エンリッチメント」の恩恵を受けることができるテキストコンテンツがまだあることがよくあります。

以下のモジュールでは、完全に匿名化された一連の患者データ（患者名、住所、メモなど）を取得して、Azure Cognitive Search にインデックス付けします。前のモジュールと同様に、カスタムスキルを利用して患者ノートから疾患を抽出し、結果のアプリケーションを作成して患者情報を検索します。

## SQL データベース接続情報

SQL Server Management Studio がある場合は、データを保持する Azure SQL データベースに接続してインデックスを作成できますが、このモジュールでは必要ありません。 データベースに接続するには、次の情報を使用します。

```
Server: azs-playground.database.windows.net
Database: medical-demo
User: reader
Password: EdrERBt3j6mZDP
Table: patient-info
```

データは次のようになります。

 ![](images/sql-patient-info.png)

## Azure Cognitive Search へのコンテンツのインデックス作成

モジュール3で行ったように、Postman を利用して、データを保持するデータソース、スキルセット、インデクサー、インデックスを作成します。代わりにポータルを使用することもできますが、ポータルでまだ使用できないため、カスタムスキルを設定して疾患を抽出することはできません。

### インデックスを作成する
[searchservice] を検索サービスに置き換え、ヘッダーで管理者APIキーを使用します。

PUT: https://[searchservice].search.windows.net/indexes/patient-info?api-version=2019-05-06

Headers:
* Content-Type: application/json
* api-key: [your admin api-key]

Body:
```json
{
    "name": "patient-info",
    "defaultScoringProfile": "",
    "fields": [
        {
            "name": "patient_id",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": true,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "en.microsoft",
            "synonymMaps": []
        },
        {
            "name": "dr_name",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        },
        {
            "name": "dob",
            "type": "Edm.DateTimeOffset",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "address",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        },
        {
            "name": "city",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        },
        {
            "name": "state",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        },
        {
            "name": "zip",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        },
        {
            "name": "marital_status",
            "type": "Edm.String",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "race",
            "type": "Edm.String",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "ethnicity",
            "type": "Edm.String",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "gender",
            "type": "Edm.String",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "last_checkup",
            "type": "Edm.DateTimeOffset",
            "searchable": false,
            "filterable": true,
            "retrievable": true,
            "sortable": false,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "summary",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "en.microsoft",
            "synonymMaps": []
        },
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
        }
    ],
    "scoringProfiles": [],
    "corsOptions": null,
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "diseases", "city", "zip", "dr_name"
            ]
        }
    ],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### データソースを作成する
[searchservice] を検索サービスに置き換え、ヘッダーで管理者APIキーを使用します。

PUT: https://[searchservice].search.windows.net/datasources/medical-demo?api-version=2019-05-06

Headers:
* Content-Type: application/json
* api-key: [your admin api-key]

Body:
```json
{
    "name": "medical-demo",
    "description": null,
    "type": "azuresql",
    "subtype": null,
    "credentials": {
        "connectionString": "Server=tcp:azs-playground.database.windows.net;Database=medical-demo;User ID=reader;Password= EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;"
    },
    "container": {
        "name": "[patient_info]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### スキルセットを作成する
[searchservice] を検索サービスに置き換え、ヘッダーで管理者APIキーを使用します。


**注:** 以下のスキルセットは、既存の Azure Functions
```
https://diseaseextraction.azurewebsites.net/api/custom-search?code=HXS0y4rEoQZ9p55A7wqybSeYFmYP6Lruna8y8HoAGu3kNSoLf80XWw==
```
を利用して病気を抽出します。これをそのまま使用することも、前のモジュールで作成したものに変更することもできます。

[Cognitive Services Key] を有効な Cognitive Services Key（Azure Cognitive Search サービスと同じリージョンで作成されたもの）で更新する必要もあります。

PUT: https://[searchservice].search.windows.net/skillsets/patient-demo?api-version=2019-05-06

Headers:
* Content-Type: application/json
* api-key: [your admin api-key]

Body:
```json
{
    "name": "patient-demo",
    "description": "basic skillset",
    "skills": [
		{
			"@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
			"description": "Disease Extraction skill",
			"uri": "https://diseaseextraction.azurewebsites.net/api/custom-search?code=HXS0y4rEoQZ9p55A7wqybSeYFmYP6Lruna8y8HoAGu3kNSoLf80XWw==",
			"batchSize":1,
			"context": "/document",
			"inputs": [
			  {
				"name": "text",
				"source": "/document/summary",
                    "sourceContext": null,
                    "inputs": []
			  }
			],
			"outputs": [
			  {
				"name": "Entities",
				"targetName": "entities"
			  },
			  {
				"name": "EntitiesFound",
				"targetName": "diseases"
			  }
			]
		},
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "#2",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "patient_id",
                    "source": "/document/patient_id",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "name",
                    "source": "/document/name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "dr_name",
                    "source": "/document/dr_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "dob",
                    "source": "/document/dob",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "address",
                    "source": "/document/address",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "city",
                    "source": "/document/city",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "state",
                    "source": "/document/state",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "zip",
                    "source": "/document/zip",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "marital_status",
                    "source": "/document/marital_status",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "race",
                    "source": "/document/race",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "ethnicity",
                    "source": "/document/ethnicity",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "gender",
                    "source": "/document/gender",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "last_checkup",
                    "source": "/document/last_checkup",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "summary",
                    "source": "/document/summary",
                    "sourceContext": null,
                    "inputs": []
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "tableprojection"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "/subscriptions/ee787b9b-a25f-4d20-86e9-45fcea5253dd/resourceGroups/km-workshop/providers/Microsoft.CognitiveServices/accounts/kmworkshop",
        "key": "[Cognitive Services Key]"
    }
}
```

### インデクサーを作成する
[searchservice] を検索サービスに置き換え、ヘッダーで管理者APIキーを使用します。

PUT: https://[searchservice].search.windows.net/indexers/patient-info?api-version=2019-05-06

Headers:
* Content-Type: application/json
* api-key: [your admin api-key]

Body:
```json
{
    "name": "patient-info",
    "description": "",
    "dataSourceName": "medical-demo",
    "skillsetName": "patient-demo",
    "targetIndexName": "patient-info",
    "disabled": null,
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": false,
        "configuration": {}
    },
    "fieldMappings": [],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/diseases",
            "targetFieldName": "diseases",
            "mappingFunction": null
        }

    ]
}
```
