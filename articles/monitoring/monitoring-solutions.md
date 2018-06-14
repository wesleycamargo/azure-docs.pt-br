---
title: Soluções de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento do Azure são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas relacionadas a uma área de problema específica.  Este artigo fornece informações sobre como instalar e usar soluções de gerenciamento.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885261"
---
# <a name="management-solutions-in-azure"></a>Soluções de gerenciamento no Azure
As soluções de gerenciamento aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um aplicativo ou serviço específico. Este artigo fornece uma breve visão geral das soluções de gerenciamento no Azure e detalhes sobre como utilizá-las e instalá-las.

As soluções de gerenciamento geralmente coletam informações no Log Analytics e fornecem exibições e pesquisas de log para analisar os dados coletados. Adicionalmente, também podem aproveitar outros serviços, como a Automação do Azure, para executar ações relacionadas ao serviço ou aplicativo.

É possível adicionar soluções de gerenciamento à assinatura do Azure para quaisquer aplicativos e serviços que você utiliza. Normalmente, estão disponíveis gratuitamente, mas coletam dados que podem invocar encargos de uso. Além das soluções fornecidas pela Microsoft, os parceiros e clientes podem [criar soluções de gerenciamento](../monitoring/monitoring-solutions-creating.md) para serem usadas no próprio ambiente ou disponibilizadas aos clientes por meio da comunidade.

## <a name="using-management-solutions"></a>Usar soluções de gerenciamento
A página de **Visão Geral** para cada espaço de trabalho do Log Analytics exibe um bloco para cada solução instalada no espaço de trabalho. Clique no bloco da solução para abrir a exibição, que inclui uma análise mais detalhada dos dados coletados.

![Visão geral](media/monitoring-solutions/overview.png)

As soluções de gerenciamento podem conter vários tipos de recursos do Azure e é possível exibir todos os recursos incluídos em uma solução como qualquer outro recurso. Por exemplo, todas as pesquisas de logs incluídas na solução são incluídas com **Pesquisas Salvas** no espaço de trabalho. Você pode usar essas pesquisas ao executar uma análise ad hoc no Log Analytics.

## <a name="list-installed-management-solutions"></a>Listar soluções de gerenciamento instaladas 
Use o procedimento a seguir para listar as soluções de gerenciamento instaladas na assinatura.

1. Faça logon no Portal do Azure.
2. No painel esquerdo, selecione **Todos os serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **Filtrar**.
4. As soluções instaladas em todos os espaços de trabalho estão listadas. O nome da solução é seguido pelo nome do espaço de trabalho do Log Analytics no qual ele está instalado.
1. Use as caixas suspensas na parte superior da tela para filtrar por assinatura ou grupo de recursos.


