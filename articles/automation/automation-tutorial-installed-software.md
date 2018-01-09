---
title: "Descobrir qual software está instalado em seus computadores com a Automação do Azure | Microsoft Docs"
description: "Use o inventário para descobrir qual software está instalado nos computadores em seu ambiente."
services: automation
keywords: "inventário, automação, alteração, acompanhamento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: hero-article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 49eedd975e456d97f9eee44f9b00993b115ad4fa
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Descobrir qual software está instalado em computadores Azure e não Azure

Neste tutorial, você aprenderá como descobrir qual software está instalado em seu ambiente. É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Exibir software instalado
> * Pesquisar software instalado nos registros de inventário

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os runbooks inspetor e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de alterações e Inventário

Primeiro, é necessário habilitar o Controle de alterações e Inventário para sua VM neste tutorial. Se você já habilitou outra solução de automação para uma VM, esta etapa não é necessária.

1. No menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista
2. No menu à esquerda, na seção **Operações**, clique em **Inventário**. A página **Habilitar Controle de alterações e Inventário** é aberta.

A validação é realizada para determinar se o Inventário está habilitado para esta VM.
A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços como Inventário.
O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Worker.
Esse agente é usado para comunicar-se com a VM e obter informações sobre o software instalado.
O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação.

Se esses pré-requisitos não forem atendidos, será exibida uma faixa oferecendo a opção para habilitar a solução.

![Faixa de configuração integrada do inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Para habilitar a solução, clique na faixa.
Se algum dos seguintes pré-requisitos estiver ausente após a validação, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automação](./automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Controle de Alterações e Inventário** é aberta. Configure o local, o espaço de trabalho do Log Analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo espaço de trabalho e conta de Automação devem ser usados.

![Habilitar janela de solução de controle de alterações](./media/automation-tutorial-installed-software/installed-software-enable.png)

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador.
Depois que a solução for habilitada, as informações sobre o software instalado e as alterações na VM fluirão para o Log Analytics.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="view-installed-software"></a>Exibir software instalado

Depois que a solução Controle de alterações e Inventário estiver habilitada, será possível exibir os resultados na página **Inventário**.

Na sua VM, selecione **Inventário** em **OPERAÇÕES**.

Na página **Inventário**, clique na guia **Software**.

Na guia **Software**, há uma lista de tabelas que o software havia encontrado. O software é agrupado por nome e versão do software.

Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome, versão e editor do software, hora da última atualização (a atualização mais recente relatada por um computador no grupo) e computadores (a contagem de computadores com esse software).

![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

Clique em uma linha para exibir as propriedades do registro de software e os nomes dos computadores com esse software.

Para procurar um software ou grupo de software específico, é possível pesquisar na caixa de texto diretamente acima dessa lista de software.
O filtro permite pesquisar com base no nome, versão ou editor do software.

Por exemplo, pesquisar "Contoso" retorna todos os softwares com um nome, um editor ou versão que contém "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar software instalado nos registros de inventário

O Inventário gera dados de log que são enviados para o Log Analytics. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da janela **Inventário**.

Os dados de inventário são armazenados com o tipo **ConfigurationData**.
A consulta de exemplo do Log Analytics a seguir retorna os Editores que contêm "Microsoft" e o número de registros de software (agrupados por SoftwareName e Computador) para cada Editor.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Para saber mais sobre como executar e pesquisar arquivos de log no Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventário de computador único

Para ver o inventário de software para um único computador, é possível acessar o Inventário da página de recursos de VM do Azure ou usar o Log Analytics para filtrar até o computador correspondente. A consulta de exemplo do Log Analytics a seguir retorna a lista de software para um computador chamado ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como exibir o inventário de software, por exemplo, como:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Exibir software instalado
> * Pesquisar software instalado nos registros de inventário

Passe para a visão geral da solução de Controle de alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Solução de gerenciamento de alterações e inventário](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)