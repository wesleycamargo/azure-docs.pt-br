---
title: Sistemas integrados de decisões de implantação conectado do Azure para o Azure Stack | Microsoft Docs
description: Determine decisões de planejamento para implantações de conectada ao Azure Stack Azure de vários nós de implantação.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 491bdf121729d690784324051ff701f3ed2d2b7a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243174"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Sistemas integrados de implantação do Azure conectada, decisões de planejamento para o Azure Stack
Depois que você decidiu [como você irá integrar o Azure Stack em seu ambiente de nuvem híbrida](azure-stack-connection-models.md), em seguida, você pode finalizar suas decisões de implantação do Azure Stack.

Implantação do Azure Stack, conectado ao Azure significa que você pode ter o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) para armazenamento de identidade. Você também pode escolher a partir de qualquer um dos modelos de cobrança: de pagamento-como-o uso ou baseada em capacidade. Uma implantação conectada é a opção padrão, pois permite que os clientes obtenham o máximo valor fora do Azure Stack, principalmente para cenários de nuvem híbrida que envolvem o Azure e o Azure Stack. 

## <a name="choose-an-identity-store"></a>Escolha um repositório de identidades
Com uma implantação de conectado, você pode escolher entre o Azure AD ou AD FS para seu repositório de identidades. Uma implantação desconectada, com nenhuma conectividade com a internet, só pode usar o AD FS.

Sua escolha de repositório de identidade não tem nenhuma relevância em máquinas virtuais de locatário (VMs). VMs do locatário podem escolher qual repositório de identidade que desejam conectar-se ao, dependendo de como eles serão configurados: AD do Azure, grupo de trabalho ingressado no domínio do Windows Server Active Directory, etc. Isso está relacionado a decisão de provedor de identidade do Azure Stack. 

Por exemplo, se você implantar VMs na parte superior da pilha do Azure do locatário de IaaS e quer que eles para ingressar em um domínio do Active Directory corporativo e usar contas a partir daí, você pode fazer isso ainda. Você não precisa usar o repositório de identidades do AD do Azure selecionadas aqui para essas contas.

### <a name="azure-ad-identity-store"></a>Repositório de identidades do AD do Azure
Quando você usa o Azure AD para seu repositório de identidades requer duas contas do AD do Azure: uma conta de administrador global e uma conta de cobrança. Essas contas podem ser as mesmas contas ou contas diferentes. Ao usar a mesma conta de usuário pode ser mais simples e útil se você tiver um número limitado de contas do Azure, suas necessidades de negócios podem sugerir o uso de duas contas:

1. **Conta de administrador global** (necessário apenas para implantações conectadas). Esta é uma conta do Azure que é usada para criar aplicativos e entidades de serviço para serviços de infraestrutura do Azure Stack no Active Directory do Azure. Essa conta deve ter permissões de administrador de diretório para o diretório que será implantado em seu sistema do Azure Stack. Ele se tornará o operador"nuvem" Administrador Global para o Azure AD de locatário e será usado: 
    - Para provisionar e delegar a aplicativos e entidades de serviço para todos os serviços do Azure Stack que precisam interagir com o Azure Active Directory e a API do Graph. 
    - Como a conta de administrador de serviço. Isso é o proprietário da assinatura do provedor padrão (que pode ser alterado posteriormente). Você pode fazer logon no portal de administração do Azure Stack com essa conta e pode usá-lo para criar ofertas e planos, definir cotas e executar outras funções administrativas no Azure Stack.
2. **Conta de cobrança** (necessário para ambos conectados e desconectados implantações). Essa conta do Azure é usada para estabelecer a relação de cobrança entre seu sistema integrado do Azure Stack e o back-end de comércio do Azure. Essa é a conta que será cobrada por taxas do Azure Stack. Essa conta também será usada para a oferta de itens no marketplace e outros cenários híbridos. 

### <a name="ad-fs-identity-store"></a>Repositório de identidades do AD FS
Escolha esta opção se você quiser usar seu próprio repositório de identidades, como seu Active Directory corporativo, para suas contas de administrador de serviço.  

## <a name="choose-a-billing-model"></a>Escolha um modelo de cobrança
Você pode escolher entre **pagamento-como-uso** ou o **capacidade** modelo de cobrança. Implantações de modelo de cobrança de pagamento-como-uso devem ser capazes de uso do relatório por meio de uma conexão para o Azure, pelo menos uma vez a cada 30 dias. Portanto, o modelo de cobrança pagamento-como-uso só está disponível para implantações conectadas.  

### <a name="pay-as-you-use"></a>Pagamento-como-uso
Com o modelo de cobrança pagamento-como-o uso, o uso é cobrado a uma assinatura do Azure. Você paga apenas quando você usa os serviços do Azure Stack. Se esse é o modelo que você decidir sobre, será necessário uma assinatura do Azure e a ID de conta associada a essa assinatura (por exemplo, serviceadmin@contoso.onmicrosoft.com). Há suporte para assinaturas de EA, CSP e CSL. Relatório de uso é configurado durante [registro do Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Na maioria dos casos, os clientes corporativos serão usar assinaturas de EA e provedores de serviço usará as assinaturas de CSP ou CSL.

Se você pretende usar uma assinatura do CSP, examine a tabela a seguir para identificar qual assinatura de CSP a ser usado, como a abordagem correta depende do cenário exato do CSP:

|Cenário|Opções de assinatura e de domínio|
|-----|-----|
|Você está uma **parceiros de CSP direto** ou um **provedor de CSP indireto**, e você irá operar o Azure Stack|Use uma assinatura CSL (camada de serviço comum).<br>     ou o<br>Crie um locatário do AD do Azure com um nome descritivo no Partner Center. Por exemplo &lt;sua organização > CSPAdmin com uma assinatura do Azure CSP associada a ele.|
|Você está uma **revendedor de CSP indireto**, e você irá operar o Azure Stack|Peça ao seu provedor de CSP indireto para criar um locatário do AD do Azure para sua organização com uma assinatura do Azure CSP associada a ele usando o Partner Center.|

### <a name="capacity-based-billing"></a>Capacidade de cobrança com base
Se você decidir usar o modelo de cobrança de capacidade, você deve comprar um SKU do Azure Stack capacidade plano com base na capacidade do seu sistema. Você precisará saber o número de núcleos físicos no Azure Stack para adquirir a quantidade correta. 

Cobrança capacidade requer um Enterprise Agreement (EA) a assinatura do Azure para o registro. O motivo é que o registro define a disponibilidade dos itens no Marketplace, que requer uma assinatura do Azure. A assinatura não é usada para o uso do Azure Stack.

## <a name="learn-more"></a>Saiba mais
- Para obter informações sobre casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o roteiro e a disponibilidade geográfica para o Azure Stack, sistemas integrados, consulte o white paper: [Azure Stack: Uma extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para saber mais sobre pacotes e preços do Microsoft Azure Stack [baixar o. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Próximas etapas
[Integração de rede do Datacenter](azure-stack-network.md)