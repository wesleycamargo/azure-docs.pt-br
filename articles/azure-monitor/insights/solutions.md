---
title: Soluções de gerenciamento no Azure Monitor | Microsoft Docs
description: As soluções de gerenciamento no Azure Monitor são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas centradas em torno de uma área específica do problema.  Este artigo fornece informações sobre como instalar e usar soluções de gerenciamento.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: 0103225803ca98cb9d8e27eb57f150e0660cd7ab
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299114"
---
# <a name="management-solutions-in-azure-monitor"></a>Soluções de gerenciamento no Azure Monitor
As soluções de gerenciamento aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um aplicativo ou serviço específico. Este artigo fornece uma breve visão geral das soluções de gerenciamento no Azure e detalhes sobre como utilizá-las e instalá-las.

As soluções de gerenciamento geralmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. Adicionalmente, também podem aproveitar outros serviços, como a Automação do Azure, para executar ações relacionadas ao serviço ou aplicativo.

É possível adicionar soluções de gerenciamento ao Azure Monitor para quaisquer aplicativos e serviços usados. Normalmente, estão disponíveis gratuitamente, mas coletam dados que podem invocar encargos de uso. Além das soluções fornecidas pela Microsoft, os parceiros e clientes podem [criar soluções de gerenciamento](solutions-creating.md) para serem usadas no próprio ambiente ou disponibilizadas aos clientes por meio da comunidade.

## <a name="use-management-solutions"></a>Usar soluções de gerenciamento
Abra a página de **Visão geral** para que o workspace do Log Analytics exiba um bloco para cada solução instalada no workspace. 

1. Faça logon no Portal do Azure.
1. Abra **Todos os serviços** e localize **Monitor**.
1. No menu **Insights**, selecione **Mais**.
1. Use as caixas suspensas na parte superior da tela para alterar o workspace ou o intervalo de tempo usado para os blocos.
1. Clique no bloco para obter uma solução para abrir a exibição que inclui uma análise mais detalhada dos dados coletados.

![Visão geral](media/solutions/overview.png)

As soluções de gerenciamento podem conter vários tipos de recursos do Azure e é possível exibir todos os recursos incluídos em uma solução como qualquer outro recurso. Por exemplo, todas as consultas de log incluídas na solução estão listadas em **Consultas de Solução** no [Gerenciador de consultas](../log-query/get-started-portal.md#load-queries) e é possível usar essas consultas ao executar análises ad hoc com o Log Analytics.

## <a name="list-installed-management-solutions"></a>Listar soluções de gerenciamento instaladas 
Use o procedimento a seguir para listar as soluções de gerenciamento instaladas na assinatura.

1. Faça logon no Portal do Azure.
1. Abra **Todos os serviços** e localize **Soluções**.
4. As soluções instaladas em todos os workspaces estão listadas. O nome da solução é seguido pelo nome do workspace do Log Analytics no qual ele está instalado.
1. Use as caixas suspensas na parte superior da tela para filtrar por assinatura ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumo. Essa página exibe todas as exibições incluídas na solução e fornece opções diferentes para a própria solução e seu workspace. Visualize a página de resumo de uma solução usando um dos procedimentos acima para listar as soluções e, em seguida, clique no nome da solução.

![Propriedades da solução](media/solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Instalar uma solução de gerenciamento
As soluções de gerenciamento da Microsoft e parceiros estão disponíveis no [ Azure Marketplace ](https://azuremarketplace.microsoft.com). Você pode procurar soluções disponíveis e instalá-las usando o procedimento a seguir. Ao instalar uma solução, você deverá selecionar um [Workspace do Log Analytics](../platform/manage-access.md) no qual a solução será instalada e onde seus dados serão coletados.

1. Na [lista de soluções para a assinatura](#list-installed-management-solutions), clique em **Adicionar**. 
1. À direita de **Soluções de gerenciamento**, clique em **Mais**. 
1. Localize a solução de gerenciamento desejada e leia sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação for iniciado, você será solicitado a fornecer a configuração necessária, que varia para cada solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução a partir da comunidade
Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure. É possível instalar essas soluções diretamente ou baixá-las para instalação posterior.

1. Siga o processo descrito no [Workspace do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um workspace e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/). 
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações, como o grupo de recursos e o local, além dos valores de quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.


## <a name="log-analytics-workspace-and-automation-account"></a>Workspace do Log Analytics e Conta de automação
Todas as soluções de gerenciamento exigem um [workspace do Log Analytics](../platform/manage-access.md) para armazenar dados coletados pela solução e hospedar as exibições e pequisas de logs. Algumas soluções também exigem uma [conta de Automação](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. O workspace e a conta devem atender aos seguintes requisitos.

* Cada instalação de uma solução somente pode usar um workspace do Log Analytics e uma conta de Automação. É possível instalar a solução separadamente em vários workspaces.
* Se uma solução exigir uma conta de Automação, o workspace do Log Analytics e a conta de Automação deverão estar vinculadas entre si. Um workspace do Log Analytics só pode ser vinculado a uma Conta de automação e uma Conta de automação só pode ser vinculada a um workspace do Log Analytics.
* Para ser vinculado, o workspace do Log Analytics e a Conta de automação devem estar no mesmo grupo de recursos e região. A exceção é um workspace na região Leste dos EUA e uma conta de Automação no Leste dos EUA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Criar um link entre um workspace do Log Analytics e uma conta de Automação
Como você especifica o workspace do Log Analytics e Conta de automação depende do método de instalação para sua solução.

* Ao instalar uma solução através do Azure Marketplace, um workspace e uma conta de Automação serão solicitados. Se ainda não estiverem vinculados, o link entre eles será criado.
* Para soluções fora do Azure Marketplace, você deverá vincular o workspace do Log Analytics e a Conta de automação antes de instalar a solução. Você poderá fazer isso selecionando qualquer solução no Azure Marketplace e selecionando o workspace do Log Analytics e a Conta de automação. Não é necessário efetivamente instalar a solução porque o vínculo é criado quando o workspace do Log Analytics e a conta Automação são selecionados. Depois que o vínculo é criado, você poderá usar esse workspace do Log Analytics e a Conta de automação para qualquer solução.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifique o link entre um workspace do Log Analytics e uma conta de Automação
Você pode verificar o vínculo entre um workspace do Log Analytics e uma Conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
1. Role até a seção **Recursos Relacionados** do menu.
1. Se a configuração **Workspace** estiver habilitada, essa conta será vinculada a um workspace do Log Analytics. Você pode clicar no **Workspace** para exibir os detalhes do workspace.

## <a name="remove-a-management-solution"></a>Remover uma solução de gerenciamento
Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a página de resumo e, em seguida, clique em **Excluir**.




## <a name="next-steps"></a>Próximas etapas
* Obtenha uma [lista de soluções de gerenciamento da Microsoft](solutions-inventory.md).
* Saiba como [criar consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar dados coletados pelas soluções de gerenciamento.

