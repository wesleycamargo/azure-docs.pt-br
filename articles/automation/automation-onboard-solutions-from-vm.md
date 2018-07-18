---
title: Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma VM do Azure
description: Saiba como integrar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221505"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma máquina virtual do Azure

A Automação do Azure fornece soluções para ajudá-lo a gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de integrar computadores. É possível integrar a solução de uma máquina virtual, [a partir de sua conta de Automação](automation-onboard-solutions-from-automation-account.md), [procurando em várias máquinas ](automation-onboard-solutions-from-browse.md), ou usando um [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções a partir de uma máquina virtual do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Habilitar as soluções

Acesse uma máquina virtual existente. Em **OPERAÇÕES** , selecione **Gerenciamento de atualizações**, **Inventário** ou **Controle de alterações**.

Para habilitar a solução somente para a VM, assegure-se de que **Habilitar para esta VM** está selecionado. Para integrar várias máquinas à solução, selecione **Habilitar para VMs nesta assinatura** e, em seguida, selecione **Clique para selecionar as máquinas para habilitar**. Para saber como integrar várias máquinas de uma só vez, consulte [Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário](automation-onboard-solutions-from-automation-account.md).

Selecione o espaço de trabalho do Azure Log Analytics e a conta de Automação e selecione **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Integrar solução de Gerenciamento de Atualizações](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Acesse outras soluções e selecione **Habilitar**. As listas suspensas da conta de Automação e Log Analytics estão desabilitadas porque essas soluções usam o mesmo espaço de trabalho e conta de Automação como a solução anteriormente habilitada.

> [!NOTE]
> O **Controle de Alterações** e **Inventário** usam a mesma solução. Quando uma dessas soluções estiver habilitada, a outra também estará habilitada.

## <a name="scope-configuration"></a>Configuração de escopo

Cada solução usa uma configuração de escopo no espaço de trabalho para direcionar os computadores que obtêm a solução. A configuração do escopo é um grupo de uma ou mais pesquisas salvas que são usadas para limitar o escopo da solução a computadores específicos. Para acessar as configurações de escopo, na sua conta de Automação, em **RECURSOS RELACIONADOS**, selecione **Espaço de trabalho**. No espaço de trabalho, em **FONTES DE DADOS DO ESPAÇO DE TRABALHO**, selecione **Configurações de Escopo**.

Se o espaço de trabalho selecionado ainda não tiver as soluções Gerenciamento de Atualizações ou Controle de Alterações, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver a solução, a solução não será implantada novamente e a configuração do escopo não será adicionada.

Selecione as reticências (**...**) em qualquer uma das configurações e, em seguida, selecione **Editar**. No painel **Editar configuração de escopo**, selecione **Selecionar Grupos de Computadores**. O painel **Grupos de Computadores** mostra as pesquisas salvas que são usadas para criar a configuração de escopo.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções Gerenciamento de Atualizações, Controle de Alterações ou Inventário, o computador é adicionado a uma das duas pesquisas salvas no espaço de trabalho. As pesquisas salvas são consultas que contêm os computadores que são direcionados para essas soluções.

Vá até seu espaço de trabalho. Em **Geral**, selecione **Pesquisas salvas**. As duas pesquisas salvas usadas por essas soluções são mostradas na tabela a seguir:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione uma das pesquisas salvas para exibir a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

Continue com os tutoriais das soluções para aprender como utilizá-las:

* [Tutorial - Gerenciar atualizações de sua VM](automation-tutorial-update-management.md)
* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)
* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
