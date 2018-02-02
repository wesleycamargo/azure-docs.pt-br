---
title: "Sistemas integrados de decisões de implantação desconectado do Azure para a pilha do Azure | Microsoft Docs"
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
ms.openlocfilehash: afea4b23aca322321b4096cdfdc9d30b087a2f30
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Sistemas integrados de decisões de pilha do Azure de planejamento de implantação desconectada do Azure
Depois que você decidir [como integrará pilha do Azure em seu ambiente de nuvem híbrida](azure-stack-deployment-decisions.md), em seguida, finalizar suas decisões de implantação da pilha do Azure.

Com a desconectada da opção de implantação do Azure, você pode implantar e usar o Azure pilha sem uma conexão à Internet. No entanto, com uma implantação desconectada, você está limitado a um repositório de identidades do AD FS e o modelo de cobrança baseado na capacidade. 

Escolha esta opção se você:
- Ter segurança ou outras restrições que exigem que você implante a pilha do Azure em um ambiente que não está conectado à Internet.
- Deseja bloquear dados (incluindo dados de uso) que está sendo enviado para o Azure.
- Deseja usar o Azure pilha puramente como uma solução de nuvem privada que é implantada em sua Intranet corporativa e não estiver interessado em cenários híbridos.

> [!TIP]
> Às vezes, esse tipo de ambiente também é chamado como um "cenário submarino".

Uma implantação desconectada estritamente significa que você não pode mais tarde conectar sua instância de pilha do Azure do Azure para cenários VM de locatário híbridos. Isso significa que você não tem conectividade com o Azure durante a implantação ou você não quiser usar o Active Directory do Azure como armazenamento de identidade. No entanto, se você quiser ter conectividade com o Azure após a implantação, independentemente do que você deseja usar como armazenamento de identidade, você deve escolher a conectar-se a opção de implantação do Azure. 

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Recursos que são prejudicadas ou ficar indisponíveis em implantações desconectadas 
Pilha do Azure foi projetada para funcionar melhor quando conectado ao Azure, portanto, é importante observar que há alguns recursos e funcionalidade prejudicada ou completamente indisponível no modo desconectado. 

|Recurso|Impacto em modo desconectado|
|-----|-----|
|Implantação da VM com a extensão de DSC para configurar pós-implantação de VM|Extensão de DSC prejudicada - parece com a Internet para o WMF mais recente.|
|Implantação da VM com a extensão de Docker para executar comandos do Docker|Usuários portadores de deficiência – Docker verificará na Internet para a versão mais recente e essa verificação falhará.|
|Links de documentação no Portal de pilha do Azure|Indisponível – Links como fornecer comentários, ajuda, Quickstart, etc. que usam um URL da Internet não funcionará.|
|Alerta correção/atenuação que faz referência a um guia de correção online|Disponível – nenhuma correção de alerta links que usam que um URL da Internet não funcionará.|
|Distribuição de mercado – a capacidade de selecionar e adicionar pacotes de galeria diretamente no Azure Marketplace|Disponível – Este recurso requer conectividade com o Azure e uma conta do Active Directory do Azure.|
|Usando contas de Federação do Active Directory do Azure para gerenciar uma implantação de pilha do Azure|Disponível – Este recurso requer conectividade com o Azure. O AD FS com uma instância local do Active Directory deve ser usado em vez disso.|
|Provedores de recursos como WebApps e SQL|Não disponível - provedores de recursos, como SQL e WebApps requer acesso à Internet para o conteúdo.|
|Interface de Linha de Comando (CLI)|Usuários portadores de deficiência – CLI tem funcionalidade reduzida em termos de autenticação e o provisionamento de princípios de serviço.|
|O Visual Studio – Cloud discovery|Cloud Discovery prejudicada – ou descobrirá nuvens diferentes ou não funcionará em todos os.|
|O Visual Studio – do AD FS|Prejudicada – somente o Visual Studio Enterprise dá suporte ao AD FS.
Telemetria|Não disponível-dados de telemetria para a pilha do Azure, bem como quaisquer pacotes de galeria de terceiros que dependem de dados de telemetria.|
|Certificados|Não disponível-conectividade com a Internet é necessário para serviços de lista de revogação de certificados (CRL) e protocolo de Status de certificado Online (OSCP) no contexto de HTTPS.|
|Key-Vault|Prejudicada – um caso de uso comum para o Cofre de chaves é ter um aplicativo ler segredos em tempo de execução. Para este aplicativo precisa de uma entidade de serviço no diretório. No Active Directory do Azure, usuários regulares (não administradores) por padrão são permitidas para adicionar entidades de serviço. No AD (usando o AD FS) não estiverem. Isso coloca uma barreira na experiência de ponta a ponta porque um sempre deve passar por um administrador de diretório para adicionar qualquer aplicativo.| 

## <a name="learn-more"></a>Saiba mais
- Para obter informações sobre os casos de uso, compra, parceiros e fornecedores de hardware de OEM, consulte o [Azure pilha](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o mapa e a disponibilidade de replicação geográfica do Azure pilha sistemas integrados, consulte o white paper: [pilha do Azure: extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Para saber mais sobre pacotes e preços de pilha do Microsoft Azure [baixar o PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Próximas etapas
[Integração de rede do Datacenter](azure-stack-network.md)