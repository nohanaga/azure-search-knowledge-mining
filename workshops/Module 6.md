# モジュール6：PowerBIを使用した抽出データの分析
目的：ナレッジストアに情報を投影したので、この構造化データは、検索を超えるシナリオで役立つ可能性があります。このモジュールでは、作成したテーブルプロジェクションを PowerBI に接続し、抽出したデータを使用していくつかのサンプルグラフを作成します

モジュール1でナレッジストアを構築したときに作成したテーブルを見てみましょう。

2つのテーブルがあり、1つはドキュメント用で、もう1つはそれらのドキュメントで識別されるエンティティごとに1つあります。

![](images/kstable.png)


![](images/kstable2.png)

このデータは、分析、MLモデルのトレーニング、または単に作成した抽出のキャッシュの維持に役立ちます。

# PowerBI を使用した抽出データの分析

1.	**PowerBI デスクトップ**を実行し、[**データを取得**]をクリックします。
 
    ![](images/mod5/ks-pbi-getdata.png)
 
2.	**Azure** > **Azure Table Storage** を選択し、**アカウント名**を入力します。（これは、モジュール1で Azure ストレージアカウントに付けた名前です）
 
    ![](images/mod5/ks-pbi-getdata2.png)
 
    ![](images/mod5/ks-pbi-getdata3.png)
 
3.	プロンプトが表示されたら、ストレージアカウントのキーを入力します。これは、以下に示すように、Azure ポータルにあります。

![](images/storeacct.png)

4.	先ほど作成した3つのテーブルを選択し、[**ロード**]をクリックします。
 
    ![](images/mod5/ks-pbi-getdata4.png)
 
5.	[**クエリを編集**]コマンドをクリックして、**Power Query** を開きます。
 
    ![](images/mod5/ks-pbi-editquery1.png)
 
    各テーブルについて：
    + Azure テーブルストレージによって作成された **PartitionKey**、**RowKey**、および **Timestamp** 列を削除します。列名を右クリックして、[**削除**]をクリックします。ナレッジストアは、この分析で使用される関係を提供します。

    + [コンテンツ]列の右側にある小さな展開アイコンをクリックして、[**コンテンツ**]フィールドを展開します。以下のように、すべての列を選択し、「元の列名を接頭辞として使用する」のチェックを外します。
 
        ![](images/mod5/ks-pbi-editquery2.png)
        ![](images/mod5/ks-pbi-editquery3.png)

    3つのテーブルに対してこの操作を行うことを忘れないでください。
 
7.	**ドキュメントテーブル**の **lastUpdatePosted** のタイプを**日付**のタイプに変更します。
 
    ![](images/mod5/ks-pbi-editquery4.png)

8.	次に[**閉じて適用**]をクリックします。
 
    ![](images/mod5/ks-pbi-closeandapply.png)

 
9. 次に、関係を双方向になるように変更します。これにより、疾患または場所を選択するたびにドキュメントをフィルタリングできます。
   
+ [モデル]タブをクリックします

    ![](images/mod5/ks-pbi-model.png)

+ 双方向になるように関係を変更します。関係の矢印をダブルクリックすると、[関係の編集]ダイアログが表示され、[クロスフィルターの方向]を[**両方**]に設定します。両方のケースでこのアクションを実行します(*Document* <--> *Disease* および *Document* <--> *Entities*)

    ![](images/mod5/ks-pbi-model2.png)

10.	[**レポート**]タブに戻って、いくつかの視覚化を作成します。すべてのエンティティを示すテーブルを作成することから始めましょう。テーブルアイコンをクリックし、*disease* を[**値**]フィールドにドラッグします。

    ![](images/mod5/ks-pbi-visual1.png)

    テーブルは次のようになります。

    ![](images/mod5/ks-pbi-visual2-disease-list.png)
 
11. 臨床試験の発生率を経時的に示す視覚化を追加してみましょう。

**積み上げ縦棒グラフ**を追加します。*Axis* には *lastUpdatePosted* フィールドを選択し、その**日付階層**を選択します。

![](images/mod5/ks-pbi-visual3-lastupdate-date.png)

次に、**四半期**、**月**、および**日**を削除します。これは、臨床試験が行われた年のみを考慮するためです。

値軸には、**Count (DocumentId)** を選択します。これにより、1年あたりの個別の試験の数をグラフ化できます。これを行うには、単に**DocumentId** フィールドを **Value** フィールドにドラッグします。

![](images/mod5/ks-pbi-visual4-doccount-graph.png)

diseases テーブルで疾患を選択すると、任意の年のその用語に言及している臨床試験を見ることができるはずです。たとえば、*mucopolyyssacharidosis* を選択すると、この10年間にこの分野での研究が増加していることがわかります。

![](images/mod5/ks-pbi-visual4-doccount-graph-filtered.png)
 
13. 塗りつぶされた地図の視覚化を追加して、臨床試験のさまざまな国と研究場所を特定します。*Location* には、*Entity* を選択しますが、*Entities* を *Location Entities* のみにフィルタリングします。

![](images/mod5/ks-pbi-visual5-filledmap-settings.png)
 
14. 完全を期すために、現在の場所/時間フィルターに基づいて臨床試験のタイトルを表示する表をもう1つ追加します。以下のように、**テーブル**視覚化を選択し、視覚化する値として **clinicalTrialsSmallDocument** テーブルから **metadata_title** および **lastUpdatePosted** フィールドを追加します。
 
    ![](images/mod5/ks-pbi-visual5-filledmap-settings2.png)

    これで、レポートは次のようになります。

    ![](images/mod5/ks-pbi-visual5-filledmap-graph.png)

Congratulations! これでインタラクティブなレポートができました。ここにあなたができるいくつかの楽しい練習があります：

1.	さまざまな年をクリックして、このデータセットで研究が長年にわたってどのように進化したかを確認してください。たとえば、*MPS* の研究は、たとえば、年月を経て実際によりグローバルになったことがわかります。

2.	*“Hypohidrosis”* (多汗症)などの特定の疾患を選択して、その臨床試験を行った研究者がどこにいるかをすばやく確認します。

3.	独自のビジュアライゼーションを作成してみてください。ビジュアライゼーションマーケットプレイスには多くの興味深いビジュアライゼーションがあります。これは、さまざまな疾患を結びつけるドキュメントを見つけるために、Force-Directed-Graph Visualization で作成したものです。
 
    ![](images/mod5/ks-pbi-visual6-directed-graph.png)

### 次：[オプション：モジュール7：Azure SQLからのデータのインデックス作成](Module&#32;7.md)



