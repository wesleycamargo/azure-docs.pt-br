---
title: "Principais diferenças entre o Azure e a pilha do Azure quando você usa os serviços e criar aplicativos | Microsoft Docs"
description: "O que você precisa saber quando você usa os serviços ou compilar aplicativos para a pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 695824ef2537a97ea0530f2c33ad24d5cd9e20f8
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Quando você usa os serviços ou compilar aplicativos para a pilha do Azure, você deve entender que há diferenças entre a pilha do Azure e o Azure. Este artigo fornece uma visão geral dos principais considerações quando você direcionar a pilha do Azure como seu ambiente de desenvolvimento de nuvem híbrida.

## <a name="overview"></a>Visão geral

O Azure Stack é uma plataforma desktop em nuvem híbrida que possibilita usar serviços do Azure por meio do datacenter da sua empresa ou do seu provedor de serviços. Como desenvolvedor, você pode criar aplicativos que são executados na pilha do Azure. Você pode implantar esses aplicativos com a pilha do Azure, no Azure, ou você pode criar realmente aplicativos híbridos que podem aproveitam a conectividade entre uma nuvem de pilha do Azure e o Azure.

O operador de pilha do Azure permitirá que você sabe quais serviços estão disponíveis para uso e como obter suporte. Eles oferecem esses serviços por meio de seus planos personalizados e ofertas.

O conteúdo técnico do Azure pressupõe que os aplicativos estão sendo desenvolvidos para um serviço do Azure, em vez de pilha do Azure. Quando você criar e implanta aplicativos com a pilha do Azure, você deve entender algumas diferenças importantes, como:

* A pilha do Azure oferece um subconjunto dos serviços e recursos que estão disponíveis no Azure.
* Sua empresa ou provedores de serviços podem escolher quais serviços que desejam oferecer. Isso inclui aplicativos ou serviços personalizados. Eles podem oferecer sua própria documentação personalizada.
* Você deve usar o correto pontos de extremidade específicos de pilha do Azure (por exemplo, as URLs para o endereço do portal e o ponto de extremidade do Gerenciador de recursos do Azure).
* Você deve usar o PowerShell e a API de versões com suporte pela pilha do Azure. Isso garante que seus aplicativos funcionará na pilha do Azure e o Azure.

## <a name="cheat-sheet-high-level-differences"></a>Roteiro: diferenças de alto nível

A tabela a seguir descreve as diferenças de alto nível entre a pilha do Azure e o Azure. Mantenha em mente ao desenvolver para o Azure pilha ou usar os serviços de pilha do Azure.

| Área | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Que opera-lo? | Microsoft | Sua organização ou provedores de serviços.|
| Quem você contatar suporte? | Microsoft | Para um sistema integrado, entre em contato com seu operador de pilha do Azure (na sua organização ou provedores de serviços) para obter suporte.<br><br>Para obter suporte do Kit de desenvolvimento de pilha do Azure, visite o [fóruns do Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial oferecido por meio de serviços de suporte de cliente do Microsoft (CSS).
| Serviços disponíveis | Consulte a lista de [produtos do Azure](https://azure.microsoft.com/services/?b=17.04b). Os serviços disponíveis variam de acordo com a região do Azure. | A pilha do Azure suporta um subconjunto de serviços do Azure. Serviços reais irão variar com base nas quais sua organização ou provedores de serviços escolhe a oferecer.
| Azure ponto de extremidade de Gerenciador de recursos de * | https://management.azure.com | Para um sistema de pilha do Azure integrada, use o ponto de extremidade que o operador de pilha do Azure fornecido.<br><br>Para o kit de desenvolvimento, use: https://management.local.azurestack.external
| Portal de URL * | [https://portal.azure.com](https://portal.azure.com) | Para um sistema de pilha do Azure integrado, vá para a URL que o operador de pilha do Azure fornecido.<br><br>Para o kit de desenvolvimento, use: https://portal.local.azurestack.external
| Região | Você pode selecionar a região que você deseja implantar. | Para um sistema de pilha do Azure integrada, use a região que está disponível no seu sistema.<br><br>Para o kit de desenvolvimento, sempre será região **local**.
| Grupos de recursos | Um grupo de recursos pode abranger regiões. | Para sistemas integrados e o kit de desenvolvimento, há apenas uma região.
|Com suporte de namespaces, tipos de recursos e as versões de API | A versão mais recente (ou versões anteriores que ainda não foram substituídas). | A pilha do Azure oferece suporte a versões específicas. Consulte a seção "Requisitos de versão" deste artigo.
| | |

* Se você for um operador de pilha do Azure, consulte [usando o portal do administrador](../azure-stack-manage-portals.md) e [Noções básicas de administração](../azure-stack-manage-basics.md) para obter mais informações.

## <a name="helpful-tools-and-best-practices"></a>Ferramentas úteis e práticas recomendadas
 
 A Microsoft fornece várias ferramentas e diretrizes que ajuda você a desenvolvem para a pilha do Azure.

| Recomendações | Referências | 
| -------- | ------------- | 
| Instale as ferramentas corretas em sua estação de trabalho do desenvolvedor. | - [Instale o PowerShell](azure-stack-powershell-install.md)<br>- [Baixar ferramentas](azure-stack-powershell-download.md)<br>- [Configurar o PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalar o Visual Studio](azure-stack-install-visual-studio.md) 
| Examinar as informações sobre o seguinte:<br>-Considerações sobre o modelo azure Resource Manager<br>-Como encontrar modelos de início rápido<br>-Use um módulo de diretiva para ajudá-lo a usar o Azure para desenvolver para a pilha do Azure | [Desenvolver para o Azure Stack](azure-stack-developer.md) | 
| Examine e siga as práticas recomendadas para modelos. | [Modelos do Gerenciador de recursos de início rápido](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisitos de versão

A pilha do Azure oferece suporte a versões específicas do Azure PowerShell e APIs do serviço do Azure. Você deve usar as versões com suporte para garantir que seu aplicativo pode ser implantado para ambos os pilha do Azure e do Azure.

Para certificar-se de que você use uma versão correta do PowerShell do Azure, use [perfis de versão de API](azure-stack-version-profiles.md). Para determinar o último perfil de versão de API que você pode usar, você deve saber que compilação da pilha do Azure que você está usando. Você pode obter essas informações do administrador de pilha do Azure.

>[!NOTE]
 Se você estiver usando o Kit de desenvolvimento de pilha do Azure, e você tem acesso administrativo, consulte a seção "Determinar a versão atual" [gerenciar atualizações](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) para determinar a compilação de pilha do Azure.

Para outras APIs, executados o seguinte comando do PowerShell para os namespaces, tipos de recursos e as versões de API que são suportadas em sua assinatura do Azure pilha de saída. Observação ainda pode ser diferenças em um nível de propriedade. (Para esse comando funcione, é necessário já ter [instalado](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-user.md) PowerShell para um ambiente de pilha do Azure. Você também deve ter uma assinatura para uma oferta de pilha do Azure.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemplo de saída (truncado): ![exemplo de saída do comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as diferenças em um nível de serviço, consulte:

* [Considerações para máquinas virtuais na pilha do Azure](azure-stack-vm-considerations.md)
* [Considerações para o armazenamento na pilha do Azure](azure-stack-acs-differences.md)
* [Considerações de rede de pilha do Azure](azure-stack-network-differences.md)
