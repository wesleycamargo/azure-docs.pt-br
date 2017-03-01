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
ms.date: 02/15/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: abdf0af8a85db19c68a0d74c0477d798c0fd03fc
ms.openlocfilehash: 8ff2fc6106438e35b93112a6dc97814ba79b06fc
ms.lasthandoff: 02/22/2017


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Definir a taxa de transferência de coleções do Azure DocumentDB

Você pode definir a taxa de transferência para suas coleções do DocumentDB no portal do Azure ou usando o SDK do cliente. 

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
2. No menu à esquerda, clique em **NoSQL (DocumentDB)** ou em **Mais Serviços** na parte inferior, role até **Bancos de dados** e clique em **NoSQL (DocumentDB)**.
3. Selecione sua conta do DocumentDB.
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

400 RU/s é a taxa de transferência mínima disponível em coleções de partição única do DocumentDB (2500 RU/s é o mínimo para coleções particionadas). Unidades de solicitação são definidas em intervalos de 100 RU/s, mas a taxa de transferência não pode ser definida como 100 RU/s ou qualquer valor menor que 400 RU/s. Se você estiver procurando por um método econômico para desenvolver e testar DocumentDB, você poderá usar o [emulador do DocumentDB](documentdb-nosql-local-emulator.md) gratuito, que pode ser implantado localmente sem custo adicional. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o provisionamento e escala planeta contínuo com o DocumentDB, veja [particionamento e escala com o DocumentDB](documentdb-partition-data.md).

