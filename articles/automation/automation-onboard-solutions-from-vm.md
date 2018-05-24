---
title: Saiba como integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma Máquina Virtual do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 39febc947f4ab6dc406290273e5e1fc1c58a59e2
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053417"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma Máquina Virtual do Azure

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de integrar computadores, por exemplo, você pode integrar a solução de uma máquina virtual [a partir da sua conta de Automação do Azure](automation-onboard-solutions-from-automation-account.md) ou por [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções a partir de uma Máquina Virtual do Azure.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Fazer logon no Azure em https://portal.azure.com

## <a name="enable-the-solutions"></a>Habilitar as soluções

Navegue até uma máquina virtual existente e selecione **Gerenciamento de atualizações**, **Inventário** ou **Controle de Alterações** em **OPERAÇÕES**.

Escolha o espaço de trabalho do Log Analytics e a conta de automação e clique em **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Integrar solução de atualização](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Navegue para as outras soluções e clique em **habilitar**, as caixas suspensas da conta de Automação, no Log Analytics, estão desabilitadas porque usam o mesmo espaço de trabalho e conta de automação como a solução anteriormente habilitada.

![Integrar solução de atualização](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> **Controle de alterações** e **Inventário** usam a mesma solução, quando um está habilitado, o outro também está habilitado.

## <a name="scope-configuration"></a>Configuração de Escopo

Cada solução usa uma Configuração de Escopo dentro do espaço de trabalho para direcionar os computadores que recebem a solução. A Configuração de Escopo é um grupo de uma ou mais pesquisas salvas utilizada para limitar o escopo da solução em computadores específicos. Para acessar as Configurações de Escopo, em sua conta de Automação, em **RECURSOS RELACIONADOS**, selecione **Espaço de Trabalho**, em seguida, no espaço de trabalho em **FONTES DE DADOS DO ESPAÇO DE TRABALHO**, selecione **Configurações de Escopo**.

Se o espaço de trabalho selecionado não tiver as soluções de Gerenciamento de Atualizações ou Controle de Alterações ainda, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o espaço de trabalho selecionado já tiver solução. A solução não é reimplantada, e a configuração de escopo não é adicionada a isso.

Clique nas reticências (...) em qualquer uma das configurações e selecione **Editar**. Na página **Editar configuração de escopo**, selecione **Selecionar Grupos de Computadores** para abrir a página **Grupos de Computadores**. Esta página mostra as pesquisas salvas que são utilizadas para criar a Configuração de Escopo.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções Gerenciamento de Atualizações ou Controle de Alterações e Inventário, elas são adicionadas para uma das duas pesquisas salvas no espaço de trabalho. Essas pesquisas salvas são consultas que contêm os computadores segmentados para essas soluções.

Navegue para o espaço de trabalho e selecione **Pesquisas salvas** em **Geral**. As duas pesquisas salvas usadas por essas soluções podem ser vistas na tabela a seguir:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione a pesquisa salva para visualizar a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados.

![Pesquisas salvas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

Continue com os tutoriais sobre as soluções para aprender a utilizá-las.

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
