---
title: Provisionar a produtividade do Azure Cosmos DB | Microsoft Docs
description: "Saiba como definir a produtividade provisionada para contêineres, coleções, grafos e tabelas do Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: afbb3392a4726ea067bf19c117792b866d9e79f3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Definir a produtividade de contêineres do Azure Cosmos DB

Você pode definir a produtividade dos contêineres do Azure Cosmos DB no portal do Azure ou usando os SDKs do cliente. 

A tabela a seguir lista a produtividade disponível para cada contêiner:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contêiner de partição única</strong></p></td>
            <td valign="top"><p><strong>Contêiner particionado</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>1000 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir a taxa de transferência usando o portal do Azure

1. Em uma nova janela, abra o [portal do Azure](https://portal.azure.com).
2. Na barra esquerda, clique em **Azure Cosmos DB** ou em **Todos os serviços** na parte inferior, role até **Bancos de Dados** e, em seguida, clique em **Azure Cosmos DB**.
3. Selecione sua conta do Cosmos DB.
4. Na nova janela, clique em **Data Explorer** no menu de navegação.
5. Na nova janela, expanda o banco de dados e o contêiner e, em seguida, clique em **Dimensionamento e Configurações**.
6. Na nova janela, digite o novo valor de produtividade na caixa **Produtividade** e, em seguida, clique em **Salvar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Para definir a taxa de transferência usando a API do SQL do .NET

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Para definir a taxa de transferência usando a API do SQL do Java

Este trecho de código é retirado do arquivo OfferCrudSamples.java no repositório [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java). 

```Java
// find offer associated with this collection
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 10300;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="throughput-faq"></a>Perguntas frequentes sobre taxa de transferência

**Posso definir minha taxa de transferência com valor menor que 400 RU/s?**

400 RU/s é a taxa de transferência mínima disponível nos contêineres de partição única do Azure Cosmos DB (1000 RU/s é a mínima para contêineres particionados). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando um método econômico para desenvolver e testar o Cosmos DB, poderá usar o [Emulador do Azure Cosmos DB](local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

**Como defino a produtividade usando a API do MongoDB?**

Não há nenhuma extensão de API do MongoDB para definir a produtividade. A recomendação é usar a API do SQL, conforme mostrado em [Para definir a taxa de transferência usando a API do SQL do .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e atingir uma escala mundial com o Cosmos DB, consulte [Particionamento e escala com o Cosmos DB](partition-data.md).
