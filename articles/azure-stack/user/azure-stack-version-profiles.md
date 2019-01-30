---
title: Gerenciar perfis de versão de API no Azure Stack | Microsoft Docs
description: Saiba mais sobre perfis de versão de API no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 28ff7c9f6ca5fc6365b3fe1b9a91d2159c8b3f48
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247605"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gerenciar perfis de versão de API no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Perfis de API especificar o provedor de recursos do Azure e a versão da API para pontos de extremidade REST do Azure. Você pode criar clientes personalizados em linguagens diferentes usando perfis de API. Cada cliente usa um perfil de API, entre em contato com o provedor de recursos corretos e a versão de API para o Azure Stack.

Você pode criar um aplicativo para trabalhar com provedores de recursos do Azure sem a necessidade de classificar exatamente qual versão de cada API do provedor de recursos é compatível com o Azure Stack. Alinhar apenas seu aplicativo para um perfil de; o SDK será revertido para a versão de API correta.

Este tópico ajuda você a:

 - Entenda os perfis de API para o Azure Stack.
 - Saiba como você pode usar perfis de API para desenvolver suas soluções.
 - Veja onde encontrar orientações específicas de código.

## <a name="summary-of-api-profiles"></a>Resumo dos perfis de API

- Perfis de API são usados para representar um conjunto de provedores de recursos do Azure e suas versões de API.
- Perfis de API foram criados para você criar modelos de várias nuvens do Azure. Perfis fornecem uma interface compatível e estável.
- Perfis são liberados quatro vezes por ano.
- Convenções de nomenclatura de três perfil são usadas:
    - **latest**  
        Contém as versões de API mais recentes lançadas no Azure global.
    - **yyyy-mm-dd-hybrid**  
    Lançado bianual, essa versão se concentra na estabilidade e a consistência entre várias nuvens. Esse perfil tem como alvo a compatibilidade do Azure Stack ideal.
    - **aaaa-mm-dd-perfil** equilibra a estabilidade ideal e os recursos mais recentes.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Os perfis de API do Azure e compatibilidade do Azure Stack

Os perfis de API do Azure mais recentes não são compatíveis com o Azure Stack. Você pode usar as seguintes convenções de nomenclatura para identificar quais perfis devem ser usar para suas soluções do Azure Stack.

**Mais recente**  
Esse perfil tem as mais recentes versões de API encontradas no Azure global, que não funcionam no Azure Stack. **Mais recente** tem o maior número de alterações significativas. O perfil coloca à parte, estabilidade e compatibilidade com outras nuvens. Se você estiver tentando usar as versões mais recentes de API, **mais recente** é o perfil que você deve usar.

**Yyyy-mm-dd-hybrid**  
Este perfil é lançado em março e setembro todo ano. Ele tem melhor estabilidade e compatibilidade com várias nuvens. **Aaaa-mm-dd-hybrid** foi projetado para ter como destino o global Azure e o Azure Stack. As versões de API do Azure listadas neste perfil será o mesmo aqueles que estão listados no Azure Stack. Você pode usar esse perfil para desenvolver código para soluções de nuvem híbrida.

**yyyy-mm-dd-profile**  
Este perfil é liberado para o Azure global em junho e dezembro. Ele não funciona com o Azure Stack; Normalmente, haverá muitas alterações significativas. Embora ele equilibra estabilidade ideal e os recursos mais recentes, a diferença entre **mais recente** e esse perfil é que **mais recente** sempre consiste nas versões de API mais recentes, independentemente de quando foi a API lançado. Por exemplo, se uma nova versão de API é criada para a API de computação no futuro, essa versão de API é listado na **mais recente**, mas não na **aaaa-mm-dd-perfil** perfil, porque esse perfil já existe. **aaaa-mm-dd-perfil** abrange as versões mais recentes lançadas antes de junho ou antes de dezembro de.

## <a name="azure-resource-manager-api-profiles"></a>Perfis do Gerenciador de recursos de API do Azure

O Azure Stack não usa a versão mais recente das versões da API encontrada no Azure global. Quando você cria uma solução, você deve encontrar a versão de API para cada provedor de recursos do Azure que é compatível com o Azure Stack.

