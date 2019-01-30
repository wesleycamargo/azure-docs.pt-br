---
title: Noções básicas sobre o Kit de desenvolvimento do Azure Stack | Microsoft Docs
description: Descreve como executar tarefas administrativas básicas para o Azure Stack desenvolvimento ASDK (Kit).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 79e031226f11d45121827795c34b0103235d8d79
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248156"
---
# <a name="asdk-administration-basics"></a>Noções básicas de administração de ASDK 
Há várias coisas que você precisa saber se você é novo para a administração do Azure Stack desenvolvimento ASDK (Kit). Este guia fornece uma visão geral de sua função como um operador do Azure Stack no ambiente de avaliação e como garantir que seus usuários de teste pode se tornarem rapidamente produtivo.

Primeiro, você deve examinar a [What ' s Kit de desenvolvimento do Azure Stack?](asdk-what-is.md) artigo para verificar se você compreender a finalidade de ASDK e suas limitações. Você deve usar o kit de desenvolvimento como uma "área restrita","onde você pode avaliar o Azure Stack para desenvolver e testar seus aplicativos em um ambiente de não produção. 

Como o Azure, Azure Stack inova rapidamente para que regularmente, lançaremos novas compilações do ASDK. No entanto, não é possível atualizar o ASDK como você pode fazer implantações de sistemas integrados do Azure Stack. Portanto, se você deseja mover para a compilação mais recente, você deve completamente [reimplantar o ASDK](asdk-redeploy.md). É possível aplicar os pacotes de atualização. Esse processo leva tempo, mas a vantagem é que você pode experimentar os recursos mais recentes assim que elas forem disponibilizadas. 

## <a name="what-account-should-i-use"></a>Qual conta devo usar?
Há algumas considerações de conta, que você deve estar atento ao gerenciar o Azure Stack. Especialmente em implantações usando o Windows Server Active Directory Federation Services (AD FS) como o provedor de identidade em vez do Azure AD (Active Directory do Azure). As considerações de conta a seguir se aplicam a sistemas integrados do Azure Stack e implantações de ASDK:

|Conta|AD do Azure|AD FS|
|-----|-----|-----|
|Administrador local (. \Administrator)|Administrador de host ASDK|Administrador de host ASDK|
|AzureStack\AzureStackAdmin|Administrador de host ASDK<br><br>Pode ser usado para fazer logon no portal de administração do Azure Stack<br><br>Acesso para exibir e administrar anéis do Service Fabric|Administrador de host ASDK<br><br>Nenhum acesso ao portal de administração do Azure Stack<br><br>Acesso para exibir e administrar anéis do Service Fabric<br><br>Não há mais de proprietário de assinatura de provedor padrão (DPS)|
|AzureStack\CloudAdmin|Pode acessar e executar os comandos permitidos dentro do ponto de extremidade com privilégios|Pode acessar e executar os comandos permitidos dentro do ponto de extremidade com privilégios<br><br>Não pode fazer logon no host ASDK<br><br>Proprietário da assinatura do provedor padrão (DPS)|
|Administrador Global do AD do Azure|Usado durante a instalação<br><br>Proprietário da assinatura do provedor padrão (DPS)|Não aplicável|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quais ferramentas usar para gerenciar?
Você pode usar o [Portal do Azure Stack administrador](https://adminportal.local.azurestack.external) ou o PowerShell para gerenciar o Azure Stack. É a maneira mais fácil de aprender os conceitos básicos por meio do portal. Se você quiser usar o PowerShell, você precisará instalar [PowerShell para o Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) e [baixar as ferramentas do Azure Stack no GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Pilha do Azure usa o Azure Resource Manager como seu mecanismo de implantação, gerenciamento e organização subjacente. Se você pretende gerenciar o Azure Stack e ajudar a dar suporte a usuários, você deve saber sobre o Azure Resource Manager. Você pode aprender mais lendo a [guia de Introdução do Azure Resource Manager whitepaper](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Suas responsabilidades típicas
Seus usuários desejam usar os serviços. Da sua perspectiva, sua função principal é disponibilizar esses serviços para eles. Usando o ASDK, você pode saber quais serviços para oferecer e como criá-los de serviços disponível por [criação de planos, ofertas e cotas](asdk-offer-services.md). Você também precisará adicionar itens ao marketplace, como imagens de máquina virtual. É a maneira mais fácil [baixar itens do marketplace](asdk-marketplace-item.md) do Azure para o Azure Stack.

> [!NOTE]
> Se você quiser testar planos, ofertas e serviços, você deve usar o [portal do usuário](https://portal.local.azurestack.external); não as [portal do administrador](https://adminportal.local.azurestack.external).

Além de fornecer serviços, você deve realizar todas as tarefas regulares de um operador de pilha do Azure para manter o ASDK em funcionamento. Essas tarefas incluem coisas como o seguinte:
- Adicione contas de usuário para o Azure Active Directory (Azure AD) ou implantações de serviços de Federação do Active Directory (AD FS).
- Atribuir funções RBAC (controle) (isso não está restrito a apenas os administradores) de acesso baseado em função
- Monitorar a integridade de infraestrutura
- Gerenciar recursos de rede e armazenamento
- Substitua o hardware do computador de host de kit de desenvolvimento com falha 

## <a name="where-to-get-support"></a>Onde obter suporte
Para o kit de desenvolvimento, sua única opção de suporte é fazer perguntas relacionadas ao suporte nas [Fórum do Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se você clique no ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, clique em **nova solicitação de suporte**, isso abre o site dos fóruns diretamente. Esses fóruns são monitorados regularmente. 

> [!IMPORTANT]
> Como o ASDK é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio de serviços de suporte de cliente do Microsoft (CSS).

## <a name="next-steps"></a>Próximas etapas
[Implantar o ASDK](asdk-install.md)

