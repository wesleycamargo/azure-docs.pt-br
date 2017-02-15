---
title: Incrementar sua conta S1 do DocumentDB | Microsoft Docs
description: "Tire proveito da taxa de transferência maior em sua conta S1 de Banco de Dados de Documentos fazendo algumas alterações simples no Portal do Azure."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 6f373fb6-b0d9-4745-b17c-88e8bc5f906a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 166e0bc7838510b52324fd9a040acd21d55118c4


---
# <a name="supercharge-your-documentdb-account"></a>Incremente sua conta de Banco de Dados de Documentos
Siga estas etapas para aproveitar o aumento da produtividade da sua conta S1 no Banco de Dados de Documentos do Azure. Com pouco ou nenhum custo adicional, você pode aumentar a produtividade da sua conta S1 existente de 250 [RU/s](documentdb-request-units.md) para 400 RU/s ou mais!  

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Alterar para desempenho definido pelo usuário no Portal do Azure
1. Em seu navegador, navegue até o [**portal do Azure**](https://portal.azure.com). 
2. Clique em **Procurar** -> **DocumentDB (NoSQL)**, em seguida, selecione a conta do DocumentDB a modificar.   
3. Na lente **Bancos de Dados**, selecione o banco de dados a modificar, em seguida, na folha **Banco de Dados**, selecione a coleção com a camada de preços S1.
   
      ![Captura de tela da folha de banco de dados com uma coleção S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)
4. Na folha **Coleção**, clique em **Mais**, em seguida, clique em **Configurações**.   
5. Na folha **Configurações**, clique em **Camada de Preços** e observe que a estimativa de custo mensal de cada plano é exibida. Na folha **Escolher sua camada de preços**, clique em **Standard**, em seguida, clique em **Selecionar** para salvar a alteração.
   
      ![Faça uma captura de tela das Configurações de Banco de Dados de Documentos e escolha as folhas do tipo de preço](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
6. Voltando para a folha **Configurações**, a **Camada de Preços** é alterada para **Standard** e a caixa **Produtividade (RU/s)** é exibida com um valor padrão 400. Clique em **OK** para salvar as alterações. 
   
   > [!NOTE]
   > Você pode definir a produtividade entre 400 e 10.000 [Unidades de solicitação](documentdb-request-units.md)/segundo (RU/s). O **Resumo de Preços** na parte inferior da página é atualizado automaticamente para fornecer uma estimativa do custo mensal.
   > 
   > 
   
    ![Captura de tela da folha Configurações, mostrando em que lugar alterar o valor da taxa de transferência](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. Na folha **Banco de dados** , você pode verificar a taxa de transferência incrementada da coleção. 
   
    ![Captura de tela da folha Banco de dados com a coleção modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para saber mais sobre as alterações relacionadas às taxas de transferência definidas e predefinidas do usuário, confira a postagem no blog [DocumentDB: Everything you need to know about using the new pricing options (DocumentDB: tudo o que você precisa saber sobre como usar as novas opções de preços)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

## <a name="next-steps"></a>Próximas etapas
Se determinar que precisa de uma taxa de transferência maior (mais de 10.000 RU/s) ou mais armazenamento (mais de 10 GB), você poderá criar uma coleção particionada. Para criar uma coleção particionada, confira [Criar uma coleção](documentdb-create-collection.md).




<!--HONumber=Nov16_HO3-->


