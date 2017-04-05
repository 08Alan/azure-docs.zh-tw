---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0ca716857733290fad4278e3be5059408bb75393
ms.lasthandoff: 03/29/2017


---
# <a name="sorting-documentdb-data-using-order-by"></a>使用 Order By 排序 DocumentDB 資料
Microsoft Azure DocumentDB 支援在 JSON 文件上使用 SQL 來查詢文件。 您可以在 SQL 查詢陳述式中使用 ORDER BY 子句來排序查詢結果。

閱讀本文後，您將能夠回答下列問題： 

* 如何使用 Order By 來進行查詢？
* 如何設定 Order by 的編製索引原則？
* 未來將推出哪些新功能？

本文章另提供[範例](#samples)和[常見問題集](#faq)。

如需 SQL 查詢的完整參考，請參閱 [DocumentDB 查詢教學課程](documentdb-sql-query.md)。

## <a name="how-to-query-with-order-by"></a>如何使用 Order By 來進行查詢
您現在查詢 DocumentDB 時於 SQL 陳述式中加入選擇性的 Order By 子句，就像 ANSI SQL 一樣。 子句可以包含選擇性 ASC/DESC 引數，利用它來指定擷取結果時必須依循的順序。 

### <a name="ordering-using-sql"></a>使用 SQL 來進行排序
例如，以下是依照其標題之遞減順序擷取前 10 名書籍的查詢。 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>使用 SQL 來進行排序並搭配篩選
您可以在文件內使用 Books.ShippingDetails.Weight 之類的任何巢狀屬性來進行排序，也可以在 WHERE 子句中指定其他篩選來搭配 Order By，如以下範例所示：

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>使用適用於 .NET 的 LINQ 提供者來進行排序
如果您使用 .NET SDK 1.2.0 和更新版本，還可以在 LINQ 查詢中使用 OrderBy() 或 OrderByDescending() 子句，如以下範例所示：

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB 支援對於每一個查詢使用單一數值、字串或布林值屬性的排序，即將推出其他查詢類型。 如需詳細資訊，請參閱 [未來將推出哪些新功能](#Whats_coming_next) 。

## <a name="configure-an-indexing-policy-for-order-by"></a>設定 Order by 的編製索引原則
請回想支援兩種類型索引 (雜湊和範圍)的 DocumentDB，它可以針對特定路徑/屬性、資料類型 (字串/數字)，並且以不同的精確度值 (最大精確度或固定精確度值) 進行設定。 因為 DocumentDB 使用雜湊索引做為預設值，您必須以具有數字、字串或兩者之「範圍」的自訂索引編製原則建立新集合，才能使用 Order By。 

> [!NOTE]
> 字串範圍索引是在 2015 年 7 月 7 日的 REST API 2015-06-03 版本中引進。 若要針對字串建立 Order By 的原則，您必須使用 .NET SDK 的 SDK 1.2.0 版，或 Python、Node.js 或 Java SDK 的 1.1.0 版。
> 
> 在 REST API 2015-06-03 版之前的版本，對於字串和數字的預設集合索引編製原則是「雜湊」。 已經變更為「雜湊」用於字串，以及「範圍」用於數字。 
> 
> 

如需詳細資訊，請參閱 [DocumentDB 索引編制原則](documentdb-indexing-policies.md)。

### <a name="indexing-for-order-by-against-all-properties"></a>針對所有屬性編製 Order By 的索引
以下是您如何針對出現在 JSON 文件內的任何/所有數字或字串屬性，以 Order By 的「所有範圍」索引建立集合。 這裡我們會將字串值的預設索引類型覆寫為範圍，並且使用最大精確度 (-1)。

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> 請注意，Order By 只會傳回使用 RangeIndex 編製索引的資料類型 (字串和數字) 的結果。 例如，如果您有預設的索引編製原則，只有數字的 RangeIndex，則針對具有字串值之路徑的 Order By 不會傳回任何文件。
> 
> 

### <a name="indexing-for-order-by-for-a-single-property"></a>針對單一屬性編制 Order By 的索引
以下是僅針對字串的 Title 屬性利用編制 Order By 索引來建立集合的方式。 有兩種路徑，一個用於 Title 屬性 ("/Title/?") 與「範圍」索引編製，而另一個用於具有預設索引編製配置的其他每個屬性，「雜湊」用於字串及「範圍」用於數字。                    

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>範例
請參閱這個示範如何使用 Order By 的 [GitHub 範例專案](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)，其內容包括使用 Order By 建立索引編製原則和分頁。 這些範例是開放原始碼，我們鼓勵您提交提取要求，並附上可幫助其他 DocumentDB 開發人員的貢獻。 請參閱 [貢獻指導方針](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) ，以取得有關如何貢獻的指引。  

## <a name="faq"></a>常見問題集
**Order By 查詢的預期要求單位 (RU) 耗用量有多高？**

由於 Order By 利用 DocumentDB 索引來進行查閱，因此 Order By 查詢所耗用的要求單位數目將與不含 Order By 的同等查詢相似。 就像 DocumentDB 上的其他所有作業一樣，要求單位的數目取決於文件大小/圖形，以及查詢的複雜性。 

**Order By 的預期索引額外負荷有多高？**

索引編製的儲存額外負荷將與屬性的數目成正比。 在最糟的情況下，索引額外負荷會是資料的 100%。 Range/Order By 索引編製和預設雜湊索引編製之間的輸送量 (要求單位) 額外負荷並無差別。

**如何使用 Order By 查詢 DocumentDB 中的現有資料？**

若要使用 Order By 排序查詢結果，您必須將集合的索引編製原則修改為使用範圍索引類型來排序，而不使用屬性來排序。 請參閱 [修改索引編製原則](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection)。 

**Order By 目前的限制有哪些？**

您只能針對數值或字串屬性，在以最大精確度 (-1) 索引編製過索引的範圍時指定 Order By。

您無法執行以下工作：

* 將 Order By 用於內部字串屬性，如 id、_rid, and _self (即將推出)。
* 將 Order By 用於衍生自文件內部聯結之結果的屬性 (即將推出)。
* 依多個屬性執行 Order By (即將推出)。
* 將 Order By 用於針對資料庫、集合、使用者、權限或附件的查詢 (即將推出)。
* 將 Order By 用於計算的屬性 (如運算式或 UDF/內建函數的結果)。

在 Azure 入口網站中使用 [查詢總管] 時，Order By 目前不支援跨資料分割查詢。

## <a name="troubleshooting"></a>疑難排解
如果您收到錯誤說明不支援 Order By，請檢查並確定您正在使用的 [SDK](documentdb-sdk-dotnet.md) 版本可支援 Order By。 

## <a name="next-steps"></a>後續步驟
取用 [GitHub 範例專案](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)，並開始排序您的資料 ！ 

## <a name="references"></a>參考
* [DocumentDB 查詢參考](documentdb-sql-query.md)
* [DocumentDB 索引編製原則參考](documentdb-indexing-policies.md)
* [DocumentDB SQL 參考](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB Order By 範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)


