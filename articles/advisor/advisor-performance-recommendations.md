---
title: "Recomendações de desempenho do Azure Advisor | Microsoft Docs"
description: "Use o Advisor para otimizar o desempenho das implantações do Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: c350134e8cea5771900e2e88b02b572c20e9d5bd

---

# <a name="advisor-performance-recommendations"></a>Recomendações de desempenho do Advisor

As recomendações de desempenho do Advisor ajudam a aprimorar e aumentar a velocidade e a capacidade de resposta de aplicativos críticos aos negócios. Você pode obter recomendações de desempenho usando o Advisor na guia **Desempenho** do painel do Advisor.

![guia Desempenho do Advisor](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>Recomendações do banco de dados SQL

O Advisor fornece uma exibição consistente e consolidada de recomendações para todos os seus recursos do Azure. Ele se integra ao Advisor do Banco de Dados SQL para fornecer recomendações de melhoria de desempenho para o banco de dados do SQL Azure. O Advisor do Banco de Dados SQL avalia o desempenho dos bancos de dados do SQL Azure analisando o histórico de uso. Desse modo, ele oferece recomendações que são mais adequadas para execução da carga de trabalho característica do banco de dados. 

> [!NOTE]
> Para obter recomendações, um banco de dados precisa ter aproximadamente uma semana de uso e dentro dessa semana deve haver atividade consistente. O Advisor do Banco de Dados SQL pode ser otimizado com mais facilidade para padrões de consulta consistentes do que para intermitências irregulares de atividade.

Para saber mais sobre o Advisor do banco de dados SQL, veja [Advisor do Banco de Dados SQL](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Recomendações do banco de dados SQL](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Recomendações do Cache Redis

O Azure Advisor identifica instâncias do Cache Redis onde o desempenho pode ser negativamente afetado pelo alto uso da memória, pela carga do servidor, pela largura de banda da rede ou pelo grande número de conexões de cliente. Ele também fornece as práticas recomendadas para evitar possíveis problemas. Para saber mais sobre recomendações do Cache Redis, veja [Advisor do Cache Redis](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="app-services-recommendations"></a>Recomendações dos Serviços de Aplicativos

O Azure Advisor integra as práticas recomendadas para melhorar sua experiência com os Serviços de Aplicativos e descobrir recursos relevantes de plataforma. Os exemplos de recomendações dos Serviços de Aplicativos são:
- Detecção de instâncias onde os recursos de memória ou CPU são esgotados por tempos de execução de aplicativo com opções de redução
- Detecção de instâncias onde a disposição de recursos como aplicativos Web e bancos de dados pode melhorar o desempenho e reduzir custos. 

Para saber mais sobre recomendações de Serviços de Aplicativos, veja [Práticas recomendadas para o Serviço de Aplicativo do Azure](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Recomendações dos Serviços de Aplicativos](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como acessar as recomendações de desempenho no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Mais serviços**, no painel do menu de serviço, role para baixo até **Monitoramento e Gerenciamento** e clique em **Azure Advisor**. Isso inicia o painel do Advisor. 
3. No painel do Advisor, clique na guia **Desempenho** e selecione a assinatura para a qual deseja receber recomendações.

> [!NOTE]
> O Advisor gera recomendações para assinaturas em que você recebeu a função de **Proprietário, Colaborador ou Leitor**.

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos para saber mais sobre as recomendações do Advisor:

-  [Introdução ao Advisor](advisor-overview.md)
-  [Introdução ao Advisor](advisor-get-started.md)
-  [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
-  [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de custo do Advisor](advisor-performance-recommendations.md)



<!--HONumber=Nov16_HO3-->


