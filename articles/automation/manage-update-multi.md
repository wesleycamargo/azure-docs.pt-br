---
title: "Gerenciar atualizações para várias máquinas virtuais do Azure | Microsoft Docs"
description: "Este tópico descreve como gerenciar atualizações para máquinas virtuais do Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: magoedte;gwallace
ms.openlocfilehash: 9bd6a290320958e2bc534ceb0abbcb647084920b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Gerenciar atualizações de vários computadores

Você pode usar o gerenciamento de atualizações para gerenciar atualizações e patches para seus computadores Windows e Linux. A partir da sua conta da [Automação do Azure](automation-offering-get-started.md), é possível:

- Carregar máquinas virtuais.
- Avaliar o status das atualizações disponíveis.
- Agendar a instalação de atualizações necessárias.
- Examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais nas quais o gerenciamento de atualização está habilitado.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de atualizações, você precisa de:

* Uma execução de Automação do Azure como conta. Para obter instruções sobre como criar uma, consulte [Introdução à Automação do Azure](automation-offering-get-started.md).

* Uma máquina virtual ou um computador com um dos sistemas operacionais com suporte instalado.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Há suporte para o gerenciamento de atualizações nos seguintes sistemas operacionais.

### <a name="windows"></a>Windows

* Windows Server 2008 e superior e implantações de atualização no Windows Server 2008 R2 SP1 e superior. Não há suporte para o Nano Server.

  O suporte à implantação de atualizações para o Windows Server 2008 R2 SP1 requer o .NET Framework 4.5 e Windows Management Framework 5.0 ou posterior.

* Não há suporte para sistemas operacionais clientes do Windows.

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update.

> [!NOTE]
> O System Center Configuration Manager não pode gerenciar o agente do Windows simultaneamente.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) e 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)  
* Ubuntu 12.04 LTS e posterior (x86/x64)   

