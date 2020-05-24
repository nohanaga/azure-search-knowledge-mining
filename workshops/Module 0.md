# ナレッジマイニングワークショップの前提条件

ワークショップを開始する前に、次の前提条件を満たしていることを確認してください。

1.	自分のAzureアカウントを持っている
1.	[Azure Portal](https://portal.azure.com) を使用できること
1.	サブスクリプションでAzureリソースを作成できることを確認します（有料リソースを含む）。

  **注**: *組織のポリシーにより、サブスクリプションでのリソースの作成が禁止されている場合、このラボでは[無料サブスクリプション](https://signup.azure.com)を使用できます。*

4. このワークショップのリソースグループを作成し、次のステップで作成する各リソースを追加します。

4. [Azure Storage Account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) を**作成**します。

   - パフォーマンスの選択：Premium ではなく **Standard**
   - アカウントの種類を選択：**StorageV2（汎用v2）**

4. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を**インストール**します。
4. 臨床試験を保持するストレージコンテナーを読み取り専用の場所からストレージアカウントに**コピー**します。
    1. Azure Storage Explorer を開いて、**アカウントの管理**、**アカウントを追加する...**、**Shared Access Signature (SAS) URIを使用する** を選択します。*表示名*は自動入力されます。 [*次へ*]を選択します
    1. 次の *URI*を**追加**してから、**次へ**を選択し、次に**接続**を選択します。
        ```
        https://kmworkshop.blob.core.windows.net/clinical-trials-small?st=2019-09-13T22%3A58%3A18Z&se=2020-09-14T22%3A58%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=M7MPfuxZvVvBkf0Jgg%2BvKWyB49RFYlGNhQ4%2F1nIJ9DU%3D
        ```
    3. エクスプローラーを表示するために、**エクスプローラーを切り替える** を選択します。 接続したばかりの *clinical-trials-small* Blobコンテナーを右クリックし、**BLOB コンテナーのコピー** を選択します。

        ![](images/copyblobcontainer.png)
        
    3. エクスプローラーで本ワークショップで使用するストレージアカウントを**検索**します。 **Blob Containers** を右クリックし、**BLOB コンテナーを貼り付け** を選択します。

        ![](images/pasteblobcontainer.png)

    1. Azure Storage Explorer の下部にあるアクティビティをチェックして、コンテナーが正常にコピーされたことを確認します。

4.	[Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-create-service-portal) リソースを**作成**します。（このワークショップには無料枠で十分です）[参考：価格表](https://docs.microsoft.com/azure/search/search-sku-tier)

4.	[Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows) リソースを**作成**します。

    **注**: *Azure Cognitive Search リソースと同じリージョンに Cognitive Services リソースを作成する必要があります。*

10.	[Visual Studio 2019](https://visualstudio.microsoft.com/) を**インストール**します。それでASP.Net Webサイトを作成できることを確認してください。
11.	[Postman](https://www.getpostman.com/) を**インストール**します。
11.	[PowerBI desktop](https://powerbi.microsoft.com/desktop/) を**インストール**します。

### 次：[モジュール1：Azure Portalを使用してインデックスを作成する-コードは不要](Module&#32;1.md)