![Listar todas as soluções](media/monitoring-solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumo. Esta página exibe quaisquer exibições do Log Analytics incluída na solução e fornece opções diferentes para a própria solução e ao espaço de trabalho. Visualize a página de resumo de uma solução usando um dos procedimentos acima para listar as soluções e, em seguida, clique no nome da solução.

![Propriedades da solução](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Localizar soluções de gerenciamento
É possível procurar e instalar as soluções de gerenciamento disponíveis da Microsoft e parceiros no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace). Realize uma [pesquisa por*Soluções de Gerenciamento*](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) para filtrar por soluções de gerenciamento e clique em qualquer item para obter mais detalhes.

![Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>Instalar uma solução de gerenciamento

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Instalar uma solução de gerenciamento do Azure Marketplace
Você pode usar qualquer um dos métodos a seguir para localizar e iniciar a instalação de uma solução de gerenciamento.

- Clique em **Obtenha agora** em uma solução de gerenciamento no [Azure Marketplace](#find-management-solutions).
- Na [lista de soluções para a assinatura](#list-installed-management-solutions), clique em **Adicionar**. À direita de **Soluções de gerenciamento**, clique em **Mais**. Localize a solução de gerenciamento desejada e clique em **Criar**.
- No Portal do Azure, selecione **Criar um recurso** > **Monitoramento + Gerenciamento** > **Ver todos**. À direita de **Soluções de gerenciamento**, clique em **Mais**. Localize a solução de gerenciamento desejada e clique em **Criar**.

Quando o processo de instalação for iniciado, você será solicitado a fornecer a configuração necessária, que varia para cada solução. Todos exigirão que você selecione um espaço de trabalho do Log Analytics onde a solução será instalada e onde os dados serão coletados. Talvez também seja necessário [especificar uma conta de Automação](#log-analytics-workspace-and-automation-account), se for exigida pela solução.

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução a partir da comunidade
Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure. É possível instalar essas soluções diretamente ou baixá-las para instalação posterior.

1. Siga o processo descrito no [Espaço de trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/). 
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações, como o grupo de recursos e o local, além dos valores de quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.


## <a name="log-analytics-workspace-and-automation-account"></a>Espaço de trabalho do Log Analytics e Conta de automação
Todas as soluções de gerenciamento exigem um [espaço de trabalho do Log Analytics](../log-analytics/log-analytics-manage-access.md) para armazenar dados coletados pela solução e hospedar as exibições e pequisas de logs. Algumas soluções também exigem uma [conta de Automação](../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. O espaço de trabalho e a conta devem atender aos seguintes requisitos.

* Cada instalação de uma solução somente pode usar um espaço de trabalho do Log Analytics e uma conta de Automação. É possível instalar a solução separadamente em vários espaços de trabalho.
* Se uma solução exigir uma conta de Automação, o espaço de trabalho do Log Analytics e a conta de Automação deverão estar vinculadas entre si. Um espaço de trabalho do Log Analytics só pode ser vinculado a uma Conta de automação e uma Conta de automação só pode ser vinculada a um espaço de trabalho do Log Analytics.
* Para ser vinculado, o espaço de trabalho do Log Analytics e a Conta de automação devem estar no mesmo grupo de recursos e região. A exceção é um espaço de trabalho na região Leste dos EUA e uma conta de Automação no Leste dos EUA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Criar um vínculo entre um espaço de trabalho do Log Analytics e a Conta de automação
Como você especifica o espaço de trabalho do Log Analytics e Conta de automação depende do método de instalação para sua solução.

* Ao instalar uma solução através do Azure Marketplace, um espaço de trabalho e uma conta de Automação serão solicitados. Se ainda não estiverem vinculados, o link entre eles será criado.
* Para soluções fora do Azure Marketplace, você deverá vincular o espaço de trabalho do Log Analytics e a Conta de automação antes de instalar a solução. Você poderá fazer isso selecionando qualquer solução no Azure Marketplace e selecionando o espaço de trabalho do Log Analytics e a Conta de automação. Não é necessário efetivamente instalar a solução porque o vínculo é criado quando o espaço de trabalho do Log Analytics e a conta Automação são selecionados. Depois que o vínculo é criado, você poderá usar esse espaço de trabalho do Log Analytics e a Conta de automação para qualquer solução.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação
Você pode verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
1. Role até a seção **Recursos Relacionados** do menu.
1. Se a configuração **Espaço de Trabalho** estiver habilitada, essa conta será vinculada a um espaço de trabalho do Log Analytics. Você pode clicar no **Espaço de trabalho** para exibir os detalhes do espaço de trabalho.

## <a name="remove-a-management-solution"></a>Remover uma solução de gerenciamento
Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-management-solutions). Clique no nome da solução para abrir a página de resumo e, em seguida, clique em **Excluir**.




## <a name="next-steps"></a>Próximas etapas
* Obtenha uma [lista de soluções de gerenciamento da Microsoft](monitoring-solutions-inventory.md).
* Saiba como [criar consultas](../log-analytics/log-analytics-log-searches.md) para analisar dados coletados pelas soluções de gerenciamento.

