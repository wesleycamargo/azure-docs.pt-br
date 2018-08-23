---
title: Compreender as principais diferenças entre o Azure e o Azure Stack ao usar os serviços e aplicativos de construção | Microsoft Docs
description: O que você precisa saber para usar os serviços ou aplicativos para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a8d211992f52c9719cad76f16133e23eba24d422
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139389"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerações da chave: usando os serviços ou criação de aplicativos para o Azure Stack

Antes de usar os serviços ou crie aplicativos para o Azure Stack, você deve entender as diferenças entre o Azure Stack e o Azure. Este artigo identifica as principais considerações ao usar o Azure Stack como seu ambiente de desenvolvimento de nuvem híbrida.

## <a name="overview"></a>Visão geral

O Azure Stack é uma plataforma desktop em nuvem híbrida que possibilita usar serviços do Azure por meio do datacenter da sua empresa ou do seu provedor de serviços. Você pode criar um aplicativo no Azure Stack e, em seguida, implantá-lo para o Azure Stack, Azure ou sua nuvem híbrida do Azure.

O operador do Azure Stack permitem que você sabe quais serviços estão disponíveis para uso e como obter suporte. Eles oferecem esses serviços por meio de seus planos personalizados e ofertas.

O conteúdo técnico do Azure pressupõe que os aplicativos estão sendo desenvolvidos para um serviço do Azure, em vez do Azure Stack. Quando você criar e implantar aplicativos para o Azure Stack, você deve entender algumas diferenças importantes, tais como:

* O Azure Stack oferece um subconjunto dos serviços e recursos que estão disponíveis no Azure.
* Seu provedor de serviço ou da empresa pode escolher quais serviços que desejam oferecer. As opções disponíveis podem incluir serviços personalizados ou aplicativos. Eles podem oferecer sua própria documentação personalizada.
* Você deve usar o correto pontos de extremidade específicos de pilha do Azure (por exemplo, as URLs para o endereço do portal e o ponto de extremidade do Azure Resource Manager).
* Você deve usar o PowerShell e API de versões com suporte do Azure Stack. Usar as versões com suporte, você garante que seus aplicativos funcionarão no Azure Stack e no Azure.

## <a name="cheat-sheet-high-level-differences"></a>Folha de dados: diferenças de alto nível

A tabela a seguir descreve as diferenças de alto nível entre o Azure Stack e o Azure. Mantenha essas diferenças em mente ao desenvolver para o Azure Stack ou usar os serviços do Azure Stack.
*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

| Área | (Global) do Azure | Azure Stack |
| -------- | ------------- | ----------|
| Quem opera a ele? | Microsoft | Seu provedor de serviço ou organização.|
| Quem você contata para obter suporte? | Microsoft | Para um sistema integrado, entre em contato com seu operador do Azure Stack (na sua organização ou provedores de serviços) para obter suporte.<br><br>Para obter suporte do Kit de desenvolvimento do Azure Stack, visite o [fóruns da Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Como o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial, oferecido por meio de serviços de suporte de cliente do Microsoft (CSS).
| Serviços disponíveis | Consulte a lista de [produtos do Azure](https://azure.microsoft.com/services/?b=17.04b). Serviços disponíveis variam de acordo com a região do Azure. | O Azure Stack oferece suporte a um subconjunto de serviços do Azure. Serviços reais irão variar com base no qual seu provedor de serviço ou a organização opta por oferecer.
| Do Azure do Gerenciador de recursos de ponto de extremidade * | https://management.azure.com | Para um sistema integrado do Azure Stack, use o ponto de extremidade que forneceu o operador do Azure Stack.<br><br>Para o kit de desenvolvimento, use: https://management.local.azurestack.external
| Portal URL * | [https://portal.azure.com](https://portal.azure.com) | Para um sistema integrado do Azure Stack, vá para a URL que o operador do Azure Stack fornecida.<br><br>Para o kit de desenvolvimento, use: https://portal.local.azurestack.external
| Região | Você pode selecionar a região na qual você deseja implantar. | Para um sistema integrado do Azure Stack, use a região que está disponível em seu sistema.<br><br>Para o kit de desenvolvimento, região sempre serão **local**.
| Grupos de recursos | Um grupo de recursos pode abranger regiões. | Para sistemas integrados e o kit de desenvolvimento, há apenas uma região.
|Com suporte de namespaces, tipos de recursos e as versões de API | A versão mais recente (ou versões anteriores que ainda não foram preteridas). | O Azure Stack oferece suporte a versões específicas. Consulte a seção "Requisitos de versão" deste artigo.
| | |

* Se você for um operador do Azure Stack, consulte [usando o portal do administrador](../azure-stack-manage-portals.md) e [Noções básicas de administração](../azure-stack-manage-basics.md) para obter mais informações.

## <a name="helpful-tools-and-best-practices"></a>Ferramentas úteis e práticas recomendadas
 
 A Microsoft fornece várias ferramentas e orientações que ajudam a desenvolvem para o Azure Stack.

| Recomendações | Referências | 
| -------- | ------------- | 
| Instale as ferramentas corretas na sua estação de trabalho do desenvolvedor. | - [Instalar o PowerShell](azure-stack-powershell-install.md)<br>- [Baixe as ferramentas](azure-stack-powershell-download.md)<br>- [Configurar o PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalar o Visual Studio](azure-stack-install-visual-studio.md) 
| Examine as informações sobre os seguintes itens:<br>-Considerações sobre o modelo do Resource Manager azure<br>-Como localizar modelos de início rápido<br>– Use um módulo de diretiva para ajudá-lo a usar o Azure para desenvolver para o Azure Stack | [Desenvolver para o Azure Stack](azure-stack-developer.md) | 
| Examine e siga as práticas recomendadas para modelos. | [Modelos de início rápido do Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisitos de versão

O Azure Stack oferece suporte a versões específicas do Azure PowerShell e APIs de serviço do Azure. Use as versões com suporte para garantir que seu aplicativo pode ser implantado para ambos os Azure Stack e no Azure.

Para certificar-se de que você use uma versão correta do Azure PowerShell, use [perfis de versão da API](azure-stack-version-profiles.md). Para determinar o perfil de versão de API mais recente que você pode usar, descubra o build do Azure Stack, você está usando. Você pode obter essas informações do administrador do Azure Stack.

>[!NOTE]
 Se você estiver usando o Kit de desenvolvimento do Azure Stack e ter acesso administrativo, consulte a seção "Determinar a versão atual" [gerenciar atualizações](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) para determinar a compilação do Azure Stack.

Para outras APIs, execute o seguinte comando do PowerShell para gerar os namespaces, tipos de recursos e as versões de API que têm suporte em sua assinatura do Azure Stack. Observe que há ainda pode ser diferenças em um nível de propriedade. (Para esse comando funcione, é necessário já ter [instalados](azure-stack-powershell-install.md) e [configurados](azure-stack-powershell-configure-user.md) PowerShell para um ambiente do Azure Stack. Você também deve ter uma assinatura para uma oferta do Azure Stack.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemplo de saída (truncado): ![exemplo de saída do comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Próximas etapas

Para obter informações mais detalhadas sobre as diferenças em um nível de serviço, consulte:

* [Considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md)
* [Considerações para o armazenamento no Azure Stack](azure-stack-acs-differences.md)
* [Considerações sobre a rede do Azure Stack](azure-stack-network-differences.md)
