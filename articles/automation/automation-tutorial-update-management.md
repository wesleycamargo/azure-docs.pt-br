---
title: Gerenciar atualizações e patches para VMs do Microsoft Azure
description: Este artigo fornece uma visão geral de como usar o Gerenciamento de Atualizações da Automação do Azure para gerenciar atualizações e patches das VMs Windows do Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 92258ce7ea39a06f2af85efd9174b1b200710566
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216959"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Gerenciar atualizações do Windows com a Automação do Azure

Você pode usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches de suas máquinas virtuais. Neste tutorial, você aprende a avaliar rapidamente o status das atualizações disponíveis, a agendar a instalação das atualizações necessárias e revisar os resultados da implantação e criar um alerta para verificar se as atualizações foram aplicadas com sucesso.

Para obter informações sobre preços, consulte [Preços de automação do Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar seu crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta da Automação do Azure](automation-offering-get-started.md) para manter os runbooks observador e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Primeiro, habilite o Gerenciamento de Atualizações da VM para este tutorial:

1. No Portal do Azure, no menu esquerdo, selecione **Máquinas virtuais**. Selecione uma VM na lista.
2. Na página da VM, em **OPERAÇÕES**, selecione **Gerenciamento de atualização**. O painel **Habilitar Gerenciamento de Atualizações** é aberto.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM. Essa validação inclui verificar se há um espaço de trabalho do Azure Log Analytics e uma conta de automação vinculada e se a solução Gerenciamento de Atualizações está no espaço de trabalho.

Um espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações. O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação. Esse agente é usado para comunicar-se com a Automação do Azure e obter informações sobre o status de atualização. O agente requer que a porta 443 esteja aberta para se comunicar com o serviço de automação do Azure e realizar o download de atualizações.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* Uma [conta da Automação](./automation-offering-get-started.md)
* Um [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) (habilitado na VM)

Em **Gerenciamento de Atualizações**, defina o local, o espaço de trabalho do Log Analytics e a conta da Automação do Azure a serem usados. Em seguida, selecione **Habilitar**. Se essas opções não estão disponíveis, isso significa que outra solução de automação está habilitada para a VM. Nesse caso, o mesmo espaço de trabalho e conta da Automação devem ser usados.

![Habilitar a janela da solução Gerenciamento de Atualizações](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A habilitação da solução pode levar alguns minutos. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para o Log Analytics. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, o painel **Gerenciamento de Atualizações** será aberto. Se alguma atualização estiver faltando, uma lista de atualizações ausentes aparece na guia **Atualizações ausentes**.

Em **INFORMATION LINK**, selecione o link de atualização para abrir o artigo de suporte da atualização em uma nova janela. Você pode ver informações importantes sobre a atualização nessa janela.

![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Clique em qualquer lugar na atualização para abrir o painel **Pesquisa de Logs** da atualização selecionada. A consulta da pesquisa de log é predefinida para essa atualização específica. Modifique ou crie sua própria consulta para exibir informações detalhadas sobre as atualizações implantadas ou ausentes no ambiente.

![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurar alertas

Nesta etapa, defina um alerta para informá-lo quando as atualizações foram implantadas com êxito. O alerta que você cria é baseado em uma consulta ao Log Analytics. Você pode programar uma consulta personalizada para outros alertas a fim de atender a vários cenários diferentes. No Portal do Azure, vá para **Monitor**e selecione **Criar Alerta**. 

Em **Criar regra**, em **1. Defina a condição de alerta** e selecione **Selecionar destino**. Em **Filtrar por tipo de recurso**, selecione **Log Analytics**. Selecione seu espaço de trabalho do Log Analytics e selecione **Concluído**.

![Criar alerta](./media/automation-tutorial-update-management/create-alert.png)

Selecione **Adicionar critérios**.

Em **Configurar lógica de sinal**, na tabela, selecione **Pesquisa de logs personalizada**. Insira a seguinte consulta na caixa de texto **Consulta de pesquisa**:

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
Essa consulta retorna os computadores e a atualização, execute o nome que foi concluída no período de tempo especificado.

Em **Lógica de alerta**, para **Limite**, digite **1**. Quando tiver terminado, selecione **Concluído**.

![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

Em **2. Defina os detalhes do alerta** e insira um nome e uma descrição para o alerta. Defina a **Gravidade** para **informativo(Sev 2)** porque o alerta é para uma execução bem-sucedida.

![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

Em **3. Defina o grupo de ação** e selecione **Novo grupo de ação**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir, dentre outras, notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../monitoring-and-diagnostics/monitoring-action-groups.md).

Na caixa **Nome do grupo de ação**, digite um nome para o alerta e um nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

Em **Ações**, insira um nome para a ação, como **Notificações por Email**. Em **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**. Em **DETALHES**, selecione **Editar detalhes**.

No painel **Email/SMS/Push/Voz**, digite um nome. Marque a caixa de seleção **Email** e digite um endereço de email válido.

![Configurar um grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

No painel **Email/SMS/Push/Voice**, selecione **OK**. No painel **Adicionar grupo de ação**, selecione **OK**.

Para personalizar o assunto do email de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do email**. Quando terminar, selecione **Criar regra de alerta**. O alerta indica quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Em seguida, agende uma implantação que siga o agendamento de versão e o período de serviço para instalar as atualizações. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a VM é reiniciada automaticamente.

Para agendar uma nova implantação de atualização para a VM, vá para **Gerenciamento de atualizações** e selecione **Agendar implantação de atualização**.

Em **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome**: insira um nome exclusivo para a implantação da atualização.

* **Sistema operacional**: escolha o sistema operacional de destino para a implantação de atualização.

* **Classificação de atualização**: selecione os tipos de software que a implantação de atualização incluiu na implantação. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |type  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Pacotes cumulativos de atualização</br>Feature packs</br>Service packs</br>Atualizações de definição</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas ou de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, consulte [classificações de atualização](automation-update-management.md#update-classifications).

* **Agendar configurações**: isso abre o painel **Agendar configurações**. A hora de início padrão é 30 minutos após a hora atual. Você pode definir a hora de início para qualquer momento a partir de 10 minutos.

   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Em **Recorrência**, selecione **Uma vez**. Deixe o padrão como 1 dia e selecione **OK**. Isso configura um agendamento recorrente.

* **Janela de manutenção (minutos)**: deixe o valor padrão. Você pode definir o período de tempo no qual deseja que a implantação de atualização ocorra. Essa configuração ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

![Painel Configurações de agendamento de atualizações](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Quando terminar de configurar a agenda, selecione **Criar**. Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você criou.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** em **Gerenciamento de Atualizações**. O status é **Em andamento** se a implantação está em execução no momento. Quando a implantação for concluída, se for bem-sucedida, o status mudará para **Êxito**. Se houver falha em uma ou mais atualizações na implantação, o status será **Falha Parcial**.

Selecione a implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para uma implantação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Em **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação na VM. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação.

A lista a seguir mostra os valores disponíveis:

* **Não foi tentada**: a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito**: a atualização foi bem-sucedida.
* **Falha**: a atualização falhou.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

Quando a implantação de atualização é bem-sucedida, um email semelhante ao exemplo a seguir é enviado para mostrar o êxito da implantação:

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Integrar uma VM para o Gerenciamento de Atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

Prossiga para a visão geral da solução de Gerenciamento de Atualizações.

> [!div class="nextstepaction"]
> [Solução Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
