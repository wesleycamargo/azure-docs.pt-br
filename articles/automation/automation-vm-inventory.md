---
title: "Gerenciar a VM do Azure com a coleta de inventário | Microsoft Docs"
description: "Gerenciar VM com coleta de inventário"
services: automation
keywords: "inventário, automação, alteração, acompanhamento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerenciar uma Máquina Virtual do Azure com a coleta de inventário

O acompanhamento de inventário pode ser habilitado para uma Máquina Virtual do Azure na página de recursos do computador. Esse método fornece uma interface do usuário baseada em navegador para definir e configurar a coleta de inventário.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
Se você não tiver uma Máquina Virtual do Azure, crie uma [máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitar a coleta de inventário de página de recursos da máquina virtual

1. Na tela à esquerda, selecione **Máquinas virtuais**.
1. Na lista, selecione uma máquina virtual.
1. Selecione **Inventário (Versão prévia)** no menu de recurso sob **Operações**.
1. Uma faixa é exibida na parte superior da página, notificando que a solução não está habilitada. Clique na faixa para habilitar a solução.
1. Selecione um espaço de trabalho do Log Analytics para armazenar seus logs de dados. Se nenhum espaço de trabalho estiver disponível para essa região, será solicitado que você crie uma conta de Automação e um espaço de trabalho padrão. Clique em **Habilitar** para iniciar a integração do seu computador.

   ![Exibir opções de integração](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos. Durante esse tempo você poderá fechar a folha ou então mantenha-a aberta e ela relatará quando a solução estiver habilitada. Você pode monitorar o status da implantação do painel de notificações.

   ![Exibir a solução de inventário imediatamente após a integração](./media/automation-vm-inventory/inventory-onboarded.png)

1. Quando a implantação for concluída, a barra de status desaparecerá. Neste momento, o sistema ainda está coletando dados de inventário e os dados podem ainda não estar visíveis. Uma coleta de dados completa pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Defina as configurações de inventário

Por padrão, Software, Serviços do Windows e Daemons Linux são configurados para coleta. Para coletar o inventário de Arquivos e do Registro do Windows, você define as configurações de coleta de inventário.

1. Da exibição **Inventário (Versão prévia)**, selecione o botão **Editar Configurações** na parte superior da página.
2. Para adicionar uma nova configuração de coleta, navegue até a categoria de configuração que você deseja adicionar usando as guias **Registro do Windows**, **Arquivos do Windows** e **Arquivos do Linux**. Clique em **Adicionar** na parte superior da página.
3. Para exibir os detalhes e as descrições de cada propriedade de configuração, visite a [Página de documentação do inventário](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Desconectando a máquina virtual do gerenciamento

Para remover o computador do gerenciamento de inventário:

1. No menu à esquerda no Portal do Azure, clique em **Log Analytics** e, em seguida, clique para selecionar o espaço de trabalho que você usou ao realizar a integração de sua máquina virtual.
1. Na página do Log Analytics, selecione **Máquinas virtuais** sob a categoria **Fontes de Dados de Espaço de Trabalho** no menu de recursos. 
1. Selecione a máquina virtual que você deseja desconectar da lista. Ela tem uma marca de seleção verde ao lado do texto que diz "Este espaço de trabalho" na coluna **Conexão OMS**. Clique em **Desconectar** na parte superior da página seguinte e **Sim** para a caixa de diálogo de confirmação para desconectar o computador do gerenciamento.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gerenciamento de alterações em arquivos e configurações do Registro em suas máquinas virtuais, consulte [Controle de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações de pacote e do Windows para as suas máquinas virtuais, consulte [Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md)

