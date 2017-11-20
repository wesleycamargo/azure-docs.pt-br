---
title: "Gerenciar atualizações para várias máquinas virtuais do Azure | Microsoft Docs"
description: "Este tópico descreve como gerenciar atualizações para máquinas virtuais do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: f97b28d1588e959728163f7ab16d2550a79f610e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Gerenciar atualizações de vários computadores

O gerenciamento de atualizações permite que você gerencie atualizações e patches para seus computadores Windows e Linux.
Na conta da [Automação do Azure](automation-offering-get-started.md), você pode carregar computadores rapidamente, avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais para as quais o gerenciamento de atualizações está habilitado.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de atualizações, você precisa:

* Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, confira [Introdução à Automação do Azure](automation-offering-get-started.md).

* Uma máquina virtual ou um computador com um dos sistemas operacionais com suporte instalado.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Há suporte para o gerenciamento de atualizações nos seguintes sistemas operacionais.

### <a name="windows"></a>Windows

* Windows Server 2008 e superior e implantações de atualização no Windows Server 2008 R2 SP1 e superior.  Não há suporte para as opções de instalação Server Core e Nano Server.

    > [!NOTE]
    > O suporte à implantação de atualizações para o Windows Server 2008 R2 SP1 requer o .NET Framework 4.5 e WMF 5.0 ou posterior.
    > 
* Não há suporte para sistemas operacionais clientes do Windows.

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update.

> [!NOTE]
> O agente do Windows não pode ser gerenciado simultaneamente pelo System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) e 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)  
* Ubuntu 12.04 LTS e posterior x86/x64   

> [!NOTE]  
> Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar isso, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Os agentes do Linux devem ter acesso a um repositório de atualização.

> [!NOTE]
> Um agente do OMS para Linux configurado para emitir relatórios para vários espaços de trabalho do OMS não tem suporte nessa solução.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar o Gerenciamento de Atualizações para máquinas virtuais do Azure

1. No Portal do Azure, abra a Conta de automação.
2. No lado esquerdo da tela, selecione **Gerenciamento de atualizações**.
3. Na parte superior da tela, clique em **Adicionar VM do Azure**.
    ![Carregar VMs](./media/manage-update-multi/update-onboard-vm.png)
4. Selecione uma máquina virtual a ser carregada. A tela **Habilitar Gerenciamento de Atualizações** é exibida.
5. Clique em **Habilitar**.

   ![Habilitar gerenciamento de atualizações](./media/manage-update-multi/update-enable.png)

O gerenciamento de atualizações está habilitado para sua máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitar o gerenciamento de atualizações para computadores e máquinas virtuais que não são Azure

Para obter instruções sobre como habilitar o gerenciamento de atualizações para computadores e máquinas virtuais Windows que não são Azure, confira [Conectar computadores Windows ao serviço Log Analytics no Azure](../log-analytics/log-analytics-windows-agents.md).

Para obter instruções sobre como habilitar o gerenciamento de atualizações para computadores e máquinas virtuais Linux que não são Azure, confira [Conectar computadores Linux ao Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

## <a name="data-collection"></a>Coleta de dados

Os agentes instalados em máquinas virtuais e computadores coletam dados sobre atualizações e os enviam para o gerenciamento de atualizações do Azure.

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes Linux e inicia a instalação das atualizações necessárias em distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |O gerenciamento de atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado. |
| Conta de Armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência de coleta

Para cada computador gerenciado do Windows, uma verificação é executada duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status for alterado e, nesse caso, é iniciada uma verificação de conformidade.  Para cada computador Linux gerenciado, uma verificação é executada a cada três horas.

Pode demorar de 30 minutos a seis horas para o painel exibir dados atualizados em computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço.
Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para uma ou mais máquinas virtuais clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de atualizações**. Na tela **Nova implantação de atualização**, especifique o seguinte:

* **Nome** – forneça um nome exclusivo para identificar a Implantação de atualizações.
* **Tipo de Sistema Operacional** – selecione Windows ou Linux.
* **Computadores a atualizar** – selecione as máquinas virtuais que você deseja atualizar.

  ![Selecionar máquinas virtuais a serem atualizadas](./media/manage-update-multi/update-select-computers.png)

* **Classificação de atualização** – selecione os tipos de software que a implantação de atualização incluirá na implantação. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Pacotes cumulativos de atualização
  * Feature packs
  * Service packs
  * Atualizações de definição
  * Ferramentas
  * Atualizações
* **Configurações de agenda** – você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual ou especificar um horário diferente.
   Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Clique na opção Recorrente em Recorrência para configurar um agendamento recorrente.

   ![Tela de configurações de agenda de atualização](./media/manage-update-multi/update-set-schedule.png)

* **Janela de manutenção (minutos)** – especifique o período de tempo em que deseja que a implantação de atualização ocorra.  Isso ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação recém-criada.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
Se ela estiver em execução, seu status será mostrado como **Em andamento**. Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver falha em uma ou mais atualizações na implantação, o status será **Falhou parcialmente**.

![Status da implantação da atualização ](./media/manage-update-multi/update-view-results.png)

Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na máquina virtual.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* Não foi tentada – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* Êxito – a atualização foi bem-sucedida
* Falha – falha na atualização

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na máquina virtual de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

Para obter informações detalhadas sobre os logs, saída e informações de erro, confira [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciamento de atualizações, consulte [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md).
