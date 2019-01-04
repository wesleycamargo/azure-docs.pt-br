---
title: Monitorar a integridade e alertas no Azure Stack | Microsoft Docs
description: Saiba como monitorar a integridade e alertas no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.openlocfilehash: 9a925a01cae75124dc56b0c2bc5cc931a6e04100
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721607"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorar a integridade e alertas no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack inclui monitoramento de infraestrutura recursos que ajudam você a exibir a integridade e alertas para uma região do Azure Stack. O **gerenciamento de região** fixação, por padrão no portal do administrador para a assinatura de provedor padrão, lista todas as regiões implantadas do Azure Stack. O bloco mostra o número de alertas críticos e de aviso ativos para cada região. O bloco é o ponto de entrada para a integridade e a funcionalidade de alerta do Azure Stack.

![O bloco de gerenciamento de região](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Entender a integridade do Azure Stack

O provedor de recursos de integridade gerencia a integridade e alertas. Componentes de infraestrutura do Azure Stack registram com o provedor de recursos de integridade durante a configuração e implantação do Azure Stack. Esse registro permite a exibição de integridade e alertas para cada componente. Integridade do Azure Stack é um conceito simple. Se existirem alertas para uma instância de um componente de registrado, o estado de integridade desse componente reflete a severidade do alerta ativa pior: aviso ou crítico.

## <a name="alert-severity-definition"></a>Definição de severidade do alerta

No Azure Stack emite alertas com apenas dois severidades: **aviso** e **críticos**.

- **Aviso**  
  Um operador pode resolver o alerta de aviso de maneira agendada. Normalmente o alerta não afeta as cargas de trabalho do usuário.

- **Crítico**  
  Um operador deve resolver o alerta crítico com urgência. Esses são problemas que causam impacto no momento ou em breve terá impacto sobre os usuários do Azure Stack.


## <a name="view-and-manage-component-health-state"></a>Exibir e gerenciar o estado de integridade do componente

Você pode exibir o estado de integridade de componentes no portal do administrador e por meio da API REST e PowerShell.

Para exibir o estado de integridade no portal, clique na região que você deseja exibir na **gerenciamento de região** lado a lado. Você pode exibir o estado de integridade de funções de infraestrutura e de provedores de recursos.

![Lista de funções de infraestrutura](media/azure-stack-monitor-health/image2.png)

Você pode clicar em um provedor de recursos ou uma função de infraestrutura para exibir informações mais detalhadas.

> [!WARNING]  
> Se você clicar uma função de infraestrutura e, em seguida, clique na instância de função, há opções para **inicie**, **reinicie**, ou **desligamento**. Não use essas ações ao aplicar atualizações a um sistema integrado. Além disso, faça **não** usar essas opções em um ambiente do Kit de desenvolvimento do Azure Stack. Essas opções só são projetadas para um ambiente de sistemas integrados, onde há mais de uma instância de função por função de infraestrutura. Reiniciar uma instância de função (especialmente AzS-Xrp01) no kit de desenvolvimento faz com que a instabilidade no sistema. Para obter assistência de solução de problemas, poste seu problema para o [Fórum do Azure Stack](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Exibir alertas

A lista de alertas ativos para cada região do Azure Stack está disponível diretamente a partir de **gerenciamento de região** folha. O primeiro bloco na configuração padrão é o **alertas** lado a lado, que exibe um resumo da crítica e alertas de aviso para a região. Você pode fixar o bloco de alertas, como qualquer outro bloco nessa folha, no painel para acesso rápido.

![Bloco que mostra um aviso de alertas](media/azure-stack-monitor-health/image3.png)

Selecionando a parte superior do **alertas** bloco, navegue para a lista de todos os alertas ativos para a região. Se você ou selecionar o **crítica** ou **aviso** item de linha dentro do bloco, navegue para uma lista filtrada de alertas (crítico ou aviso). 

O **alertas** folha suporta a capacidade de filtrar no status (ativo ou fechado) e gravidade (crítico ou aviso). A exibição padrão exibe todos os alertas ativos. Todos os alertas fechados são removidos do sistema após sete dias.

![Painel de filtro para filtrar por críticos ou status de aviso](media/azure-stack-monitor-health/alert-view.png)

O **API de modo de exibição** ação exibe a API REST que foi usado para gerar a exibição de lista. Essa ação fornece uma maneira rápida de se familiarizar com a sintaxe de API REST que você pode usar para alertas de consulta. Você pode usar essa API de automação ou para integração com seu datacenter existente, monitoramento, relatórios e soluções de emissão de tíquetes.

Você pode clicar em um alerta específico para exibir os detalhes do alerta. Os detalhes do alerta mostram todos os campos que estão associados com o alerta e habilitam a navegação rápida para o componente afetado e a origem do alerta. Por exemplo, o seguinte alerta ocorre se uma das instâncias de função de infraestrutura fica offline ou não está acessível.  

![A folha de detalhes do alerta](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Alertas de reparo

Você pode selecionar **reparo** em alguns alertas.

Quando selecionada, o **reparo** ação executa as etapas específicas para o alerta para tentar resolver o problema. Uma vez selecionado o status do **reparo** ação está disponível como uma notificação no portal.

![O reparo em andamento](media/azure-stack-monitor-health/repair-in-progress.png)

O **reparo** ação irá relatar a conclusão bem-sucedida ou Falha ao concluir a ação na mesma folha de notificação no portal.  Se uma ação de reparo falhar por um alerta, você pode executar novamente o **reparo** ação de detalhes alerta. Se a ação de reparo for concluída com êxito, **não tiver** executar novamente o **reparo** ação.

![O reparo for concluído com êxito](media/azure-stack-monitor-health/repair-completed.png)

Depois que a instância de função de infraestrutura estiver online novamente, esse alerta é fechado automaticamente. Muitos, mas não a cada alerta, será fechado automaticamente quando o problema subjacente é resolvido. Alertas que fornecem um botão de ação de reparo serão fechada automaticamente se o Azure Stack resolve o problema.  Para todos os outras alertas, selecione **fechar alerta** depois de executar as etapas de correção. Se o problema persistir, o Azure Stack gera um novo alerta. Se você resolver o problema, o alerta permanece fechado e requer que não há mais etapas.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar atualizações no Azure Stack](azure-stack-updates.md)

[Gerenciamento de região no Azure Stack](azure-stack-region-management.md)
