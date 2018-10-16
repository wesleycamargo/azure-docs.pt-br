---
title: Noções básicas de administração do Azure Stack | Microsoft Docs
description: Saiba o que você precisa saber para administrar o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.openlocfilehash: 37b8eff2d4ed89c90f1fa6f128673ed5bacaaa90
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339943"
---
# <a name="azure-stack-administration-basics"></a>Noções básicas de administração do Azure Stack
Há várias coisas que você precisa saber se você é novo para a administração do Azure Stack. Este guia fornece uma visão geral de sua função como um operador do Azure Stack, e o que é preciso dizer que os usuários para que eles se tornarem rapidamente produtivos.

## <a name="understand-the-builds"></a>Entender as compilações

### <a name="integrated-systems"></a>Sistemas integrados

Se você estiver usando um sistema integrado do Azure Stack, versões atualizadas do Azure Stack são distribuídas por meio de pacotes de atualização. Você pode importar esses pacotes e aplicá-las usando o bloco de atualizações no portal do administrador.
 
### <a name="development-kit"></a>Kit de desenvolvimento

Se você estiver usando o Kit de desenvolvimento do Azure Stack, examine os [o que é o Azure Stack?](.\asdk\asdk-what-is.md) artigo para verificar se você entender a finalidade do kit de desenvolvimento e suas limitações. Você deve usar o kit de desenvolvimento como uma "área restrita","onde você pode avaliar o Azure Stack e desenvolver e testar seus aplicativos em um ambiente de não produção. (Para obter informações de implantação, consulte o [implantação do Kit de desenvolvimento do Azure Stack](.\asdk\asdk-install.md) artigo.)

Como o Azure, podemos inovar rapidamente. Regularmente, lançaremos novas compilações. Se você estiver executando o kit de desenvolvimento e você deseja mover para a compilação mais recente, você deve [reimplantar o Azure Stack](.\asdk\asdk-redeploy.md). É possível aplicar os pacotes de atualização. Esse processo leva tempo, mas a vantagem é que você pode experimentar os recursos mais recentes. A documentação do kit de desenvolvimento em nosso site reflete o build de versão mais recente.

## <a name="learn-about-available-services"></a>Saiba mais sobre os serviços disponíveis

Será necessário um reconhecimento de quais serviços você pode disponibilizar aos seus usuários. O Azure Stack oferece suporte a um subconjunto de serviços do Azure. A lista de serviços com suporte continuará a evoluir.

**Serviços fundamentais**

Por padrão, o Azure Stack inclui os seguintes "serviços fundamentais" quando você implanta o Azure Stack:

- Computação
- Armazenamento
- Rede
- Key Vault

Com esses serviços fundamentais, você pode oferecer infraestrutura-como um serviço (IaaS) para os usuários com configuração mínima.

**Serviços adicionais**

Atualmente, damos suporte os seguintes serviços de plataforma-como um serviço (PaaS) adicionais:

- Serviço de Aplicativo
- Funções do Azure
- Bancos de dados SQL e MySQL

Esses serviços exigem configuração adicional antes que você pode torná-los disponíveis aos usuários. Para obter mais informações, consulte "Tutoriais" e as seções "guides\Offer" como "serviços" da nossa documentação de operador do Azure Stack.

**Roteiro de serviço**

