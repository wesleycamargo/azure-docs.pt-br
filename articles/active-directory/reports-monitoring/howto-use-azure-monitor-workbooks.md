---
title: Usar pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure | Microsoft Docs
description: Saiba como usar as pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013303"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como: Usar pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure

Você deseja:

- Entender o impacto de seu [políticas de acesso condicional](../conditional-access/overview.md) na experiência de entrada dos usuários?

- Solucionar problemas de falhas de entrada para obter uma melhor visão da integridade de logon da organização, bem como resolver problemas rapidamente?

- Saber quem está usando autenticações herdadas se conectem ao seu ambiente? Por [bloqueios de autenticação herdada](../conditional-access/block-legacy-authentication.md), você pode melhorar a proteção do seu locatário.


[Pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinar texto, consultas de análise, métricas do Azure e parâmetros em relatórios interativos avançados. O Azure Active Directory fornece pastas de trabalho de monitoramento que ajudam você a encontrar respostas para as perguntas acima.

Este artigo:

- Pressupõe que você esteja familiarizado com a [crie relatórios interativos com pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como você pode usar as pastas de trabalho do Azure Monitor sobre o monitoramento para responder às perguntas acima.
 


## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:

- Um locatário do Active Directory do Azure, com uma licença premium (P1/P2). Saiba como [obter uma licença premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Um [espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Acessar pastas de trabalho 

Para acessar as pastas de trabalho:

1. Entrar no seu [portal do Azure](https://portal.azure.com).

2. Na barra de navegação à esquerda, clique em **Azure Active Directory**.

3. No **Monitoring** seção, clique em **Insights**. 

    ![Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Clique em um relatório ou modelo, ou clique em **abrir** na barra de ferramentas. 

    ![Galeria](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Análise de entrada

Para acessar a pasta de trabalho de análise de entrar, clique em **entradas** na **uso** seção. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso

- Ação do usuário pendente

- Failure

Você pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicativos

- Usuários

![Galeria](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, você pode obter uma análise:

- Local padrão

    ![Galeria](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Galeria](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Usando a autenticação herdada entradas 


Para acessar as entradas usando [autenticação herdados](../conditional-access/block-legacy-authentication.md) pasta de trabalho, clique em **entradas usando a autenticação herdada** no **uso** seção. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso


Você pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicativos

- Usuários

- Protocolos 

![Galeria](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Galeria](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Entradas por acesso condicional 


Para acessar as entradas pelo [políticas de acesso condicional](../conditional-access/overview.md) pasta de trabalho, clique em **entradas pelo acesso condicional** no **acesso condicional** seção. 

Esta pasta de trabalho mostra a tendência de entradas desabilitadas.

Você pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicativos

- Usuários

![Galeria](./media/howto-use-azure-monitor-workbooks/49.png)


Para as entradas desabilitadas, você pode obter uma análise pelo status de acesso condicional.

![Status de acesso condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Entradas por controles de concessão

Para acessar as entradas pelo [conceder controles](../conditional-access/controls.md) pasta de trabalho, clique em **entradas por controles de concessão** no **acesso condicional** seção. 

Esta pasta de trabalho mostra as seguintes desabilitadas entrar tendências:

- Exigir MFA
 
- Exigir termos de uso

- Exigir a declaração de privacidade

- Outros


Você pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicativos

- Usuários

![Galeria](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Galeria](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de entradas

Use o **análise de falha de entradas** pasta de trabalho para solucionar problemas de erros com:

- Entradas
- Políticas de acesso condicional
- Modo de autenticação herdado. 


Para acessar as entradas por dados de acesso condicional, clique em **entradas usando a autenticação herdada** na **solucionar** seção. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso

- Ação pendente

- Failure


Você pode filtrar cada tendência por:

- Intervalo de tempo

- Aplicativos

- Usuários

![Galeria](./media/howto-use-azure-monitor-workbooks/52.png)


Para solucionar problemas de entradas, você pode obter uma análise:

- Principais erros

    ![Galeria](./media/howto-use-azure-monitor-workbooks/53.png)

- Entradas aguardando ação do usuário

    ![Galeria](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Próximos passos

* [Crie relatórios interativos com pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)