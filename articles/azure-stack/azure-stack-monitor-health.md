---
title: Monitorar a integridade e alertas na pilha do Azure | Microsoft Docs
description: Saiba como monitorar a integridade e alertas na pilha do Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: 446df7922422ccfcf3fbb92ecf153c6dec2f6197
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorar a integridade e alertas na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Pilha do Azure inclui recursos que permitem exibir a integridade e alertas para uma região do Azure pilha de monitoramento de infraestrutura. O **gerenciamento região** lista de fixação, por padrão no portal do administrador para a assinatura do provedor padrão, todas as regiões implantadas da pilha do Azure. O bloco mostra o número de alertas críticos e de aviso ativos para cada região e é o ponto de entrada para a integridade e a funcionalidade de alerta da pilha do Azure.

 ![O bloco de gerenciamento de região](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Entender a integridade na pilha do Azure

 Integridade e os alertas são gerenciadas pelo provedor de recursos de integridade. Componentes da infraestrutura do Azure pilha registram com o provedor de recursos de integridade durante a configuração e implantação de pilha do Azure. Esse registro permite que a exibição de integridade e alertas para cada componente. Integridade na pilha do Azure é um conceito simple. Se existirem alertas para uma instância de um componente de registrado, o estado de integridade desse componente reflete a pior severidade do alerta ativa; Aviso ou crítico.
 
 ## <a name="view-and-manage-component-health-state"></a>Exibir e gerenciar o estado de integridade do componente
 
 Como um operador de pilha do Azure, você pode exibir o estado de integridade dos componentes no portal do administrador e por meio da API REST e PowerShell.
 
Para exibir o estado de integridade no portal, clique na região que você deseja exibir o **gerenciamento região** lado a lado. Você pode exibir o estado de integridade de funções de infraestrutura e de provedores de recursos.

![Lista de funções de infraestrutura](media/azure-stack-monitor-health/image2.png)

Você pode clicar em um provedor de recursos ou uma função de infraestrutura para exibir informações mais detalhadas.

> [!WARNING]
>Se você clicar uma função de infraestrutura e, em seguida, clique na instância de função, há opções para iniciar, reiniciar ou desligar. Não use essas ações quando aplicar atualizações a um sistema integrado. Além disso, **não** usar essas opções em um ambiente do Kit de desenvolvimento de pilha do Azure. Essas opções são desenvolvidas somente para um ambiente de sistemas integrados, onde há mais de uma instância de função por função de infraestrutura. Reiniciar uma instância de função (especialmente AzS-Xrp01) no kit de desenvolvimento provoca instabilidade no sistema. Para obter ajuda de solução de problemas, lançar seu problema para o [Fórum do Azure pilha](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Exibir alertas

A lista de alertas ativos para cada região de pilha do Azure está disponível diretamente a partir de **gerenciamento região** folha. O primeiro bloco na configuração padrão é o **alertas** lado a lado, que exibe um resumo dos críticos e alertas de aviso para a região. Você pode fixar o bloco de alertas, como qualquer outro bloco nesta folha, no painel de acesso rápido.   

![Alertas de bloco que mostra um aviso](media/azure-stack-monitor-health/image3.png)

Selecionando a parte superior do **alertas** bloco, navegue para a lista de todos os alertas ativos para a região. Se você selecionar o **crítico** ou **aviso** item de linha em bloco, navegue para uma lista filtrada de alertas (crítico ou aviso). 

![Alertas de aviso filtrado](media/azure-stack-monitor-health/image4.png)
  
O **alertas** lâmina oferece suporte à capacidade de filtrar gravidade (crítico ou aviso) e no status (ativo ou fechado). A exibição padrão exibe todos os alertas ativos. Todos os alertas fechados são removidos do sistema após sete dias.

![Painel de filtro para filtrar por crítico ou de status de aviso](media/azure-stack-monitor-health/image5.png)

O **exibição API** ação exibe a API REST que foi usado para gerar a exibição de lista. Essa ação fornece uma maneira rápida de se familiarizar com a sintaxe da API REST que você pode usar para alertas de consulta. Você pode usar esta API na automação ou para a integração com o data center existente de monitoramento, relatórios e soluções de emissão de tíquetes. 

![A opção de API do modo de exibição que mostra a API REST](media/azure-stack-monitor-health/image6.png)

Você pode clicar em um alerta específico para exibir os detalhes do alerta. Os detalhes do alerta mostram todos os campos que estão associados com o alerta e permitem a navegação rápida para o componente afetado e a origem do alerta. Por exemplo, o seguinte alerta ocorre se uma das instâncias de função de infra-estrutura fica offline ou não está acessível.  

![A folha de detalhes do alerta](media/azure-stack-monitor-health/image7.png)

Depois que a instância de função de infraestrutura está novamente online, este alerta é fechado automaticamente. Muitas, mas não a cada alerta será fechado automaticamente quando o problema subjacente é resolvido. Recomendamos que você selecione **fechar alerta** depois de executar as etapas de correção. Se o problema persistir, a pilha do Azure gera um novo alerta. Se você resolver o problema, o alerta permanece fechado e não requer nenhuma ação adicional.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar atualizações na pilha do Azure](azure-stack-updates.md)

[Gerenciamento de região na pilha do Azure](azure-stack-region-management.md)
