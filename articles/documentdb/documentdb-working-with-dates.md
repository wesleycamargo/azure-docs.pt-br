---
title: Trabalhando com datas no Azure DocumentDB | Microsoft Docs
description: Saiba como trabalhar com datas no Azure DocumentDB.
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c18d17d40dff658a8fc47ef2126dd2c21b68606a
ms.lasthandoff: 03/29/2017


---
# <a name="working-with-dates-in-azure-documentdb"></a>Trabalhando com datas no Azure DocumentDB
O DocumentDB fornece flexibilidade de esquema e indexação avançada por meio de um modelo de dados [JSON](http://www.json.org) nativo. Todos os recursos do DocumentDB, incluindo bancos de dados, coleções, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para ser portátil, o JSON (e o DocumentDB) dá suporte apenas a um pequeno conjunto de tipos básicos: Cadeia de caracteres, Número, Booliano, Matriz, Objeto e Nulo. No entanto, o JSON é flexível e permite aos desenvolvedores e estruturas representar tipos mais complexos usando esses primitivos e compondo-os como objetos ou matrizes. 

Além dos tipos básicos, muitos aplicativos precisam do tipo [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) para representar datas e carimbos de data/hora. Este artigo descreve como os desenvolvedores podem armazenar, recuperar e consultar datas no DocumentDB usando o SDK do .NET.

## <a name="storing-datetimes"></a>Armazenando DateTimes
Por padrão, o [SDK do DocumentDB](documentdb-sdk-dotnet.md) serializa os valores de DateTime como cadeias de caracteres [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874). A maioria dos aplicativos pode usar a representação de cadeia de caracteres padrão para DateTime pelos seguintes motivos:

* As cadeias de caracteres podem ser comparadas e a ordenação relativa dos valores de DateTime é preservada quando eles são transformados em cadeias de caracteres. 
* Essa abordagem não exige qualquer código personalizado ou atributos para conversão de JSON.
* As datas, conforme armazenadas no JSON, são legíveis para humanos.
* Essa abordagem pode aproveitar o índice do DocumentDB para desempenho rápido de consulta.

Por exemplo, o trecho a seguir armazena um objeto `Order` que contém duas propriedades DateTime — `ShipDate` e `OrderDate` como um documento usando o SDK do .NET:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Esse documento está armazenado no DocumentDB da seguinte maneira:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Como alternativa, você pode armazenar DateTimes como carimbos de data/hora do Unix, ou seja, como um número que representa o número de segundos decorridos desde 1º de janeiro de 1970. A propriedade Timestamp (`_ts`) interna do DocumentDB segue essa abordagem. Você pode usar a classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) para serializar DateTimes como números. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexando DateTimes para consultas de intervalo
As consultas de intervalo são comuns com valores de DateTime. Por exemplo, se precisar localizar todos os pedidos criados desde ontem ou todos os pedidos enviados nos últimos cinco minutos, você precisará executar consultas de intervalo. Para executar essas consultas de modo eficiente, você deve configurar sua coleção para indexação de Intervalo em cadeias de caracteres.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Você pode saber mais sobre como configurar as políticas de indexação em [Políticas de indexação do DocumentDB](documentdb-indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Consultando DateTimes no LINQ
O SDK do .NET para DocumentDB dá suporte automaticamente à consulta de dados armazenados no DocumentDB via LINQ. Por exemplo, o trecho a seguir mostra uma consulta do LINQ que filtra pedidos que foram enviados nos últimos três dias.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on DocumentDB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Você pode aprender mais sobre a linguagem de consulta SQL do DocumentDB o provedor LINQ em [Consultando o DocumentDB](documentdb-sql-query.md).

Neste artigo, analisamos como armazenar, indexar e consultar DateTimes no DocumentDB.

## <a name="next-steps"></a>Próximas etapas
* Baixe e execute os [Exemplos de código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas do Banco de Dados de Documentos](documentdb-sql-query.md)
* Saiba mais sobre [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md)