> [!NOTE]  
> Para prevenir que atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para obter mais informações, consulte o [tópico de Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Os agentes do Linux devem ter acesso a um repositório de atualização.

Essa solução não dá suporte a um Agente do OMS para Linux configurado para reportar a vários espaços de trabalho do Operations Management Suite.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar o Gerenciamento de Atualizações para máquinas virtuais do Azure

1. No Portal do Azure, abra a Conta de automação.
2. No painel esquerdo, selecione **Gerenciamento de atualização**.
3. Na parte superior da janela, selecione **Adicionar VM do Azure**.
   ![Guia Adicionar VM do Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Selecione uma máquina virtual a ser carregada. A caixa de diálogo **Habilitar Gerenciamento de Atualizações** é exibida.
5. Selecione **Habilitar**.

   ![Caixa de diálogo Habilitar Gerenciamento de Atualizações](./media/manage-update-multi/update-enable.png)

O gerenciamento de atualizações está habilitado para sua máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitar o gerenciamento de atualizações para computadores e máquinas virtuais que não são Azure

Para obter instruções sobre como habilitar o gerenciamento de atualizações para computadores e máquinas virtuais Windows que não são Azure, confira [Conectar computadores Windows ao serviço Log Analytics no Azure](../log-analytics/log-analytics-windows-agent.md).

Para obter instruções sobre como habilitar o gerenciamento de atualizações para computadores e máquinas virtuais Linux que não são Azure, confira [Conectar computadores Linux ao Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Exibir computadores conectados à sua conta de automação
Depois de habilitar o gerenciamento de atualizações para suas máquinas, exiba suas informações clicando em **Computadores**. Informações do computador, como *Nome*, *Conformidade*, *Ambiente*, *Tipo de SO*, *Atualizações Críticas e de Segurança*  e *Outras Atualizações* estão disponíveis. 

  ![Guia Exibir computadores](./media/manage-update-multi/update-computers-tab.png)

Talvez os computadores que foram recentemente habilitados para o gerenciamento de atualizações ainda não foram avaliados. O estado de conformidade desses computadores teria um status de *Não avaliado*.  Confira aqui uma lista de valores para o estado de conformidade:
* Compatível: computadores com todas as atualizações críticas ou de segurança.
* Não compatível: computadores que não têm pelo menos uma atualização crítica ou de segurança.
* Não avaliado: os dados da avaliação de atualização não foram recebidos do computador dentro do período de tempo esperado.  Para computadores Linux, nas últimas três horas, e para computadores com Windows, nas últimas 12 horas.  

## <a name="view-an-update-assessment"></a>Exibir uma avaliação de atualização

Depois que o gerenciamento de atualizações for habilitado, a caixa de diálogo **Gerenciamento de atualizações** será exibida. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

## <a name="collect-data"></a>Coletar dados

Os agentes instalados em máquinas virtuais e computadores coletam dados sobre atualizações e os enviam para o gerenciamento de atualizações do Azure.

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas às quais essa solução dá suporte:

| Fonte conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes Linux e inicia a instalação das atualizações necessárias em distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado. |
| Conta de Armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência de coleta

Para cada computador gerenciado do Windows, uma verificação é executada duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Em caso positivo, uma verificação de conformidade é iniciada. Para cada computador Linux gerenciado, uma verificação é executada a cada 3 horas.

Pode demorar de 30 minutos a 6 horas para o painel exibir dados atualizados em computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço.
Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para uma ou mais máquinas virtuais selecionando em **Agendar implantação de atualização** na parte superior da caixa de diálogo **Gerenciamento de atualizações**. No painel **Nova implantação de atualização**, especifique o seguinte:

* **Nome**: forneça um nome exclusivo para identificar a implantação de atualizações.
* **Tipo de Sistema Operacional**: selecione Windows ou Linux.
* **Computadores a atualizar**: selecione as máquinas virtuais que você deseja atualizar.

  ![Painel “Nova implantação de atualizações”](./media/manage-update-multi/update-select-computers.png)

* **Classificação de atualização**: selecione os tipos de software que a implantação de atualização incluirá. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Pacotes cumulativos de atualização
  * Feature packs
  * Service packs
  * Atualizações de definição
  * Ferramentas
  * Atualizações
* **Configurações de agenda**: você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual. Ou você pode especificar uma hora diferente.
   Você também pode especificar se a implantação ocorre uma única vez ou em um agendamento recorrente. Para configurar um agendamento recorrente, selecione a opção **Recorrente** em **Recorrência**.

   ![Caixa de diálogo Configurações de agendamento](./media/manage-update-multi/update-set-schedule.png)

* **Janela de manutenção (minutos)**: especifique o período de tempo em que deseja que a implantação de atualização ocorra. Essa configuração ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Depois de concluir a configuração da agenda, retorne ao painel de status selecionando o botão **Criar**. A tabela **Agendado** mostra a agenda de implantação recém-criada.

> [!WARNING]
> Para atualizações que exigem reiniciar, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Depois de a implantação agendada iniciar, você pode ver o status dessa implantação na guia **Implantações de atualização** na caixa de diálogo **Gerenciamento de atualizações**.
Se a implantação estiver em execução no momento, seu status será **Em andamento**. Depois que a implantação for concluída com êxito, ele é alterado para **Êxito**.
Se uma ou mais atualizações falharem na implantação, o status será **Falhou parcialmente**.

![Status da implantação da atualização](./media/manage-update-multi/update-view-results.png)

Para ver o painel de uma implantação de atualização, selecione a implantação concluída.

O painel **Resultados de atualização** exibe o número total de atualizações e os resultados da implantação na máquina virtual.
A tabela à direita fornece uma análise detalhada de cada atualização e os resultados da instalação. Os resultados de instalação podem ser um dos seguintes valores:

* Não foi tentada: a atualização não foi instalada pois não havia tempo suficiente disponível com base na janela de manutenção definida.
* Êxito: a atualização foi bem-sucedida.
* Falha: a atualização falhou.

Para ver todas as entradas de log que a implantação criou, selecione **Todos os logs**.

Para ver o fluxo de trabalho do runbook que gerencia a implantação de atualização na máquina virtual de destino, selecione o bloco **Saída**.

Para ver informações detalhadas sobre quaisquer erros da implantação, selecione **Erros**.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gerenciamento de atualização – incluindo logs, saída e erros – consulte [Solução Gerenciamento de Atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).

