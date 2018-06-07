---
title: Gerenciar perfis de versão de API na pilha do Azure | Microsoft Docs
description: Saiba mais sobre perfis de versão de API na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: adbe88a44ac38868a68a6845c328ef4cf7fba60c
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604430"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gerenciar perfis de versão de API na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Perfis de API especificar o provedor de recursos do Azure e a versão de API para pontos de extremidade REST do Azure. Você pode criar clientes personalizados em idiomas diferentes usando perfis de API. Cada cliente usa um perfil de API para entrar em contato com o provedor de recursos corretos e a versão de API para a pilha do Azure.

Você pode criar um aplicativo para trabalhar com provedores de recursos do Azure sem a necessidade de classificar exatamente qual versão de cada provedor de recursos API é compatível com a pilha do Azure. Alinhar apenas seu aplicativo para um perfil; o SDK é revertida para a versão correta do API.

Este tópico ajuda você a:

 - Entenda os perfis de API de pilha do Azure.
 - Saiba como você pode usar perfis de API para desenvolver suas soluções.
 - Veja onde encontrar diretrizes específicas de código.

## <a name="summary-of-api-profiles"></a>Resumo dos perfis de API

- Perfis de API são usados para representar um conjunto de provedores de recursos do Azure e suas versões de API.
- Perfis de API foram criados para criar modelos em várias nuvens do Azure. Perfis são projetados para atender às suas necessidades de uma interface compatível e estável.
- Perfis são liberados quatro vezes por ano.
- Convenções de nomenclatura de três perfil são usadas:
    - **latest**  
        Contém as versões de API mais recentes liberadas no Azure global.
    - **yyyy-mm-dd-hybrid**  
    Liberado em um ritmo inserir, nesta versão se concentra na estabilidade e a consistência entre várias nuvens. Esse perfil tem como alvo ideal compatibilidade de pilha do Azure.
    - **aaaa-mm-dd-perfil** fica entre melhor estabilidade e os recursos mais recentes.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Perfis de API do Azure e compatibilidade de pilha do Azure

Os perfis de API do Azure mais recentes não são compatíveis com a pilha do Azure. Você pode usar as convenções de nomenclatura a seguir para identificar quais perfis devem ser usado para suas soluções de pilha do Azure.

**Mais recente**  
Esse perfil tem as versões de API mais atualizadas, encontradas no Azure global, o que não funcionará na pilha do Azure. **Última** tem o maior número de alterações significativas. O perfil coloca lado estabilidade e a compatibilidade com outras nuvens. Se você estiver tentando usar as versões de API mais atualizadas, **mais recente** é o perfil que você deve usar.

**Aaaa-mm-dd-híbrido**  
Este perfil é liberado em março e setembro de cada ano. Esse perfil tem ideal estabilidade e a compatibilidade com várias nuvens. **Aaaa-mm-dd-híbrida** destina-se ao destino global Azure e a pilha do Azure. As versões de API do Azure listadas neste perfil serão iguais às listadas na pilha do Azure. Você pode usar este perfil para desenvolver o código para soluções de nuvem híbrida.

**yyyy-mm-dd-profile**  
Este perfil é liberado para o Azure global em junho e dezembro. Esse perfil não funcionarão em relação a pilha do Azure; Normalmente, haverá muitas alterações de quebra. Embora ele se encontra entre melhor estabilidade e recursos mais recentes, a diferença entre **mais recente** e esse perfil é que **mais recente** sempre consistirá as versões de API mais recentes, independentemente de quando a API foi lançada. Por exemplo, se uma nova versão de API é criada para a API de computação amanhã, essa versão do API será listado no **mais recente**, mas não no **aaaa-mm-dd-perfil** porque este perfil já existe.  **aaaa-mm-dd-perfil** abrange as versões mais atualizadas liberadas antes de junho ou antes de dezembro.

## <a name="azure-resource-manager-api-profiles"></a>Perfis de API do Gerenciador de recursos do Azure

