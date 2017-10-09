---
title: "Controle as alterações nas suas máquinas virtuais do Azure | Microsoft Docs"
description: "Use o Controle de alterações para controlar as alterações em arquivos e registro nas suas máquinas virtuais"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Controle as alterações nas suas máquinas virtuais do Azure

Ao habilitar o controle de alterações, você pode controlar as alterações a arquivos e chaves de registro do Windows nas suas máquinas virtuais. . Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

Você pode habilitar o controle de alterações diretamente da sua máquina virtual do Azure.

Se você não tiver uma máquina virtual do Azure, poderá criar uma usando o [Início rápido do Windows](../virtual-machines/windows/quick-create-portal.md) ou [Início rápido do Linux](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Habilitar o controle de alterações para uma máquina virtual do Azure

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
1. Na lista, selecione uma máquina virtual.
1. Na tela de máquina virtual, na seção **Operações**, clique em **Controle de alterações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

   ![VM de integração de controle de alterações](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

É feita uma validação para determinar se o controle de alterações está habilitado para essa máquina virtual. Se o controle de alterações não estiver habilitado, uma faixa será exibida dando a opção de habilitar a solução.

   ![Faixa de configuração integrada do controle de alterações](./media/automation-vm-change-tracking/change-onboard-banner.png)

Clique na faixa para habilitar a solução. Se você não tiver os itens a seguir, eles serão adicionados automaticamente:

* Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md)
* Conta de [automação](../automation/automation-offering-get-started.md)

Selecione um espaço de trabalho do Log Analytics para armazenar logs de dados de controle de alterações e uma conta de Automação para controlar as alterações e, em seguida, clique em **Habilitar**.

Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos.

## <a name="configure-change-tracking"></a>Configurar o controle de alterações

Depois que o controle de alterações estiver habilitado, a tela **Controle de alterações** será exibida. Escolha quais arquivos e chaves de registro controlar clicando em **Editar configurações**.

![Editar configurações do controle de alterações](./media/automation-vm-change-tracking/change-edit-settings.png)

A tela **Configuração do Espaço de Trabalho** se abre. Clique em **Adicionar** na guia apropriada para adicionar as chaves de registro do Windows, os arquivos do Windows ou os arquivos do Linux a serem controlados.

## <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registro do Windows

1. Na guia **Registro do Windows** da tela **Configuração do Espaço de Trabalho**, clique em **Adicionar**. A tela **Adicionar Registro do Windows para Controle de Alterações** se abre.

   ![Adicionar registro do controle de alterações](./media/automation-vm-change-tracking/change-add-registry.png)

1. Em **Habilitado**, selecione **True**.
1. Adicionar um nome amigável no campo **Nome do Item**.
1. No campo **Grupo**, digite um nome de grupo (opcional).
1. No campo **Chave do Registro do Windows**, adicione o nome da chave do registro que você deseja controlar.
1. Clique em **Salvar**.

## <a name="add-a-windows-file"></a>Adicionar um arquivo do Windows

1. Na guia **Arquivos do Windows** da tela **Configuração do Espaço de Trabalho**, clique em **Adicionar**. A tela **Adicionar Arquivo do Windows para Controle de Alterações** é aberta.

   ![Adicionar arquivo do Windows do controle de alterações](./media/automation-vm-change-tracking/change-add-win-file.png)

1. Em **Habilitado**, selecione **True**.
1. Adicionar um nome amigável no campo **Nome do Item**.
1. No campo **Grupo**, digite um nome de grupo (opcional).
1. No campo **Inserir Caminho**, adicione o caminho completo e o nome do arquivo que você deseja rastrear.
1. Clique em **Salvar**.

## <a name="add-a-linux-file"></a>Adicionar um arquivo Linux

1. Na guia **Arquivos do Linux** da tela **Configuração do Espaço de Trabalho**, clique em **Adicionar**. A tela **Adicionar Arquivo do Linux para Controle de Alterações** é aberta.

   ![Adicionar arquivo do Linux do controle de alterações](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. Em **Habilitado**, selecione **True**.
1. Adicionar um nome amigável no campo **Nome do Item**.
1. No campo **Grupo**, digite um nome de grupo (opcional).
1. No campo **Inserir Caminho**, adicione o caminho completo e o nome do arquivo que você deseja rastrear.
1. No campo **Tipo de Caminho**, selecione **Arquivo** ou **Diretório**.
1. Em **Recursão**, selecione **Ligado** para controlar alterações para o caminho especificado e todos os arquivos e caminhos sob ele. Para controlar somente o caminho ou o arquivo selecionado, selecione **Desligado**.
1. Em **Sudo**, selecione **Ativado** para controlar arquivos que exija o comando `sudo` para acesso. Caso contrário, selecione **Desligado**.
1. Clique em **Salvar**.

## <a name="view-changes"></a>Exibir alterações

Na tela **Controle de alterações**, você pode ver as alterações em cada uma das várias categorias para a sua máquina virtual ao longo do tempo.

   ![Adicionar alterações de exibição do controle de alterações](./media/automation-vm-change-tracking/change-view-changes.png)

Você pode selecionar as categorias e o intervalo de tempo das alterações a exibir. Na parte superior da tela, você verá uma exibição gráfica das alterações ao longo do tempo.
Na parte inferior da tela, você verá uma lista das alterações recentes.

## <a name="view-change-tracking-log-data"></a>Exibir dados de log do controle de alterações

O controle de alterações gera dados de log que são enviados para o Log Analytics. Para pesquisar os logs executando consultas, clique em **Log Analytics** na parte superior da tela **Controle de alterações**.

   ![Log Analytics do controle de alterações](./media/automation-vm-change-tracking/change-log-analytics.png)

Para saber mais sobre como executar arquivos de log de pesquisa no Log Analytics, consulte [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o controle de alterações, consulte [Controle de Alterações](../log-analytics/log-analytics-change-tracking.md)
* Para saber mais sobre como gerenciar atualizações para as suas máquinas virtuais, consulte [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md).

