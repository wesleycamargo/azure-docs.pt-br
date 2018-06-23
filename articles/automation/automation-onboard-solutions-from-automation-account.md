---
title: Saiba como integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário na Automação do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0174e2a3c0b14c52b5750e343932a5df39d18976
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833342"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de integrar computadores, por exemplo, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [procurando em vários computadores](automation-onboard-solutions-from-browse.md), de sua conta de Automação ou por [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções em sua conta de Automação.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Fazer logon no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Habilitar soluções

Navegue até sua conta de Automação e selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**.

Escolha o espaço de trabalho do Log Analytics e a conta de automação e clique em **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Integrar a solução de Inventário](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

A solução de Controle de Alterações e Inventário fornece a capacidade de [acompanhar alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) em suas máquinas virtuais. Nesta etapa, você deve habilitar a solução em uma máquina virtual.

Quando a notificação da integração da solução de inventário e controle de alterações for concluída, clique em **Gerenciamento de Atualizações** sob **GERENCIAMENTO DE CONFIGURAÇÃO**.

A solução de Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure. Você pode avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM. Esta ação habilitou a solução para a VM.

Selecione **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**. O espaço de trabalho do Log Analytics selecionado é o mesmo espaço de trabalho usado na etapa anterior. Clique em **Habilitar** para integrar a solução de Gerenciamento de atualizações. A solução demora até 15 minutos para habilitar.

![Integrar solução de atualização](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuração de Escopo

Cada solução usa uma Configuração de Escopo dentro do espaço de trabalho para direcionar os computadores que recebem a solução. A Configuração de Escopo é um grupo de uma ou mais pesquisas salvas utilizada para limitar o escopo da solução em computadores específicos. Para acessar as Configurações de Escopo, na sua conta de Automação, em **RECURSOS RELACIONADOS**, selecione **Espaço de trabalho**. Em seguida, no espaço de trabalho em **FONTES DE DADOS DO ESPAÇO DE TRABALHO**, selecione **Configurações de Escopo**.

Se o espaço de trabalho selecionado não tiver as soluções de Gerenciamento de Atualizações ou Controle de Alterações ainda, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver solução. A solução não é reimplantada, e a configuração de escopo não é adicionada a isso.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções Gerenciamento de Atualizações ou Controle de Alterações e Inventário, elas são adicionadas para uma das duas pesquisas salvas no espaço de trabalho. Essas pesquisas salvas são consultas que contêm os computadores segmentados para essas soluções.

Navegue para a conta de Automação e selecione **Pesquisas salvas** em **Geral**. As duas pesquisas salvas usadas por essas soluções podem ser vistas na tabela a seguir:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione a pesquisa salva para visualizar a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Integrar VMs do Azure

Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Clique em **+Adicionar VMs do Azure**, selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. Na página **Habilitar Gerenciamento de Atualizações**, clique em **Habilitar**. Isso adiciona as VMs selecionadas à pesquisa salva no grupo de computadores da solução.

![Habilitar VMs do Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Integrar um computador não Azure

Computadores que não estão no Azure precisam ser adicionados manualmente. Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Clique em **Adicionar computador não Azure**. Isso abre uma nova janela do navegador com as [instruções sobre como instalar e configurar o Microsoft Monitoring Agent no computador](../log-analytics/log-analytics-concept-hybrid.md) para que o computador possa começar a relatar a solução. Se você estiver instalando um computador atualmente gerenciado pelo System Center Operations Manager, um novo agente não é necessário, as informações do espaço de trabalho são inseridas no agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Integrar computadores no espaço de trabalho

Computadores instalados manualmente ou computadores que já estão emitindo relatórios para seu espaço de trabalho precisam ser adicionados à Automação do Azure para a solução a ser habilitada. Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Selecione **Gerenciar computadores**. Isso abre a página **Gerenciar Computadores**. Esta página permite habilitar a solução em um conjunto selecionado de computadores, todos os computadores disponíveis ou habilitar a solução para todos os computadores atuais e habilitá-la em todos os computadores futuros.

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Todos os computadores disponíveis

Para habilitar a solução a todos os computadores disponíveis, selecione **Habilitar em todos os computadores disponíveis**. Isso desabilita o controle para adicionar computadores individualmente. Esta tarefa adiciona todos os nomes dos computadores que informam sobre o espaço de trabalho na consulta de pesquisa salva do grupo de computadores.

### <a name="all-available-and-future-machines"></a>Todos os computadores disponíveis e futuros

Para habilitar a solução a todos os computadores disponíveis e todos os computadores futuros, selecione **Habilitar em todos os computadores disponíveis e futuros**. Essa opção exclui as pesquisas salvas e as Configurações de Escopo do espaço de trabalho. Isso abre a solução para todos os computadores do Azure e não Azure que estão relatando para o espaço de trabalho.

### <a name="selected-machines"></a>Computadores selecionados

Para habilitar a solução em um ou mais computadores, selecione **Habilitar nos computadores selecionados** e clique em **adicionar** próximo a cada computador que você quer adicionar à solução. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para a solução.

## <a name="next-steps"></a>Próximas etapas

Continue com os tutoriais sobre as soluções para aprender a utilizá-las.

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)