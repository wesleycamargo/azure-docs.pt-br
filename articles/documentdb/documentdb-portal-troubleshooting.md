---
title: Solucionando problemas do portal de DocumentDB | Microsoft Docs
description: Descubra como resolver problemas do DocumentDB no portal do Azure.
services: documentdb
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: monicar

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: mimig

---
# Dicas de solução de problemas do portal do Azure DocumentDB
Este artigo descreve como resolver problemas do DocumentDB no portal do Azure.

## Recursos ausentes
**Sintoma**: coleções ou bancos de dados não estão presentes nas folhas do portal.

**Solução**: reduza o uso do aplicativo para operar abaixo da cota máxima de produtividade para a coleção.

**Explicação**: o portal é um aplicativo como qualquer outro, fazendo chamadas para o banco de dados e a coleção do seu DocumentDB. Se as suas solicitações estiverem sofrendo restrições no momento devido a chamadas feitas de um aplicativo separado, o portal também poderá sofrer restrições, fazendo com que os recursos não apareçam no portal. Para solucionar o problema, resolva a causa do alto uso de produtividade e, em seguida, atualize a folha do portal. Informações sobre como medir e reduzir o uso da produtividade podem ser encontradas na seção [Produtividade](documentdb-performance-tips.md#throughput) do artigo [Dicas de desempenho](documentdb-performance-tips.md).

## Páginas ou folhas não carregam
**Sintoma**: páginas e folhas no portal não são exibidas.

**Solução**: reduza o uso do aplicativo para operar abaixo da cota máxima de produtividade para a coleção.

**Explicação**: o portal é um aplicativo como qualquer outro, fazendo chamadas para o banco de dados e a coleção do seu DocumentDB. Se as suas solicitações estiverem sofrendo restrições no momento devido a chamadas feitas de um aplicativo separado, o portal também poderá sofrer restrições, fazendo com que os recursos não apareçam no portal. Para solucionar o problema, resolva a causa do alto uso de produtividade e, em seguida, atualize a folha do portal. Informações sobre como medir e reduzir o uso da produtividade podem ser encontradas na seção [Produtividade](documentdb-performance-tips.md#throughput) do artigo [Dicas de desempenho](documentdb-performance-tips.md).

## O botão Adicionar Coleção está desabilitado
**Sintoma**: na folha Banco de Dados, o botão **Adicionar Coleção** está desabilitado.

**Explicação**: se sua assinatura do Azure estiver associada aos créditos de benefício, como créditos gratuitos oferecidos de uma assinatura do MSDN, e você tiver usado todos os seus créditos do mês, não será possível criar coleções adicionais no DocumentDB.

**Solução**: remova o limite de gastos da sua conta.

1. No portal do Azure, na barra de atalhos, clique em **Assinaturas**, clique na assinatura associada ao banco de dados do DocumentDB e, na folha **Assinatura**, clique em **Gerenciar**. ![O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. Na nova janela do navegador, você verá que não há créditos restantes. Clique no botão **Remover limite de gastos** para remover os gastos apenas do período de cobrança atual ou indefinidamente. Conclua o Assistente para adicionar ou confirmar suas informações de cartão de crédito. ![O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## O Gerenciador de Consultas é concluído com erros
Confira [Solucionar problemas do Gerenciador de Consultas](documentdb-query-collections-query-explorer.md#troubleshoot).

## Não há dados disponíveis nos blocos de monitoramento
Confira [Solucionar problemas dos blocos de monitoramento](documentdb-monitor-accounts.md#troubleshooting).

## Nenhum documento retornado no Gerenciador de Documentos
Confira [Solucionando problemas do Gerenciador de Documentos](documentdb-view-json-document-explorer.md#troubleshoot).

## Próximas etapas
Se você ainda estiver tendo problemas no portal, envie um email para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para obter assistência ou faça uma solicitação de suporte no portal clicando em **Procurar**, **Ajuda + suporte** e em **Criar solicitação de suporte**.

<!---HONumber=AcomDC_0831_2016-->