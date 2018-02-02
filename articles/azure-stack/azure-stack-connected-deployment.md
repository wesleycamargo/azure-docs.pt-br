---
title: "Sistemas integrados de decisões de implantação conectados do Azure para a pilha do Azure | Microsoft Docs"
description: "Determine as decisões de vários nós do Azure de pilha do Azure conectada implantações de planejamento de implantação."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: a2d4efc3a3e1480de71528144ae3f025f4879f07
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Sistemas integrados do Azure conectada decisões de pilha do Azure de planejamento de implantação
Depois que você decidir [como integrará pilha do Azure em seu ambiente de nuvem híbrida](azure-stack-deployment-decisions.md), em seguida, finalizar suas decisões de implantação da pilha do Azure.

Implantando pilha Azure conectado ao Azure significa que você pode ter o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS) para armazenamento de identidade. Você também pode escolher entre qualquer modelo de cobrança: de pagamento que você-uso ou baseado na capacidade. Uma implantação conectada é a opção padrão porque permite que os clientes obter o máximo valor fora da pilha do Azure, particularmente para cenários de nuvem híbrida que envolvem o Azure e a pilha do Azure. 

## <a name="choose-an-identity-store"></a>Escolher um repositório de identidade
Com uma implantação de conectado, você pode escolher entre o Azure AD ou AD FS para armazenamento de identidade. Uma implantação desconectada, com nenhuma conectividade com a internet, só pode usar o AD FS.

Sua escolha de repositório de identidade não tem nenhuma relevância em máquinas virtuais de locatário (VMs). Máquinas virtuais de locatário podem escolher qual repositório de identidade que desejam conectar ao dependendo de como eles serão configurados: AD do Azure, o grupo de trabalho ingressado no domínio do Active Directory do Windows Server, etc. Isso está relacionado à decisão de provedor de identidade de pilha do Azure. 

Por exemplo, se você implanta o locatário IaaS VMs na parte superior da pilha do Azure e deseja ingressar em um domínio do Active Directory corporativo e usa contas de lá, você pode fazer isso ainda. Você não precisa usar o repositório de identidade do AD do Azure selecionada aqui para essas contas.

### <a name="azure-ad-identity-store"></a>Repositório de identidades do Azure AD
Quando você usa o AD do Azure para armazenamento de identidade requer duas contas do AD do Azure: uma conta de administrador global e uma conta de cobrança. Essas contas podem ser as mesmas contas ou contas diferentes. Ao usar a mesma conta de usuário pode ser mais simples e útil se você tiver um número limitado de contas do Azure, suas necessidades de negócios podem sugerir o uso de duas contas:

1. **Conta de administrador global** (necessário apenas para implantações conectadas). Essa é uma conta do Azure que é usada para criar aplicativos e entidades de serviço para serviços de infraestrutura de pilha do Azure no Azure Active Directory. Essa conta deve ter permissões de administrador de diretório para o diretório que o sistema de pilha do Azure será implantado em. Ele se tornará o operador"nuvem" Administrador Global para o Azure AD locatário e será usado: 
    - Para provisionar e delegar a aplicativos e entidades de serviço para todos os serviços de pilha do Azure que precisam interagir com o Active Directory do Azure e API do Graph. 
    - Como a conta de administrador de serviço. Esse é o proprietário da assinatura do provedor padrão (que você pode alterar posteriormente). Você pode fazer logon no portal de administração do Azure pilha com essa conta e pode usá-lo para criar ofertas e planos, definir cotas e executar outras funções administrativas na pilha do Azure.
2. **Conta de cobrança** (necessário para conectados e desconectados implantações). Essa conta do Azure é usada para estabelecer a relação de cobrança entre o Azure pilha integrado e o back-end de comércio do Azure. Essa é a conta que será cobrada por taxas de pilha do Azure. Essa conta também poderá ser usada para distribuição de mercado e outros cenários híbridos. 

### <a name="ad-fs-identity-store"></a>Repositório de identidades do AD FS
Escolha esta opção se você quiser usar seu próprio repositório de identidade, como o Active Directory corporativo, para suas contas de administrador de serviço.  

## <a name="choose-a-billing-model"></a>Escolha um modelo de cobrança
Você pode escolher a **pagamento como você-uso** ou **capacidade** modelo de cobrança. Implantações de modelo de cobrança de pagamento como você-uso devem ser capazes de uso de relatórios por meio de uma conexão para o Azure, pelo menos uma vez a cada 30 dias, portanto, se a conectividade será não está disponível, o modelo de cobrança de capacidade é a única opção. 

### <a name="pay-as-you-use"></a>Pagamento como você-uso
Com o modelo de cobrança de pagamento que você-uso uso é cobrado a uma assinatura do Azure. Você paga apenas quando você usa os serviços de pilha do Azure. Se este é o modelo que você decide, você precisará fazer uma assinatura do Azure e a ID da conta associada à assinatura (por exemplo, serviceadmin@contoso.onmicrosoft.com). Há suporte para assinaturas de EA CSP e CSL. Relatório de uso é configurado durante a [registro Azure pilha](azure-stack-registration.md).

> [!NOTE]
> Na maioria dos casos, os clientes corporativos irá usar assinaturas de EA, e provedores de serviço usará o CSP ou CSL assinaturas.

Se você pretende usar uma assinatura de CSP, examine a tabela a seguir para identificar qual assinatura CSP a usar, como a abordagem correta dependendo do cenário exato do CSP:

|Cenário|Opções de assinatura e de domínio|
|-----|-----|
|Você está uma **parceiro direto do CSP** ou um **indireta provedor de CSP**, e você irá operar a pilha do Azure|Use uma assinatura de CSL (camada de serviço comum).<br>     ou o<br>Crie um locatário do AD do Azure com um nome descritivo no Centro de parceiros. Por exemplo &lt;sua organização > CSPAdmin com uma assinatura do Azure CSP associada a ele.|
|Você está uma **indireta revendedor de CSP**, e você irá operar a pilha do Azure|Peça ao seu provedor de CSP indireta para criar um locatário do AD do Azure para sua organização com uma assinatura do Azure CSP associada a ele usando o Centro de parceiros.|

### <a name="capacity-based-billing"></a>Capacidade de cobrança com base
Se você decidir usar o modelo de cobrança de capacidade, você deve adquirir um Azure pilha capacidade planejar SKU com base na capacidade do seu sistema. Você precisará saber o número de núcleos físicos na pilha do Azure para adquirir a quantidade correta. 

Cobrança capacidade requer um Enterprise Agreement (EA) assinatura do Azure para registro. O motivo é que o registro define a distribuição, o que exige uma assinatura do Azure. A assinatura não é usada para o uso da pilha do Azure.

## <a name="learn-more"></a>Saiba mais
- Para obter informações sobre os casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [Azure pilha](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o mapa e a disponibilidade de replicação geográfica do Azure pilha sistemas integrados, consulte o white paper: [pilha do Azure: extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para saber mais sobre pacotes e preços de pilha do Microsoft Azure [baixar o PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Próximas etapas
[Integração de rede do Datacenter](azure-stack-network.md)