A pilha do Azure não usa a versão mais recente das versões de API encontradas no Azure global. Quando você estiver criando uma solução, você precisa localizar a versão de API para cada provedor de recursos do Azure que é compatível com a pilha do Azure.

Em vez de pesquisar cada provedor de recursos e a versão específica com suporte pela pilha do Azure, você pode usar um perfil de API. O perfil especifica um conjunto de provedores de recursos e as versões de API. O SDK ou uma ferramenta com o SDK, será revertido para a api-version de destino especificada no perfil. Com perfis de API, você pode especificar uma versão de perfil que se aplica a um modelo inteiro e, em tempo de execução, o Azure Resource Manager seleciona a versão correta do recurso.

Perfis de API trabalharem com ferramentas que usam o Gerenciador de recursos do Azure, como o PowerShell, CLI do Azure, o código fornecido no SDK e o Microsoft Visual Studio. Ferramentas e SDKs podem usar perfis para ler a versão dos módulos e bibliotecas para incluir ao criar um aplicativo.

Por exemplo, se usar o PowerShell para criar um armazenamento de conta usando o **Microsoft** provedor de recursos, que dá suporte à versão de api 2016-03-30 e uma VM usando o provedor de recursos Microsoft. Compute com uma api versão 2015-12-01 , você precisa pesquisar que dá suporte a módulo PowerShell 2016-03-30 para o armazenamento e qual módulo dá suporte a 2015-02-01 para a computação e instalá-los. Em vez disso, você pode usar um perfil. Use o cmdlet * * instalar perfil * profilename * e PowerShell carrega a versão correta dos módulos.

Da mesma forma, ao usar o SDK de Python para compilar um aplicativo baseado em Python, você pode especificar o perfil. O SDK carrega os módulos corretos para os provedores de recursos que você especificou no script.

Como desenvolvedor, você pode se concentrar em escrever sua solução. Em vez de Pesquisar quais versões de api, o provedor de recursos e quais nuvem funciona em conjunto, se você usa um perfil e sabe que seu código funcione em todas as nuvens que oferecem suporte a esse perfil.

## <a name="api-profile-code-samples"></a>Exemplos de código de perfil de API

Você pode encontrar exemplos de código para ajudá-lo a integrar a solução com o idioma de preferência com pilha do Azure usando os perfis. No momento, você encontrará diretrizes e exemplos para os seguintes idiomas:

- **PowerShell**  
Você pode usar o **AzureRM.Bootstrapper** disponíveis por meio da Galeria do PowerShell para obter os cmdlets do PowerShell necessários para trabalhar com perfis de versão de API do módulo. Para obter informações, consulte [perfis de versão de API de uso do PowerShell](azure-stack-version-profiles-powershell.md).
- **CLI 2.0 do Azure**  
Você pode atualizar sua configuração de ambiente para usar o perfil de versão de API específico da pilha do Azure. Para obter informações, consulte [perfis de versão de API de uso para Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
No SDK VÁ, um perfil é uma combinação de diferentes tipos de recursos com versões diferentes de serviços diferentes. os perfis estão disponíveis nos perfis / caminho, com a versão no **AAAA-MM-DD** formato. Para obter informações, consulte [perfis de versão de API de uso para ir](azure-stack-version-profiles-go.md).
- **Ruby**  
O SDK do Ruby para o Gerenciador de recursos do Azure pilha fornece ferramentas para ajudá-lo a criar e gerenciar sua infra-estrutura. Provedores de recursos no SDK incluem computação, redes virtuais e armazenamento com a linguagem Ruby. Para obter informações, consulte [perfis de versão de API de uso com Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
- O SDK de Python dá suporte a perfis de versão de API para destinar plataformas de nuvem diferente, como a pilha do Azure e o Azure global. Você pode usar perfis de API na criação de soluções para uma nuvem híbrida. Para obter informações, consulte [perfis de versão de API de uso com Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Próximas etapas

* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)
* [Examine os detalhes sobre as versões de API do provedor de recursos compatíveis com os perfis](azure-stack-profiles-azure-resource-manager-versions.md).