Em vez de cada provedor de recursos e a versão específica com suporte do Azure Stack de pesquisa, você pode usar um perfil de API. O perfil especifica um conjunto de provedores de recursos e as versões de API. O SDK ou uma ferramenta interna com o SDK, será revertido para o destino `api-version` especificada no perfil. Com perfis de API, você pode especificar uma versão de perfil que se aplica a um modelo inteiro e em tempo de execução, o Azure Resource Manager seleciona a versão correta do recurso.

Perfis de API trabalharem com ferramentas que usam o Azure Resource Manager, PowerShell, CLI do Azure, o código fornecido no SDK e o Microsoft Visual Studio. Ferramentas e SDKs podem usar perfis para ler de qual versão dos módulos e bibliotecas para incluir ao criar um aplicativo.

Por exemplo, se você usar o PowerShell para criar uma conta de armazenamento usando o **Microsoft. Storage** provedor de recursos, que dá suporte ao **api-version** 2016-03-30 e uma VM usando o  **Microsoft. Compute** provedor de recursos com **api-version** 2015-12-01, você deve pesquisar qual módulo de PowerShell suporta 2016-03-30 para o armazenamento e qual módulo dá suporte a 2015-02-01 para a computação e, em seguida, instalá-los. Em vez disso, você pode usar um perfil. Use o cmdlet `Install-Profile <profilename>`, e a versão correta dos módulos do PowerShell carrega.

Da mesma forma, ao usar o SDK do Python para compilar um aplicativo baseado em Python, você pode especificar o perfil. O SDK carrega os módulos corretos para os provedores de recursos que você especificou no script.

Como desenvolvedor, você pode se concentrar em escrever sua solução. Em vez de pesquisar sobre quais versões de API, provedor de recursos e nuvem funcionam juntos, você pode usar um perfil e saber que seu código funciona em todas as nuvens que dão suporte a esse perfil.

## <a name="api-profile-code-samples"></a>Exemplos de código de perfil de API

Você pode encontrar exemplos de código para ajudá-lo a integrar sua solução ao seu idioma preferencial com o Azure Stack usando os perfis. No momento, você pode encontrar diretrizes e exemplos para os seguintes idiomas:

- **.NET** você pode usar o perfil de API do .NET para obter a versão mais recente e mais estável de cada tipo de recurso em um pacote de provedor de recursos. Para obter mais informações, consulte [perfis de versão da API de uso com o .NET no Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Você pode usar o **AzureRM.Bootstrapper** disponíveis por meio da Galeria do PowerShell para obter os cmdlets do PowerShell necessários para trabalhar com perfis de versão da API do módulo. Para obter informações, consulte [perfis de versão da API de uso para o PowerShell](azure-stack-version-profiles-powershell.md).
- **CLI do Azure**  
Você pode atualizar a configuração do seu ambiente para usar o perfil de versão de API específico do Azure Stack. Para obter informações, consulte [perfis de versão da API de uso para a CLI do Azure](azure-stack-version-profiles-azurecli2.md).
- **Go**  
No SDK Go, um perfil é uma combinação de diferentes tipos de recursos com diferentes versões de serviços diferentes. Perfis estão disponíveis em perfis/caminho, com a versão na **AAAA-MM-DD** formato. Para obter informações, consulte [perfis de versão da API de uso para linguagem Go](azure-stack-version-profiles-go.md).
- **Ruby**  
O SDK do Ruby do Azure Stack Resource Manager fornece ferramentas para ajudá-lo a criar e gerenciar sua infraestrutura. Provedores de recursos no SDK incluem computação, redes virtuais e armazenamento com a linguagem Ruby. Para obter informações, consulte [perfis de versão da API de uso com o Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
O SDK do Python dá suporte a perfis de versão da API para direcionar diferentes plataformas de nuvem, como o Azure Stack e o Azure global. Você pode usar perfis de API para criar soluções para uma nuvem híbrida. Para obter informações, consulte [perfis de versão da API de uso com o Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Próximas etapas

* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md)
* [Examine os detalhes sobre as versões de API do provedor de recursos com suporte pelos perfis](azure-stack-profiles-azure-resource-manager-versions.md).