O Azure Stack continuará a adicionar suporte para serviços do Azure. Para o roteiro projetado, consulte o [do Azure Stack: uma extensão do Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) white paper. Você também pode monitorar o [postagens de blog do Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para ver anúncios sobre novas.

## <a name="what-account-should-i-use"></a>Qual conta devo usar?
Há algumas considerações de conta, que você deve estar atento ao gerenciar o Azure Stack. Especialmente em implantações usando o Windows Server Active Directory Federation Services (AD FS) como o provedor de identidade em vez do Azure AD (Active Directory do Azure). As considerações de conta a seguir se aplicam a sistemas integrados do Azure Stack e implantações de ASDK:


|Conta|AD do Azure|AD FS|
|-----|-----|-----|
|Administrador local (. \Administrator)|Administrador de host ASDK|Administrador de host ASDK|
|AzureStack\AzureStackAdmin|Administrador de host ASDK<br><br>Pode ser usado para entrar no portal de administração do Azure Stack<br><br>Acesso para exibir e administrar anéis do Service Fabric|Administrador de host ASDK<br><br>Nenhum acesso ao portal de administração do Azure Stack<br><br>Acesso para exibir e administrar anéis do Service Fabric<br><br>Não há mais de proprietário de assinatura de provedor padrão (DPS)|
|AzureStack\CloudAdmin|Pode acessar e executar os comandos permitidos dentro do ponto de extremidade com privilégios|Pode acessar e executar os comandos permitidos dentro do ponto de extremidade com privilégios<br><br>Não pode entrar no host ASDK<br><br>Proprietário da assinatura do provedor padrão (DPS)|
|Administrador Global do AD do Azure|Usado durante a instalação<br><br>Proprietário da assinatura do provedor padrão (DPS)|Não aplicável|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quais ferramentas usar para gerenciar?
 
Você pode usar o [portal do administrador](azure-stack-manage-portals.md) ou o PowerShell para gerenciar o Azure Stack. É a maneira mais fácil de aprender os conceitos básicos por meio do portal. Se você quiser usar o PowerShell, há etapas de preparação. Você deve [instale](azure-stack-powershell-install.md) PowerShell, [baixar](azure-stack-powershell-download.md) módulos adicionais, e [configurar](azure-stack-powershell-configure-admin.md) PowerShell.

Pilha do Azure usa o Azure Resource Manager como seu mecanismo de implantação, gerenciamento e organização subjacente. Se você pretende gerenciar o Azure Stack e ajudar a dar suporte a usuários, você deve saber sobre o Resource Manager. Consulte a [Introdução ao Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) white paper.

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas

Seus usuários desejam usar os serviços. Da sua perspectiva, sua função principal é disponibilizar esses serviços para eles. Você deve decidir quais serviços para oferecer e disponibilizar esses serviços com a criação de planos, ofertas e cotas. Para obter mais informações, consulte [visão geral da oferta de serviços no Azure Stack](azure-stack-offer-services-overview.md). 

Você também precisará adicionar itens a serem [marketplace](azure-stack-marketplace.md), como imagens de máquina virtual. É a maneira mais fácil [baixar itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se você quiser testar planos, ofertas e serviços, você deve usar o [portal do usuário](azure-stack-manage-portals.md); não o portal do administrador.

Além de fornecer serviços, você deve realizar todas as tarefas regulares de um operador para manter o Azure Stack em funcionamento. Essas tarefas incluem o seguinte:

- Adicionar contas de usuário (para [Azure Active Directory](azure-stack-add-new-user-aad.md) implantação ou para [serviços de Federação do Active Directory](azure-stack-add-users-adfs.md) implantação)
- [Atribuir funções RBAC (controle) de acesso baseado em função](azure-stack-manage-permissions.md) (isso não é restrito aos administradores.)
- [Monitorar a integridade de infraestrutura](azure-stack-monitor-health.md)
- Gerencie [rede](azure-stack-viewing-public-ip-address-consumption.md) e [armazenamento](azure-stack-manage-storage-accounts.md) recursos
- Substituir o hardware incorreta, por exemplo [substituir um disco com falha](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>O que dizer a seus usuários

Você precisará permitir que os usuários saber como trabalhar com os serviços no Azure Stack, como se conectar ao ambiente e como se inscrever para ofertas. Além de qualquer documentação personalizada que você talvez queira fornecer aos usuários, você pode direcionar usuários para o site de documentação de usuários do Azure Stack.

**Entender como trabalhar com os serviços no Azure Stack**

Não há informações que os usuários devem entender antes de usar os serviços e aplicativos no Azure Stack. Por exemplo, há requisitos específicos de versão de API e PowerShell. Além disso, há alguns deltas de recurso entre um serviço no Azure e o serviço equivalente no Azure Stack. Certifique-se de que seus usuários examine os seguintes artigos:

- [Considerações da chave: usando os serviços ou criação de aplicativos para o Azure Stack](user/azure-stack-considerations.md)
- [Considerações para máquinas virtuais no Azure Stack](user/azure-stack-vm-considerations.md)
- [Armazenamento: diferenças e considerações](user/azure-stack-acs-differences.md)

As informações neste artigo resumem as diferenças entre um serviço no Azure e o Azure Stack. Ele complementa as informações que está disponíveis para um serviço do Azure na documentação do Azure global.

**Conectar-se ao Azure Stack como um usuário**

Em um ambiente do kit de desenvolvimento, se um usuário não tem acesso de área de trabalho remota para o host do kit de desenvolvimento, eles devem configurar uma conexão de rede virtual privada (VPN) antes que possam acessar o Azure Stack. Ver [conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md). 

Os usuários vão querer saber como [acessar o portal do usuário ](user/azure-stack-use-portal.md) ou como se conectar por meio do PowerShell. Em um ambiente de sistemas integrados, o endereço de portal do usuário varia por implantação. Você precisará fornecer aos usuários com a URL correta.

Se estiver usando o PowerShell, os usuários precisam registrar os provedores de recursos antes de poderem usar os serviços. (Um provedor de recursos gerencia um serviço. Por exemplo, o provedor de recursos de rede gerencia recursos como redes virtuais, adaptadores de rede e balanceadores de carga.) Eles devem [instale](user/azure-stack-powershell-install.md) PowerShell, [baixar](user/azure-stack-powershell-download.md) módulos adicionais, e [configurar](user/azure-stack-powershell-configure-user.md) PowerShell (que inclui o registro do provedor de recursos).

**Assinar uma oferta**

Antes de um usuário pode acessar os serviços, eles devem [assinar uma oferta](azure-stack-subscribe-plan-provision-vm.md) que você criou como um operador.

## <a name="where-to-get-support"></a>Onde obter suporte

### <a name="integrated-systems"></a>Sistemas integrados

Para um sistema integrado, há um escalonamento coordenado e o processo de resolução entre a Microsoft e nossos parceiros de hardware do fabricante original do equipamento (OEM).

Se houver um problema de serviços de nuvem, o suporte é oferecido por meio de serviços de suporte de cliente do Microsoft (CSS). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre um site onde você pode abrir uma solicitação de suporte diretamente.

Se houver um problema com a implantação, patch e atualização, o hardware (incluindo unidades substituíveis de campo) e qualquer software de hardware com a marca, como o software em execução no host de ciclo de vida do hardware, entre em contato com seu fornecedor de hardware OEM pela primeira vez.

Para qualquer outra finalidade, entre em contato com o Microsoft CSS.

### <a name="development-kit"></a>Kit de desenvolvimento

Para o kit de desenvolvimento, você pode fazer perguntas relacionadas ao suporte nas [fóruns da Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre o site dos fóruns diretamente. Esses fóruns são monitorados regularmente. Como o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial oferecida por meio do Microsoft CSS.

## <a name="next-steps"></a>Próximas etapas

[Gerenciamento de região no Azure Stack](azure-stack-region-management.md)


