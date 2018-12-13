---
title: Sistemas integrados de decisões de implantação desconectado do Azure para o Azure Stack | Microsoft Docs
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
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 33512b47eff75421ce07b02f9c17ae3028152568
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276240"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Sistemas integrados de implantação do Azure desconectada, decisões de planejamento para o Azure Stack
Depois que você decidiu [como você irá integrar o Azure Stack em seu ambiente de nuvem híbrida](azure-stack-connection-models.md), em seguida, você pode finalizar suas decisões de implantação do Azure Stack.

Você pode implantar e usar o Azure Stack sem uma conexão à internet. No entanto, com uma implantação desconectada, você está limitado a um repositório de identidades do AD FS e o modelo de cobrança com base em capacidade. Como a multilocação requer o uso do AD do Azure, multilocação não há suporte para implantações desconectadas. 

Escolha esta opção se você:
- Ter segurança ou outras restrições que exigem que você implante o Azure Stack em um ambiente que não está conectado à Internet.
- Deseja bloquear (incluindo dados de uso) de dados sejam enviados para o Azure.
- Deseja usar o Azure Stack puramente como uma solução de nuvem privada que é implantada em sua Intranet corporativa e não esteja interessado em cenários híbridos.

> [!TIP]
> Às vezes, esse tipo de ambiente é também conhecido como um "cenário submarino".

Uma implantação desconectada não estritamente significa que você não pode mais tarde se conectar sua instância do Azure Stack para o Azure para cenários VM de locatário de híbrida. Isso significa que você não tiver conectividade com o Azure durante a implantação ou você não quiser usar o Azure Active Directory como seu repositório de identidades.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Recursos que estão com deficiência ou não está disponíveis em implantações desconectadas 
O Azure Stack foi projetado para funcionar melhor quando conectado ao Azure, portanto, é importante observar que há alguns recursos e funcionalidades que estão completamente indisponível no modo desconectado ou com deficiência. 

|Recurso|Impacto em modo desconectado|
|-----|-----|
|Implantação da VM com a extensão de DSC para configurar a implantação de postagem VM|Extensão de DSC prejudicada - parece à Internet para o WMF mais recente.|
|Implantação da VM com a extensão do Docker para executar comandos do Docker|Usuários portadores de deficiência – Docker verificará a Internet para a versão mais recente, e essa verificação falhará.|
|Links de documentação no Portal do Azure Stack|Não disponível-Links, como fornecer comentários, ajuda, Quickstart, etc. que usam uma URL de Internet não funcionará.|
|Alerta correção/redução que faz referência a um guia de atualização on-line|Não disponível – qualquer correção de alertas links que usam que uma URL de Internet não funcionará.|
|Marketplace – a capacidade de selecionar e adicionar pacotes de galeria diretamente no Azure Marketplace|Usuários portadores de deficiência – quando você implanta o Azure Stack em um modo desconectado (sem nenhuma conectividade de Internet), não é possível baixar itens do marketplace usando o portal do Azure Stack. No entanto, você pode usar o [ferramenta de distribuição de mercado](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) para baixar os itens do marketplace para um computador que tenha conectividade com a internet e, em seguida, transferi-las para seu ambiente do Azure Stack.|
|Usando contas de Federação do Active Directory do Azure para gerenciar uma implantação do Azure Stack|Não disponível – Este recurso requer conectividade com o Azure. AD FS com uma instância local do Active Directory deve ser usado em vez disso.|
|Serviços de Aplicativos|Usuários portadores de deficiência - aplicativos Web pode exigir acesso à Internet para conteúdo atualizado.|
|Interface de Linha de Comando (CLI)|Usuários portadores de deficiência – CLI tem funcionalidade reduzida em termos de autenticação e provisionamento de princípios de serviço.|
|Visual Studio – o Cloud discovery|Com deficiências – o Cloud Discovery descobrirá ou nuvens diferentes ou não funcionará em todos os.|
|Visual Studio – AD FS|Usuários portadores de deficiência – somente o Visual Studio Enterprise dá suporte ao AD FS.
Telemetria|Não disponível-dados de telemetria para o Azure Stack, assim como os pacotes de galeria de produtos de terceiros que dependem de dados de telemetria.|
|Certificados|Não disponível-conectividade com a Internet é necessário para serviços de lista de revogação de certificados (CRL) e protocolo de Status de certificado Online (OSCP) no contexto de HTTPS.|
|Cofre de chaves|Um caso de uso comum para o Key Vault prejudicada – é ter um aplicativo leia segredos em tempo de execução. Para este aplicativo precisa de uma entidade de serviço no diretório. No Azure Active Directory, usuários regulares (não administradores) são por padrão, permitida para adicionar entidades de serviço. No AD (usando o AD FS) não forem. Isso coloca um obstáculo na experiência de ponta a ponta, porque um sempre deve passar por um administrador de diretório para adicionar qualquer aplicativo.| 

## <a name="learn-more"></a>Saiba mais
- Para obter informações sobre casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o roteiro e a disponibilidade geográfica para o Azure Stack, sistemas integrados, consulte o white paper: [O Azure Stack: Uma extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para saber mais sobre pacotes e preços do Microsoft Azure Stack [baixar o. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Próximas etapas
[Integração de rede do Datacenter](azure-stack-network.md)