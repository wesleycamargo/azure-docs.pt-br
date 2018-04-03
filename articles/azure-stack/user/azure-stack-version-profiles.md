---
title: Gerenciar perfis de versão de API na pilha do Azure | Microsoft Docs
description: Saiba mais sobre perfis de versão de API na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gerenciar perfis de versão de API na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Perfis de API especificar o provedor de recursos do Azure e a versão de API para pontos de extremidade REST do Azure. Você pode criar clientes personalizados em idiomas diferentes usando perfis de API. Cada cliente usa um perfil de API para entrar em contato com o provedor de recursos à direita e a versão de API para a pilha do Azure. 

Você pode criar um aplicativo para trabalhar com provedores de recursos do Azure sem a necessidade de classificar exatamente qual versão de cada provedor de recursos API é compatível com a pilha do Azure. Alinhar apenas seu aplicativo para um perfil; o SDK é revertida para as versões de API à direita.


Este tópico ajuda você a:
 - Entenda os perfis de API de pilha do Azure.
 - Como você pode usar perfis de API para desenvolver suas soluções.
 - Onde procurar diretrizes específicas de código.

## <a name="summary-of-api-profiles"></a>Resumo dos perfis de API

- Perfis de API são usados para representar um conjunto de provedores de recursos do Azure e suas versões de API.
- Perfis de API foram criados para os desenvolvedores criem modelos entre várias nuvens do Azure. Eles são projetados para atender às suas necessidades de uma interface compatível e estável.
- Perfis são liberados quatro vezes por ano.
- Convenções de nomenclatura de três perfil são:
    - **latest**  
        Versões de API mais recentes liberadas no Azure.
    - **yyyy-mm-dd-hybrid**  
    Liberado em um ritmo inserir, nesta versão voltadas para estabilidade e a consistência entre várias nuvens.
    - **yyyy-mm-dd-profile**  
    Fica entre melhor estabilidade e os recursos mais recentes.

## <a name="azure-resource-manager-api-profiles"></a>Perfis de API do Gerenciador de recursos do Azure

A pilha do Azure não usa a versão mais recente das versões de API encontradas no Azure global. Ao criar sua própria solução, você precisa localizar a versão de API para cada provedor de recursos no Azure que é compatível com a pilha do Azure.

Em vez de pesquisar cada provedor de recursos e a versão específica com suporte pela pilha do Azure, você pode usar um perfil de API. O perfil especifica um conjunto de provedores de recursos e as versões de API. O SDK ou uma ferramenta com o SDK, será revertido para a api-version de destino especificada no perfil. Com perfis de API, você pode especificar uma versão de perfil que se aplica a um modelo inteiro e, em tempo de execução, o Azure Resource Manager seleciona a versão correta do recurso.

Perfis de API trabalharem com ferramentas que usam o Gerenciador de recursos do Azure, como o PowerShell, CLI do Azure, o código fornecido no SDK e o Microsoft Visual Studio. Ferramentas e SDKs podem usar perfis para ler a versão dos módulos e bibliotecas para incluir ao criar um aplicativo.

Por exemplo, se usar o PowerShell para criar um armazenamento de conta usando o **Microsoft** provedor de recursos, que dá suporte à versão de api 2016-03-30 e uma VM usando o provedor de recursos Microsoft. Compute com uma api versão 2015-12-01 , você precisa pesquisar que dá suporte a módulo PowerShell 2016-03-30 para o armazenamento e qual módulo dá suporte a 2015-02-01 para a computação e instalá-los. Em vez disso, você pode usar um perfil. Use o cmdlet * * instalar perfil * profilename * e PowerShell carrega a versão correta dos módulos.

Da mesma forma, ao usar o SDK de Python para compilar um aplicativo baseado em Python, você pode especificar o perfil. O SDK carrega os módulos corretos para os provedores de recursos que você especificou no script.

Como desenvolvedor, você pode se concentrar em escrever sua solução. Em vez de Pesquisar quais versões de api, o provedor de recursos e quais nuvem funciona em conjunto, se você usa um perfil e sabe que seu código funcione em todas as nuvens que oferecem suporte a esse perfil.

## <a name="api-profile-code-samples"></a>Exemplos de código de perfil de API

Você pode encontrar exemplos de código para ajudá-lo a integrar a solução com o idioma de preferência com pilha do Azure usando os perfis. No momento, você encontrará diretrizes e exemplos para os seguintes idiomas:

- **PowerShell**  
Você pode usar o **AzureRM.Bootstrapper** disponíveis por meio da Galeria do PowerShell para obter os cmdlets do PowerShell necessários para trabalhar com perfis de versão de API do módulo.  
Para obter informações, consulte [perfis de versão de API de uso do PowerShell](azure-stack-version-profiles-powershell.md).
- **CLI 2.0 do Azure**  
Você pode atualizar sua configuração de ambiente para usar o perfil de versão de API específico da pilha do Azure.  
Para obter informações, consulte [perfis de versão de API de uso para Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
No SDK VÁ, um perfil é uma combinação de diferentes tipos de recursos com versões diferentes de serviços diferentes. os perfis estão disponíveis nos perfis / caminho, com a versão no **AAAA-MM-DD** formato.  
Para obter informações, consulte [perfis de versão de API de uso para ir](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Próximas etapas
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)
* [Examine os detalhes sobre as versões de API do provedor de recursos compatíveis com os perfis](azure-stack-profiles-azure-resource-manager-versions.md).
