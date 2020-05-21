# PowerShellを使用した検索インデックスの作成

前のステップの `deploy.ps1` PowerShellスクリプトは、検索インデックスを作成する方法を示しています。

次のコードは、 `templates`フォルダー内のjsonファイルに基づいて検索インデックスを作成します。

```powershell
    while (!($uniqueName = Read-Host "uniqueName")) { Write-Host "You must provide a uniqueName."; }

    # Generate derivative parameters.
    $searchServiceName = $uniqueName + "search";
    $storageAccountName = $uniqueName + "storage";
    $storageContainerName = "documents";

    $dataSourceName = $uniqueName + "-datasource";
    $skillsetName = $uniqueName + "-skillset";
    $indexName = $uniqueName + "-index";
    $indexerName = $uniqueName + "-indexer";

    $global:storageAccountKey = "";
    $global:searchServiceKey = "";
    $global:storageConnectionString = "";
    $global:cogServicesKey = "";

    function CreateSearchIndex
    {
        Write-Host "Creating Search Index"; 

        function CallSearchAPI
        {
            param (
                [string]$url,
                [string]$body
            )

            $headers = @{
                'api-key' = $global:searchServiceKey
                'Content-Type' = 'application/json' 
                'Accept' = 'application/json' 
            }
            $baseSearchUrl = "https://"+$searchServiceName+".search.windows.net"
            $fullUrl = $baseSearchUrl + $url

            Write-Host "Calling api: '"$fullUrl"'";
            Invoke-RestMethod -Uri $fullUrl -Headers $headers -Method Put -Body $body | ConvertTo-Json
		};

        # Create the datasource
        $dataSourceBody = Get-Content -Path .\templates\base-datasource.json  
        $dataSourceBody = $dataSourceBody -replace "{{env_storage_connection_string}}", $global:storageConnectionString      
        $dataSourceBody = $dataSourceBody -replace "{{env_storage_container}}", $storageContainerName        
        CallSearchAPI -url ("/datasources/"+$dataSourceName+"?api-version=2019-05-06") -body $dataSourceBody

        # Create the skillset
        $skillBody = Get-Content -Path .\templates\base-skills.json
        $skillBody = $skillBody -replace "{{cog_services_key}}", $global:cogServicesKey  
        CallSearchAPI -url ("/skillsets/"+$skillsetName+"?api-version=2019-05-06") -body $skillBody

        # Create the index
        $indexBody = Get-Content -Path .\templates\base-index.json 
        CallSearchAPI -url ("/indexes/"+$indexName+"?api-version=2019-05-06") -body $indexBody
        
        # Create the indexer
        $indexerBody = Get-Content -Path .\templates\base-indexer.json
        $indexerBody = $indexerBody -replace "{{datasource_name}}", $dataSourceName
        $indexerBody = $indexerBody -replace "{{skillset_name}}", $skillsetName   
        $indexerBody = $indexerBody -replace "{{index_name}}", $indexName   
        CallSearchAPI -url ("/indexers/"+$indexerName+"?api-version=2019-05-06") -body $indexerBody
	}

    CreateSearchIndex;
```

## PowerShellスクリプトの実行

> PowerShellを初めて使用する場合は、[Windows 10でPowerShellスクリプトファイルを実行する方法](https://www.windowscentral.com/how-create-and-run-your-first-powershell-script-file-windows-10)を使用して作業を開始できます。

[PowerShell script](https://github.com/nohanaga/azure-search-knowledge-mining/blob/master/00%20-%20Resource%20Deployment/deploy.ps1)を実行するには、

1. PowerShellを開き、カレントフォルダーに移動します。
2. 次のコマンドを実行します。

    ```cmd
        ./deploy.ps1
    ```

3. スクリプトを実行すると、ログインして追加情報を提供するように求められます。