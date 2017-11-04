---
title: "Noções básicas de administração do Azure pilha | Microsoft Docs"
description: "Saiba o que você precisa saber para administrar a pilha do Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 04dea8f055eb562455b568c43553a6fefe749467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-administration-basics"></a>Noções básicas de administração do Azure pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Há várias coisas que você precisa saber se você é novo para a administração de pilha do Azure. Este guia fornece uma visão geral de sua função como um operador de pilha do Azure, e o que você precisa informar os usuários para que eles se tornarem rapidamente produtivo.

## <a name="understand-the-builds"></a>Entender as compilações

### <a name="integrated-systems"></a>Sistemas integrados

Se você estiver usando um sistema de pilha do Azure integrado, versões atualizadas da pilha do Azure são distribuídas por meio de pacotes de atualização. Você pode importar esses pacotes e aplicá-los usando o bloco de atualizações no portal do administrador.
 
### <a name="development-kit"></a>Kit de desenvolvimento

Se você estiver usando o Kit de desenvolvimento de pilha do Azure, examine o [o que é a pilha do Azure?](azure-stack-poc.md) artigo para certificar-se de entender a finalidade do kit de desenvolvimento e suas limitações. Você deve usar o kit de desenvolvimento como um "sandbox", onde você pode avaliar a pilha do Azure e desenvolver e testar seus aplicativos em um ambiente de não produção. (Para obter informações de implantação, consulte o [implantação do Kit de desenvolvimento de pilha do Azure](azure-stack-deploy-overview.md) início rápido.)

Como o Azure, estamos inovar rapidamente. Lançaremos regularmente novas compilações. Se você estiver executando o kit de desenvolvimento e você deseja mover para a última compilação, você deve [reimplantar Azure pilha](azure-stack-redeploy.md). Você não pode aplicar os pacotes de atualização. Esse processo leva tempo, mas o benefício é que você pode experimentar os recursos mais recentes. A documentação do kit de desenvolvimento no nosso site reflete a compilação de versão mais recente.

## <a name="learn-about-available-services"></a>Saiba mais sobre os serviços disponíveis

Você precisará de reconhecimento de quais serviços você pode disponibilizar para os usuários. A pilha do Azure suporta um subconjunto de serviços do Azure. A lista de serviços com suporte continuarão a evoluir.

**Serviços fundamentais**

Por padrão, a pilha do Azure inclui os seguintes "fundamentais serviços" quando você implanta a pilha do Azure:

- Computação
- Armazenamento
- Rede
- Cofre de Chaves

Com esses serviços fundamentais, você pode oferecer a infraestrutura-como-um-serviço (IaaS) para os usuários com configuração mínima.

**Serviços adicionais**

Atualmente, há suporte para os seguintes serviços de plataforma-como-um-serviço (PaaS) adicionais:

- Serviço de Aplicativo
- Funções do Azure
- Bancos de dados SQL e MySQL

Esses serviços exigem configuração adicional antes de você pode torná-los disponíveis aos usuários. Para obter mais informações, consulte "Tutoriais" e as seções "guides\Offer" como fazer "serviços" de nossa documentação de operador de pilha do Azure.

**Mapa de serviço**

