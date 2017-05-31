---
title: Provisionar a produtividade do Azure Cosmos DB | Microsoft Docs
description: "Saiba como definir a produtividade provisionada de sua coleção do Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5258ba0bc37442c983d91c5dd7435fd5fbefd56f
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="set-throughput-for-azure-cosmos-db-collections"></a>Definir a produtividade de coleções do Azure Cosmos DB

Você pode definir a produtividade de suas coleções do Azure Cosmos DB no portal do Azure ou usando os SDKs do cliente. 

A tabela a seguir lista a produtividade disponível para cada coleção:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Coleção de Partição Única</strong></p></td>
            <td valign="top"><p><strong>Coleção Particionada</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>2.500 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> Para definir coleções particionadas como um valor de taxa de transferência entre 2.500 RU/s e 10.000 RU/s, você deve usar o portal do Azure temporariamente. Esta funcionalidade ainda não está disponível nos SDKs.

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir a taxa de transferência usando o portal do Azure

1. Em uma nova janela, abra o [portal do Azure](https://portal.azure.com).
2. Na barra esquerda, clique em **Azure Cosmos DB** ou em **Mais Serviços** na parte inferior, role até **Bancos de dados** e, depois, clique em **Azure Cosmos DB**.
3. Selecione sua conta do Cosmos DB.
4. Na nova janela, em **coleções**, clique em **escala** conforme mostrado na seguinte captura de tela.
5. Na nova janela, selecione a coleção da lista suspensa, alteração de **Throughput** valor e, em seguida, clique em **salvar**.

    ![Captura de tela mostrando como alterar a taxa de transferência para uma coleção no portal do Azure navegando até sua conta e clicar em escala](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>Para definir a taxa de transferência usando o SDK do .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Perguntas frequentes sobre taxa de transferência

**Posso definir minha taxa de transferência com valor menor que 400 RU/s?**

400 RU/s é a produtividade mínima disponível em coleções de partição única do Cosmos DB (2500 RU/s é o mínimo para coleções particionadas). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando um método econômico para desenvolver e testar o Cosmos DB, poderá usar o [Emulador do Azure Cosmos DB](documentdb-nosql-local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e atingir uma escala mundial com o Cosmos DB, consulte [Particionamento e escala com o Cosmos DB](documentdb-partition-data.md).

