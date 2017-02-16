---
title: Assinaturas do Azure Governamental | Microsoft Docs
description: "Informações sobre como gerenciar sua assinatura no Azure Governamental"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gerenciando e conectando-se à sua assinatura no Azure Governamental
O Azure Governamental tem URLs e pontos de extremidade exclusivos para gerenciar seu ambiente. É importante usar conexões adequadas para gerenciar seu ambiente usando o portal ou o PowerShell. Quando você está conectado ao ambiente do Azure Governamental, as operações normais para gerenciar um serviço funcionarão se o componente foi implantado.

## <a name="connecting-via-the-portal"></a>Conectando-se usando o portal
O portal é a principal maneira usada pelas pessoas para se conectar ao Azure Governamental.  Para se conectar, navegue até o portal em [https://portal.azure.us](https://portal.azure.us).  A versão herdada do portal do Azure pode ser acessada em [https://manage.windowsazure.us](https://manage.windowsazure.us).

É possível criar assinaturas para sua conta ao se conectar a [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Conectando-se usando o PowerShell
Se você estiver usando o Azure PowerShell para gerenciar uma grande assinatura com recursos de acesso ou script que não estão disponíveis atualmente no portal do Azure, precisará se conectar ao Azure Governamental em vez do Público do Azure.  Se você usou o PowerShell no Público do Azure, será basicamente a mesma coisa.  As diferenças do Azure Governamental são:

* A conexão com sua conta
* Os nomes das regiões

> [!NOTE]
> Se você ainda não usou o PowerShell, confira a [Introdução ao Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Quando você inicia o PowerShell, precisa que o Azure PowerShell se conecte ao Azure Governamental especificando um parâmetro de ambiente.  O parâmetro garante que o PowerShell está se conectando aos pontos de extremidade corretos.  A coleção de pontos de extremidade é determinada quando você entra na sua conta.  APIs diferentes exigem versões de opções do ambiente diferentes:

| Tipo de conexão | Command |
| --- | --- |
| Comandos de [gerenciamento de serviços](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandos de [gerenciamento de recursos](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandos do [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Comando do Azure Active Directory v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Linha de da Comando CLI do Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

Você também pode usar a opção Ambiente durante a conexão com uma conta de armazenamento usando New-AzureStorageContext e especificando AzureUSGovernment.

### <a name="determining-region"></a>Determinando a região
Depois de se conectar, haverá mais uma diferença: as regiões usadas para um serviço de destino.  Cada nuvem do Azure tem regiões diferentes.  Você pode vê-las listadas na página de disponibilidade do serviço.  Normalmente, você usa a região no parâmetro Location para um comando.

Há um problema.  As regiões do Azure Governamental precisam estar formatadas de maneira diferente de seus nomes comuns:

| Nome comum | Comando |
| --- | --- |
| US Gov Virginia |USGov Virginia |
| US Gov Iowa |USGov Iowa |

> [!NOTE]
> Não existe espaço entre US e Gov ao usar o parâmetro Location.
> 
> 

Se você quiser validar as regiões disponíveis no Azure Governamental, poderá executar os seguintes comandos e imprimir a lista atual:

    Get-AzureLocation

Se estiver curioso sobre os ambientes disponíveis no Azure, poderá executar:

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>Conectar-se por meio do Visual Studio
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

## <a name="next-steps"></a>Próximas etapas
Se quiser saber mais, confira:

* [Documentos do PowerShell no GitHub](https://github.com/Azure/azure-powershell)
* [Instruções passo a passo sobre como conectar-se ao Gerenciamento de Recursos](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [Documentos do Azure PowerShell no MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Para obter informações complementares e atualizações, assine o [Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