Pilha do Azure continuará a adicionar suporte para os serviços do Azure. Para o roteiro projetado, consulte o [pilha do Azure: extensão do Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) white paper. Você também pode monitorar o [postagens no blog do Azure pilha](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para lançamentos de novos.

## <a name="what-tools-do-i-use-to-manage"></a>Quais ferramentas usar para gerenciar?
 
Você pode usar o [portal do administrador](azure-stack-manage-portals.md) ou o PowerShell para gerenciar a pilha do Azure. É a maneira mais fácil de aprender os conceitos básicos por meio do portal. Se você quiser usar o PowerShell, há etapas de preparação. Você deve [instalar](azure-stack-powershell-install.md) PowerShell, [baixar](azure-stack-powershell-download.md) módulos adicionais, e [configurar](azure-stack-powershell-configure-admin.md) PowerShell.

Pilha do Azure usa o Gerenciador de recursos do Azure como seu mecanismo subjacente de implantação, gerenciamento e organização. Se você pretende gerenciar a pilha do Azure e ajudar a oferecer suporte a usuários, você deve saber sobre o Gerenciador de recursos. Consulte o [guia de Introdução com o Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) white paper.

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas

Seus usuários desejam usar os serviços. Da sua perspectiva, sua função principal é disponibilizar esses serviços a eles. Você deve decidir quais serviços para oferecer e disponibilizar esses serviços por meio da criação de planos, ofertas e cotas. Para obter mais informações, consulte [visão geral da oferta de serviços na pilha do Azure](azure-stack-offer-services-overview.md). 

Você também precisará adicionar itens à [marketplace](azure-stack-marketplace.md), como imagens de máquina virtual. A maneira mais fácil é [baixar itens do marketplace do Azure para o Azure pilha](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se você quiser testar planos, ofertas e serviços, você deve usar o [portal do usuário](azure-stack-manage-portals.md); não o portal do administrador.

Além de fornecer serviços, você deve executar todas as tarefas regulares de um operador para manter a pilha do Azure em execução. Essas tarefas incluem o seguinte:

- Adicionar contas de usuário (para [Active Directory do Azure](azure-stack-add-new-user-aad.md) implantação ou [os serviços de Federação do Active Directory](azure-stack-add-users-adfs.md) implantação)
- [Atribuir funções RBAC (controle) de acesso baseado em função](azure-stack-manage-permissions.md) (isso não é restrito aos administradores.)
- [Monitorar a integridade de infraestrutura](azure-stack-monitor-health.md)
- Gerenciar [rede](azure-stack-viewing-public-ip-address-consumption.md) e [armazenamento](azure-stack-manage-storage-accounts.md) recursos
- Substitua o hardware inválido, por exemplo [substituir um disco com falha](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>O que dizer a seus usuários

Será necessário para que os usuários saibam como trabalhar com serviços na pilha do Azure, como conectar-se ao ambiente e como se inscrever para ofertas. Além de qualquer documentação personalizada que talvez você queira fornecer aos usuários, você pode direcionar os usuários para o site de documentação de usuários de pilha do Azure.

**Entender como trabalhar com os serviços na pilha do Azure**

Não há informações de que seus usuários devem entender antes de usar os serviços e criar aplicativos na pilha do Azure. Por exemplo, existem requisitos específicos de versão de API e do PowerShell. Além disso, há alguns deltas de recurso entre um serviço no Azure e o serviço equivalente na pilha do Azure. Certifique-se de que seus usuários Revise os seguintes artigos:

- [Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure](user/azure-stack-considerations.md)
- [Considerações para máquinas virtuais na pilha do Azure](user/azure-stack-vm-considerations.md)
- [Armazenamento: as diferenças e considerações](user/azure-stack-acs-differences.md)

As informações neste artigo resumem as diferenças entre um serviço no Azure e a pilha do Azure. Ele complementa as informações que estão disponíveis para um serviço do Azure na documentação do Azure global.

**Conecte-se a pilha do Azure como um usuário**

Em um ambiente do kit de desenvolvimento, se um usuário não tem acesso de área de trabalho remota para o host do kit de desenvolvimento, eles devem configurar uma conexão de rede virtual privada (VPN) antes que eles possam acessar a pilha do Azure. Consulte [pilha do Azure se conectem](azure-stack-connect-azure-stack.md). 

Seus usuários desejam saber como [acessar o portal do usuário ](user/azure-stack-use-portal.md) ou como se conectar por meio do PowerShell. Em um ambiente de sistemas integrados, o portal do usuário endereço varia por implantação. Você precisará fornecer aos usuários a URL correta.

Se estiver usando o PowerShell, os usuários podem precisar registrar provedores de recursos para poder usar os serviços. (Um provedor de recursos gerencia um serviço. Por exemplo, o provedor de recursos de rede gerencia os recursos, como redes virtuais, interfaces de rede e balanceadores de carga.) Eles devem [instalar](user/azure-stack-powershell-install.md) PowerShell, [baixar](user/azure-stack-powershell-download.md) módulos adicionais, e [configurar](user/azure-stack-powershell-configure-user.md) PowerShell (que inclui o registro do provedor de recursos).

**Assinar uma oferta**

Antes de um usuário pode acessar serviços, eles devem [assinar uma oferta](azure-stack-subscribe-plan-provision-vm.md) que você criou como um operador.

## <a name="where-to-get-support"></a>Onde obter suporte

### <a name="integrated-systems"></a>Sistemas integrados

Para um sistema integrado, há uma coordenada de escalonamento e o processo de resolução entre a Microsoft e seus parceiros de hardware do fabricante (OEM).

Se houver um problema de serviços de nuvem, o suporte é oferecido por meio de serviços de suporte de cliente do Microsoft (CSS). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre um site onde você pode abrir uma solicitação de suporte diretamente.

Se houver um problema com a implantação, patch e atualização, o hardware (incluindo unidades substituíveis de campo) e qualquer software de hardware com a marca, como o software em execução no host de ciclo de vida do hardware, entre em contato com o fornecedor do hardware OEM primeiro.

Para qualquer outra coisa, entre em contato com a Microsoft CSS.

### <a name="development-kit"></a>Kit de desenvolvimento

Para o kit de desenvolvimento, você pode fazer perguntas relacionadas ao suporte a [fóruns do Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre o site de fóruns diretamente. Esses fóruns são monitorados regularmente. Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum suporte oficial oferecido pelo Microsoft CSS.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de região na pilha do Azure](azure-stack-region-management.md)


