---
title: Noções básicas do Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: Descreve como executar tarefas de administração básicas para o Kit de desenvolvimento do Azure pilha (ASDK).
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fa2ee4df9a0a28c08237f30fcf6a4bac50c21100
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849501"
---
# <a name="asdk-administration-basics"></a>Noções básicas de administração de ASDK 
Há várias coisas que você precisa saber se você é novo para a administração do Kit de desenvolvimento na pilha do Azure (ASDK). Este guia fornece uma visão geral de sua função como um operador de pilha do Azure no ambiente de avaliação e como garantir que os usuários de teste pode se tornarem rapidamente produtivo.

Primeiro, você deve examinar o [o que é o Kit de desenvolvimento de pilha do Azure?](asdk-what-is.md) artigo para certificar-se de entender a finalidade de ASDK e suas limitações. Você deve usar o kit de desenvolvimento como "seguro", onde você pode avaliar a pilha do Azure para desenvolver e testar seus aplicativos em um ambiente de não produção. 

Como o Azure, Azure pilha inova rapidamente para regularmente lançaremos novas compilações do ASDK. No entanto, não é possível atualizar o ASDK como implantações de sistemas do Azure pilha integrado. Portanto, se você deseja mover para a última compilação, você deve completamente [reimplantar o ASDK](asdk-redeploy.md). Você não pode aplicar os pacotes de atualização. Esse processo leva tempo, mas o benefício é que você pode experimentar os recursos mais recentes assim que estiverem disponíveis. 

## <a name="what-account-should-i-use"></a>Qual conta devo usar?
Há algumas considerações de conta, que você deve estar atento ao gerenciar a pilha do Azure. Especialmente em implantações usando o Windows Server Active Directory Federation Services (AD FS) como o provedor de identidade em vez do Azure Active Directory (AD do Azure). As considerações de conta a seguir se aplicam a sistemas de pilha do Azure integradas e implantações de ASDK:

|Conta|AD do Azure|AD FS|
|-----|-----|-----|
|Administrador local (. \Administrator)|Administrador de host ASDK|Administrador de host ASDK|
|AzureStack\AzureStackAdmin|Administrador de host ASDK<br><br>Pode ser usado para fazer logon no portal de administração do Azure pilha<br><br>Acesso para exibir e administrar anéis Service Fabric|Administrador de host ASDK<br><br>Nenhum acesso ao portal de administração de pilha do Azure<br><br>Acesso para exibir e administrar anéis Service Fabric<br><br>Não é mais o proprietário da assinatura de provedor padrão (DPS)|
|AzureStack\CloudAdmin|Pode acessar e executar comandos permitidos no ponto de extremidade com privilégios|Pode acessar e executar comandos permitidos no ponto de extremidade com privilégios<br><br>Não pode fazer logon no host ASDK<br><br>Proprietário da assinatura de provedor padrão (DPS)|
|Administrador Global do AD do Azure|Usado durante a instalação<br><br>Proprietário da assinatura de provedor padrão (DPS)|Não aplicável|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quais ferramentas usar para gerenciar?
Você pode usar o [Portal de administrador de pilha](https://adminportal.local.azurestack.external) ou o PowerShell para gerenciar a pilha do Azure. É a maneira mais fácil de aprender os conceitos básicos por meio do portal. Se você quiser usar o PowerShell, você precisa instalar [PowerShell para Azure pilha](asdk-post-deploy.md#install-azure-stack-powershell) e [baixar as ferramentas do Azure pilha do GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Pilha do Azure usa o Gerenciador de recursos do Azure como seu mecanismo subjacente de implantação, gerenciamento e organização. Se você pretende gerenciar a pilha do Azure e ajudar a oferecer suporte a usuários, você deve saber sobre o Gerenciador de recursos do Azure. Você pode aprender mais lendo o [guia de Introdução ao Azure Resource Manager whitepaper](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas
Seus usuários desejam usar os serviços. Da sua perspectiva, sua função principal é disponibilizar esses serviços a eles. Usando o ASDK, você pode saber quais serviços para oferecer e como tornar os serviços disponíveis por [criação de planos, ofertas e cotas](asdk-offer-services.md). Você também precisará adicionar itens para o marketplace, como imagens de máquina virtual. A maneira mais fácil é [baixar itens do marketplace](asdk-marketplace-item.md) do Azure para a pilha do Azure.

> [!NOTE]
> Se você quiser testar planos, ofertas e serviços, você deve usar o [portal do usuário](https://portal.local.azurestack.external); não o [portal do administrador](https://adminportal.local.azurestack.external).

Além de fornecer serviços, você deve executar todas as tarefas regulares de um operador de pilha do Azure para manter o ASDK em execução. Essas atividades incluem itens como o seguinte:
- Adicione contas de usuário para o Azure Active Directory (AD do Azure) ou implantações de serviços de Federação do Active Directory (AD FS).
- Atribuir acesso baseado em função (isso não é restrito aos administradores apenas) de funções RBAC (controle)
- Monitorar a integridade de infraestrutura
- Gerenciar recursos de rede e armazenamento
- Substitua o hardware do computador de host de kit de desenvolvimento com falha 

## <a name="where-to-get-support"></a>Onde obter suporte
Para o kit de desenvolvimento, sua única opção de suporte é fazer perguntas relacionadas ao suporte no [Fórum do Microsoft Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre o site de fóruns diretamente. Esses fóruns são monitorados regularmente. 

> [!IMPORTANT]
> Como o ASDK é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio de serviços de suporte de cliente do Microsoft (CSS).

## <a name="next-steps"></a>Próximas etapas
[Implantar o ASDK](asdk-install.md)

