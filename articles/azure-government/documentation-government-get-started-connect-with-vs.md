---
title: Conectar-se ao Azure Governamental com o Visual Studio | Microsoft Docs
description: "Informações sobre como gerenciar sua assinatura no Azure Governamental conectando-se ao Visual Studio"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Conectar-se por meio do Visual Studio
O Visual Studio é usado pelos desenvolvedores para gerenciar facilmente suas assinaturas do Azure durante a criação de soluções.  Atualmente, o Visual Studio não permite que você configure uma conexão com o Azure Governamental na interface do usuário.  

### <a name="updating-visual-studio-for-azure-government"></a>Atualizando o Visual Studio para o Azure Governamental
Para habilitar o Visual Studio para se conectar ao Azure Governamental, você precisará atualizar o registro.

1. Fechar o Visual Studio
2. Crie um arquivo de texto chamado **VisualStudioForAzureGov.reg**
3. Copie e cole o seguinte texto em **VisualStudioForAzureGov.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Salve e execute o arquivo clicando duas vezes nele.  Você precisará mesclar o arquivo ao registro.
5. Inicie o Visual Studio e comece a usar o [Gerenciador de Nuvem](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> Depois que essa chave do registro for definida, apenas assinaturas do Azure Governamental estarão acessíveis.  Você ainda verá as assinaturas configuradas anteriormente, mas e.as não funcionam porque o Visual Studio está conectado ao Azure Governamental em vez do Público do Azure.  Consulte a seção a seguir para obter as etapas para reverter as alterações.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Revertendo a conexão do Visual Studio para o Azure Governamental
Para habilitar o Visual Studio para se conectar ao Público do Azure, você precisará remover as configurações do registro que permitem a conexão com o Azure Governamental.

1. Fechar o Visual Studio
2. Crie um arquivo de texto chamado **VisualStudioForAzureGov_Remove.reg**
3. Copie e cole o seguinte texto em **VisualStudioForAzureGov_Remove.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Salve e execute o arquivo clicando duas vezes nele.  Você precisará mesclar o arquivo ao registro.
5. Iniciar o Visual Studio

> [!NOTE]
> Depois que essa chave do registro tiver sido revertida, suas assinaturas do Azure Governamental serão mostradas, mas não estarão acessíveis.  Elas podem ser removidas com segurança.
> 
> 

