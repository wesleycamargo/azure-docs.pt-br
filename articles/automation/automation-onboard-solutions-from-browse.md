---
title: Saiba como integrar as soluções de gerenciamento de atualizações, controle de alterações e inventário para várias VMs na automação do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233930"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Habilitar o gerenciamento de atualizações, o controle de alterações e as soluções de inventário em várias VMs

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras para integrar máquinas, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), da sua [conta de automação](automation-onboard-solutions-from-automation-account.md), durante a navegação de máquinas virtuais ou pelo [runbook](automation-onboard-solutions.md). Este artigo aborda essas soluções de integração ao procurar máquinas virtuais no Azure.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Fazer logon no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Habilitar soluções

No portal do Azure, navegue até **máquinas virtuais**.

Usando as caixas de seleção, selecione as máquinas virtuais que você deseja integrar com o controle de alterações e inventário ou de gerenciamento de atualização. A integração está disponível para até três grupos de recursos diferente cada vez.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Use os controles de filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de seleção superior para selecionar todas as máquinas virtuais na lista.

Na barra de comandos, clique em **serviços** e selecione **controle de alterações**, **inventário**, ou **Update Management**.

> [!NOTE]
> **Controle de alterações** e **Inventário** usam a mesma solução, quando um está habilitado, o outro também está habilitado.

A imagem a seguir é para o gerenciamento de atualizações. O controle de alterações e inventário têm o mesmo layout e comportamento.

A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

Use os controles de filtro para selecionar máquinas virtuais em assinaturas diferentes, locais e grupos de recursos.

![Solução integrada de gerenciamento de atualização](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Revise as opções para o espaço de trabalho de análise de Log e a conta de automação. Um novo espaço de trabalho e a conta de automação são selecionadas pelo padrão. Se você tiver um espaço de trabalho de análise de Log existente e a conta de automação que você deseja usar, clique em **alterar** para selecioná-los a partir da página de **configuração**. Quando terminar, clique em **Salvar**.

![Selecione espaço de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. Máquinas virtuais que não podem ser habilitadas já estão desmarcadas.

Clique em **Ativar** para ativar a solução. A solução demora até 15 minutos para habilitar.

## <a name="troubleshooting"></a>solução de problemas

Ao integrar várias máquinas, pode haver máquinas que mostrem como **Não é possível ativar**. Há diferentes razões que algumas máquinas não podem ser ativadas. As seções a seguir mostram possíveis razões para o estado **Impossível ativar** em uma VM ao tentar integrar.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Relatórios de VM para outro espaço de trabalho: '\<workspaceName\>'.  Alterar a configuração para usá-lo para ativar

**Causa**: este erro mostra a máquina virtual que você está tentando carregar relatórios para outro espaço de trabalho.

**Solução**: Clique em **Usar como configuração** para alterar a área de trabalho da conta de automação e do Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Relatórios VM para um espaço de trabalho que não está disponível nesta assinatura

**Causa**: O espaço de trabalho que se reporta a máquina virtual:

* Está em uma assinatura diferente ou
* Não existe mais, ou
* Está em um grupo de recursos que você não tem as permissões de acesso

**Solução**: localizar a conta de automação associada com o espaço de trabalho que se reporta a VM e integrar a máquina virtual, alterando a configuração do escopo.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A versão ou distribuição do sistema operacional VM não é suportada

**Causa:** A solução não é suportada em todas as distribuições do Linux ou em todas as versões do Windows.

**Solução:** consulte a [lista de clientes com suporte](automation-update-management.md#clients) para a solução.

### <a name="classic-vms-cannot-be-enabled"></a>VMs clássicas não podem ser habilitadas

**Causa**: máquinas virtuais que usam o modelo de implantação clássico não são suportadas.

**Solução**: migre a máquina virtual para o modelo de implementação do gerenciador de recursos. Para aprender como fazer isso, consulte [ Migrar recursos do modelo de implantação clássico ](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>A VM está parada. (deslocada)

**Causa**: a máquina virtual não está no estado **Em Execução**.

**Solução**: para integrar uma VM a uma solução, a VM deve estar em execução. Clique no link embutido **Iniciar VM** para iniciar a VM sem sair da página.

## <a name="next-steps"></a>Próximas etapas

Agora que a solução está habilitada para suas máquinas virtuais, visite o artigo Visão Geral do Gerenciamento de Atualizações para saber como visualizar a avaliação de atualizações de suas máquinas.

> [!div class="nextstepaction"]
> [Gerenciamento de atualização - avaliação de atualização do modo de exibição](./automation-update-management.md#viewing-update-assessments)

Tutoriais de adição sobre as soluções e como usá-los:

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)