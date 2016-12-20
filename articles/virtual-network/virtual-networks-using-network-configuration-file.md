---
title: "Configurar uma rede virtual usando um arquivo de configuração de rede"
description: "Instruções para exportar e importar um arquivo de configuração de rede no Portal de gerenciamento para criar ou modificar as redes virtuais. "
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a29f94cf0b6f8da70a5a18dde1dcbdca68bf284


---
# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurar uma rede virtual usando um arquivo de configuração de rede
Você pode configurar uma rede virtual (VNet) usando o portal de gerenciamento do Azure, ou usando um arquivo de configuração de rede.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Criando e modificando um arquivo de configuração de rede
A maneira mais fácil de criar um arquivo de configuração de rede é exportar as configurações de rede de uma configuração de rede virtual existente e, então modificar o arquivo para conter as configurações que você deseja para configurar suas redes virtuais.

Para editar o arquivo de configuração de rede, basta abrir o arquivo, fazer as alterações apropriadas e, em seguida,  salvá-los. Você pode usar qualquer editor *xml* para fazer alterações no arquivo de configuração de rede. 

Você deve seguir atentamente as diretrizes para [configurações de esquema de arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

O Azure considera uma sub-rede com algo implantado como **em uso**. Quando uma sub-rede estiver em uso, ela não poderá ser modificada. Antes de modificar, mova qualquer coisa que você tenha implantado na sub-rede para uma sub-rede diferente que não está sendo modificada.   Consulte [Mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exporte e importe as configurações de rede virtual usando o Portal de gerenciamento
Você pode importar e exportar definições de configuração de rede contidas no arquivo de configuração de rede usando o PowerShell ou o Portal de Gerenciamento. As instruções a seguir ajudarão você a exportar e importar usando o Portal de gerenciamento. 

### <a name="to-export-your-network-settings"></a>Para exportar as configurações de rede
Quando você exporta, todas as configurações para as redes virtuais na sua assinatura serão gravadas em um arquivo .xml. 

1. Faça logon no **Portal de Gerenciamento**.
2. No Portal de Gerenciamento, na parte inferior da página **Redes**, clique em **Exportar**. 
3. Na janela **Exportar a configuração de rede** , verifique se você selecionou a assinatura para a qual deseja exportar as configurações de rede. Em seguida, clique na marca de seleção no canto inferior direito. 
4. Quando for solicitado, salve o arquivo *NetworkConfig.xml* no local de sua escolha.

### <a name="to-import-your-network-settings"></a>Para importar as configurações de rede
1. No **Portal de Gerenciamento**, no painel de navegação no canto inferior esquerdo, clique em **Novo**.
2. Clique em **Serviços de Rede** -> **Rede Virtual** -> **Importar configuração**.
3. Na página **Importar o arquivo de configuração de rede**, navegue até o arquivo de configuração de rede e, em seguida, clique na seta **Avançar**.
4. Na página **Criar sua rede** , você verá as informações na tela mostrando quais seções da sua configuração de rede serão alteradas ou criadas. Se as alterações estiverem corretas para você, clique na marca de seleção para continuar para atualizar ou criar sua rede virtual. 




<!--HONumber=Nov16_HO3-->


