---
title: Gerenciar atualizações e patches para VMs do Microsoft Azure
description: Este artigo fornece uma visão geral de como usar a Automação do Azure – Gerenciamento de atualizações, para gerenciar atualizações e patches das VMs do Microsoft Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Gerenciar atualizações do Windows com Automação do Azure

O gerenciamento de atualizações permite que você gerencie atualizações e patches para suas máquinas virtuais.
Neste tutorial, você aprende a avaliar rapidamente o status das atualizações disponíveis, a agendar a instalação das atualizações necessárias e revisar os resultados da implantação e criar um alerta para verificar se as atualizações foram aplicadas com sucesso.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Carregar VM para gerenciamento de atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os seu crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os runbooks inspetor e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Primeiro, você precisa habilitar o Gerenciamento de atualizações da VM para este tutorial.

1. No portal do Azure, menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista
2. Na página VM, clique em **Atualizar gerenciamento**, clique na seção **Operações**. A janela **Habilitar Gerenciamento de Atualizações** é exibida.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM. Essa validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações. O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação.
Esse agente é usado para comunicar-se com a Automação do Azure e obter informações sobre o status de atualização. O agente requer que a porta 443 esteja aberta para se comunicar com o serviço de automação do Azure e realizar o download de atualizações.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Conta de Automação](./automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Gerenciamento de Atualizações** é exibida. Configure o local, o espaço de trabalho de Log analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo espaço de trabalho e conta de Automação devem ser usados.

![Habilitar a janela de solução de Gerenciamento de atualizações](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A habilitação da solução pode levar alguns minutos. Durante esse tempo, não feche a janela do navegador.
Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para o Log Analytics.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida.
Se alguma atualização estiver faltando, você verá uma lista de atualizações ausentes na guia **Atualizações ausentes**.

Selecione **INFORMATION LINK** na atualização para abrir o artigo de suporte para atualização em uma nova janela. Aqui você pode saber informações importantes sobre a atualização.

![Exibir o status de atualização](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Se você clicar em qualquer lugar na atualização, a janela **Pesquisa de Log** é exibida para a atualização selecionada. A consulta da pesquisa de log é predefinida para essa atualização específica. Modifique ou crie sua própria consulta para exibir informações detalhadas sobre as atualizações implantadas ou ausentes no ambiente.

![Exibir o status de atualização](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Configurar alertas

Nesta etapa, você deve configurar um alerta para informá-lo quando as atualizações foram implantadas com êxito. O alerta que você cria é baseado em uma consulta ao Log Analytics. Qualquer consulta personalizada pode ser escrita para alertas adicionais atenderem a vários cenários diferentes. No portal do Azure, navegue até **Monitor** e clique em **Criar Alerta**. isso abre a página **Criar regra**.

Em **1. Defina a condição de alerta**, clique em **+ Selecionar destino**. Em **Filtrar por tipo de recurso**, selecione **Log Analytics**. Escolha seu espaço de trabalho do Log Analytics e clique em **Pronto**.

![criar alerta](./media/automation-tutorial-update-management/create-alert.png)

Clique no botão **+ Adicionar critérios** para abrir a página **Lógica de sinal configurar**. Escolha **Pesquisa de log personalizada** na tabela. Insira a consulta a seguir na caixa de texto **Consulta de pesquisa**. Essa consulta retorna os computadores e a atualização, execute o nome que foi concluída no período de tempo especificado.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Insira **1** como o **Limite** para a lógica do Alerta. Ao terminar, clique em **Concluído**.

![Configurar sinal lógico](./media/automation-tutorial-update-management/signal-logic.png)

Em **2. Definir os detalhes do alerta**, dê ao alerta um nome e descrição fáceis. Defina a **Gravidade** para **informativo(Sev 2)** desde que o alerta seja para uma execução bem-sucedida.

![Configurar sinal lógico](./media/automation-tutorial-update-management/define-alert-details.png)

Em **3. Definir o grupo de ação**, clique em **+ Novo grupo de ação**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. Podem incluir, mas não estão limitados a notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../monitoring-and-diagnostics/monitoring-action-groups.md).

Na caixa **Nome do grupo de ação** caixa, dê a ele um nome fácil e curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

Em **Ações**, a ação de um nome fácil, como **Notificações por Email** em **TIPO DE AÇÃO** selecione **Email/SMS/Envio/Voz**. Em **DETALHES**, selecione **Editar detalhes**.

Na página **Email/SMS/Envio/Voz** página, dê um nome a ele. Marque a caixa de seleção **Email** e insira um endereço de email válido a ser usado.

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/configure-email-action-group.png)

Clique em **Ok** na página **Email/SMS/Envio/Voz** para fechá-la e clique em **Ok** para fechar a página **Adicionar grupo de ação**.

Você pode personalizar o assunto do email enviado clicando em **Assunto do Email** em **Personalizar ações** na página **Criar regra**. Ao concluir, clique em **Criar alerta**. Isso cria a regra que o alerta quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Agora que o alerta está configurado, agende uma implantação que siga o agendamento de versão e a janela de serviço para instalar atualizações.
Você pode escolher quais tipos de atualização deseja incluir na implantação.
Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a VM é reiniciada automaticamente.

Agende uma nova implantação de atualização para a VM, navegando de volta para **Gerenciamento de Atualizações** e marcando **Agendar implantação de atualização** na parte superior da tela.

Na tela **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome**: forneça um nome exclusivo para a implantação da atualização.

* **Sistema operacional** - escolha o sistema operacional de destino para a implantação de atualização.

* **Classificação de atualização** – selecione os tipos de software que a implantação de atualização incluiu na implantação. Para este tutorial, deixe todos os tipos selecionados.

  Os tipos de classificação são:

   |SO  |type  |
   |---------|---------|
   |Windows     | Atualizações críticas</br>Atualizações de segurança</br>Pacotes cumulativos de atualização</br>Feature packs</br>Service packs</br>Atualizações de definição</br>Ferramentas</br>Atualizações        |
   |Linux     | Atualizações críticas ou de segurança</br>Outras atualizações       |

   Para obter uma descrição dos tipos de classificação, consulte [classificações de atualização](automation-update-management.md#update-classifications).

* **Configurações agenda** - isso abre a página de Configurações da Agenda. A hora de início padrão é 30 minutos após a hora atual. É possível definir qualquer tempo de 10 minutos no futuro.

   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente.
   Selecione **Uma vez** em **Recorrência**. Deixe o padrão como 1 dia e clique em **OK**. Isso configura um agendamento recorrente.

* **Janela de manutenção (minutos)** – Deixe esse valor no valor padrão. Especifique o período de tempo em que deseja que a implantação da atualização ocorra. Essa configuração ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

![Tela de configurações de agenda de atualização](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Depois de concluir a configuração do agendamento, clique no botão **Criar**. Você é retornado ao painel de status. Selecione **Implantações de atualização agendadas** para mostrar a agenda de implantação que você criou.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
O status é exibido como **Em andamento** quando está em execução.
Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver falha em uma ou mais atualizações na implantação, o status será **Falha Parcial**.
Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para implantação específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

No bloco **Resultados da atualização** há um resumo do número total de atualizações e resultados de implantação na VM.
A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação.
A lista a seguir mostra os valores disponíveis:

* **Não foi tentada** – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito** – a atualização foi bem-sucedida
* **Falha** – Falha na atualização

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

Depois que a implantação de atualização for bem-sucedida, um email semelhante à imagem a seguir é enviado para mostrar o êxito da implantação.

![Configurar o grupo de ação de email](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Carregar VM para gerenciamento de atualizações
> * Exibir uma avaliação de atualização
> * Configurar alertas
> * Agendar uma implantação de atualização
> * Exibir os resultados de uma implantação

Prossiga para a visão geral da solução de Gerenciamento de Atualizações.

> [!div class="nextstepaction"]
> [Solução Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
