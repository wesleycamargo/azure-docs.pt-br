---
title: "Solução de problemas do portal do DocumentDB | Microsoft Docs"
description: Descubra como resolver problemas do DocumentDB no portal do Azure.
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d41cbded31d64525a3e04ef742091c8d87760416


---
# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Dicas de solução de problemas do portal do Azure DocumentDB
Este artigo descreve como resolver problemas do DocumentDB no portal do Azure. 

## <a name="resources-are-missing"></a>Recursos ausentes
**Sintoma**: coleções ou bancos de dados não estão presentes nas folhas do portal.

**Solução**: reduza o uso do aplicativo para operar abaixo da cota máxima de produtividade para a coleção. 

**Explicação**: o portal é um aplicativo como qualquer outro, fazendo chamadas para o banco de dados e a coleção do seu DocumentDB. Se as suas solicitações estiverem sofrendo restrições no momento devido a chamadas feitas de um aplicativo separado, o portal também poderá sofrer restrições, fazendo com que os recursos não apareçam no portal. Para solucionar o problema, resolva a causa do alto uso de produtividade e, em seguida, atualize a folha do portal. Informações sobre como medir e reduzir o uso da produtividade podem ser encontradas na seção [Produtividade](documentdb-performance-tips.md#throughput) do artigo [Dicas de desempenho](documentdb-performance-tips.md).

## <a name="pages-or-blades-wont-load"></a>Páginas ou folhas não carregam
**Sintoma**: páginas e folhas no portal não são exibidas.

**Solução**: reduza o uso do aplicativo para operar abaixo da cota máxima de produtividade para a coleção. 

**Explicação**: o portal é um aplicativo como qualquer outro, fazendo chamadas para o banco de dados e a coleção do seu DocumentDB. Se as suas solicitações estiverem sofrendo restrições no momento devido a chamadas feitas de um aplicativo separado, o portal também poderá sofrer restrições, fazendo com que os recursos não apareçam no portal. Para solucionar o problema, resolva a causa do alto uso de produtividade e, em seguida, atualize a folha do portal. Informações sobre como medir e reduzir o uso da produtividade podem ser encontradas na seção [Produtividade](documentdb-performance-tips.md#throughput) do artigo [Dicas de desempenho](documentdb-performance-tips.md).

## <a name="add-collection-button-is-disabled"></a>O botão Adicionar Coleção está desabilitado
**Sintoma**: na folha Banco de Dados, o botão **Adicionar Coleção** está desabilitado.

**Explicação**: se sua assinatura do Azure estiver associada aos créditos de benefício, como créditos gratuitos oferecidos de uma assinatura do MSDN, e você tiver usado todos os seus créditos do mês, não será possível criar coleções adicionais no DocumentDB.

**Solução**: remova o limite de gastos da sua conta.

1. No Portal do Azure, na barra de atalhos, clique em **Assinaturas**, clique na assinatura associada ao banco de dados do DocumentDB e, na folha **Assinatura**, clique em **Gerenciar**. 
    ![O DocumentDB oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. Na nova janela do navegador, você verá que não há créditos restantes. Clique no botão **Remover limite de gastos** para remover os gastos apenas do período de cobrança atual ou indefinidamente. Conclua o Assistente para adicionar ou confirmar suas informações de cartão de crédito. 
    ![O DocumentDB oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>O Gerenciador de Consultas é concluído com erros
Confira [Solucionar problemas do Gerenciador de Consultas](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>Não há dados disponíveis nos blocos de monitoramento
Confira [Solucionar problemas dos blocos de monitoramento](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>Nenhum documento retornado no Gerenciador de Documentos
Confira [Solucionando problemas do Gerenciador de Documentos](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Próximas etapas
Se você ainda estiver tendo problemas no portal, envie um email para o endereço [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para obter assistência ou faça uma solicitação de suporte no portal clicando em **Procurar**, **Ajuda + suporte** e, então, em **Criar solicitação de suporte**.




<!--HONumber=Nov16_HO3-->


