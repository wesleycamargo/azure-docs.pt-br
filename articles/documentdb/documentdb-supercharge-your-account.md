---
title: Migrar sua conta S1 do DocumentDB | Microsoft Docs
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
ms.date: 01/04/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: f8190faec63c51fd3e6c69c16e2e1041ac67304e
ms.openlocfilehash: 36c13458d17651b911ea60d6460e4342f40e87a1


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>Migrar sua conta DocumentDB S1, S2 ou S3 do DocumentDB
Siga estas etapas para aproveitar o aumento da taxa de transferência da sua conta do Azure DocumentDB movendo o tipo de preço Standard. Com pouco ou nenhum custo adicional, você pode aumentar a produtividade da sua conta S1 existente de 250 [RU/s](documentdb-request-units.md) para 400 RU/s ou mais! Todas as contas Standard se beneficiam da capacidade de dimensionar a taxa de transferência para atender às necessidades dos aplicativos. Você não precisa mais escolher dentre as opções predefinidas de taxa de transferência; é possível fazer o dimensionamento sempre que você precisar atingir a taxa de transferência necessária para seu aplicativo. 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Alterar para desempenho definido pelo usuário no Portal do Azure
1. Em seu navegador, navegue até o [**portal do Azure**](https://portal.azure.com). 
2. No menu à esquerda, clique em **NoSQL (DocumentDB)** ou em **Mais Serviços**, role até **Bancos de dados** e escolha **NoSQL (DocumentDB)**.   
3. Na folha **NoSQL (DocumentDB)**, escolha a conta a ser modificada.
4. Na nova folha, no menu em **Coleções**, clique em **Escala**. 

      ![Faça uma captura de tela das Configurações de Banco de Dados de Documentos e escolha as folhas do tipo de preço](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. Como mostrado na captura de tela abaixo, faça o seguinte: 

 - Na nova folha, use o menu suspenso para selecionar a coleção com o tipo de preço S1, S2 ou S3. 
 - Clique em **Tipo de Preço S1**, **S2** ou **S3**.
 - Na folha **Escolher sua camada de preços**, clique em **Standard**, em seguida, clique em **Selecionar** para salvar a alteração.
   
6. De volta na folha **Escala**, a caixa **Taxa de Transferência (RU/s)** é exibida com um valor padrão de 400 e o **Tipo de Preço** é alterado para **Standard**.  Você pode alterar a taxa de transferência para qualquer valor necessário para o aplicativo. É recomendável usar o [Planejador de capacidade do DocumentDB](https://www.documentdb.com/capacityplanner) para determinar quantas RU/s ([Unidades de solicitação](documentdb-request-units.md)/segundo) seu aplicativo precisa. A área **Estimativa da conta mensal** na parte inferior da página é atualizada automaticamente para fornecer uma estimativa do custo mensal. Clique em **Salvar** para salvar as alterações. 
      
    ![Captura de tela da folha Configurações, mostrando em que lugar alterar o valor da taxa de transferência](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. A coleção é dimensionada para atender aos novos requisitos e uma mensagem de êxito é exibida.  
   
    ![Captura de tela da folha Banco de dados com a coleção modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para saber mais sobre as alterações relacionadas às taxas de transferência definidas e predefinidas do usuário, confira a postagem no blog [DocumentDB: Everything you need to know about using the new pricing options (DocumentDB: tudo o que você precisa saber sobre como usar as novas opções de preços)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como particionar os dados e atingir a escala global com o DocumentDB em [Particionamento e dimensionamento no Azure DocumentDB](documentdb-partition-data.md).



<!--HONumber=Jan17_HO2-->


