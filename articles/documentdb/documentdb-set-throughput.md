---
title: "Taxa de transferência de provisão para o Azure DocumentDB | Microsoft Docs"
description: "Saiba como definir a taxa de transferência provisionada para sua coleção do DocumentDB."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 9919f9430626b02635272599e7d1f3f09e0068cd
ms.openlocfilehash: ae83dc01113e72576a35aa57acbac3b71d87e9f8


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Definir a taxa de transferência de coleções do Azure DocumentDB

Você pode definir a taxa de transferência para suas coleções do DocumentDB no portal do Azure ou usando o SDK do cliente. 

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir a taxa de transferência usando o portal do Azure

1. Em uma nova janela, abra o [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **NoSQL (DocumentDB)** ou em **Mais Serviços** na parte inferior, role até **Bancos de dados** e clique em **NoSQL (DocumentDB)**.
3. Selecione sua conta do DocumentDB.
4. Na nova janela, em **coleções**, clique em **escala** conforme mostrado na seguinte captura de tela.
5. Na nova janela, selecione a coleção da lista suspensa, alteração de **Throughput** valor e, em seguida, clique em **salvar**.

    ![Captura de tela mostrando como alterar a taxa de transferência para uma coleção no portal do Azure navegando até sua conta e clicar em escala](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

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

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e escala planeta contínuo com o DocumentDB, veja [particionamento e escala com o DocumentDB](documentdb-partition-data.md).


<!--HONumber=Feb17_HO2-->


