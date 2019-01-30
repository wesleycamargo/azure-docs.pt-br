---
title: Arquitetura de identidade para o Azure Stack | Microsoft Docs
description: Saiba mais sobre a arquitetura de identidade que você pode usar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: b739db654a182433bbe1f47528d1ab99f1b10c08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242154"
---
# <a name="identity-architecture-for-azure-stack"></a>Arquitetura de identidade para o Azure Stack
Antes de escolher um provedor de identidade para uso com o Azure Stack, compreenda as diferenças importantes entre as opções do Azure Active Directory (Azure AD) e os serviços de Federação do Active Directory (AD FS). 

## <a name="capabilities-and-limitations"></a>Recursos e limitações 
O provedor de identidade que você escolher pode limitar suas opções, incluindo suporte para multilocação. 

  

|Cenário ou recurso        |AD do Azure  |AD FS  |
|------------------------------|----------|-------|
|Conectado à internet     |Sim       |Opcional|
|Suporte a multilocatário     |Sim       |Não       |
|Itens de oferta no Marketplace |Sim       |Sim. Requer o uso do [Sindicalização de Marketplace offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) ferramenta.|
|Suporte para o Active Directory Authentication Library (ADAL) |Sim |Sim|
|Suporte para as ferramentas, como a CLI do Azure, o Visual Studio e o PowerShell  |Sim |Sim|
|Criar entidades de serviço por meio do portal do Azure     |Sim |Não |
|Criar entidades de serviço com certificados      |Sim |Sim|
|Criar entidades de serviço com os segredos (chaves)    |Sim |Não |
|Aplicativos podem usar o serviço de gráfico           |Sim |Não |
|Aplicativos podem usar o provedor de identidade para entrar |Sim |Sim. Exige a federação com o local de aplicativos instâncias do AD FS. |

## <a name="topologies"></a>Topologias
As seções a seguir discutem as várias topologias de identidade que você pode usar.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: topologia de único locatário 
Por padrão, quando você instala o Azure Stack e usa o Azure AD, o Azure Stack usa uma topologia de único locatário. 

Uma topologia de locatário único é útil quando:
- Todos os usuários fazem parte do mesmo locatário.
- Um provedor de serviço hospeda uma instância do Azure Stack para uma organização. 

![Topologia de único locatário do Azure Stack com o Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Essa topologia apresenta as seguintes características:
- O Azure Stack registra todos os aplicativos e serviços com o Azure AD mesmo diretório do locatário. 
- O Azure Stack autentica apenas os usuários e aplicativos desse diretório, incluindo tokens. 
- Identidades de usuários de locatário e administradores (operadores de nuvem) estão no mesmo locatário do diretório. 
- Para habilitar um usuário de outro diretório acessar este ambiente do Azure Stack, você deverá [convidar o usuário como convidado](azure-stack-identity-overview.md#guest-users) ao diretório do locatário. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologia de vários locatário
Operadores de nuvem podem configurar o Azure Stack para permitir o acesso a aplicativos por locatários de um ou mais organizações. Os usuários acessam aplicativos por meio do portal do usuário. Nessa configuração, o portal de administração (usado pelo operador de nuvem) é limitado a usuários de um único diretório. 

Uma topologia de multilocatário é útil quando:
- Quer um provedor de serviços permitir que os usuários de várias organizações para acessar o Azure Stack.

![Topologia de vários locatário do Azure Stack com o Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Essa topologia apresenta as seguintes características:
- Acesso aos recursos deve ser em uma base por organização. 
- Os usuários de uma organização devem ser não é possível conceder acesso aos recursos para os usuários que estão fora da sua organização. 
- Identidades para os administradores (operadores de nuvem) podem estar em um locatário de diretório separado das identidades dos usuários. Essa separação fornece isolamento de conta no nível do provedor de identidade. 
 
### <a name="ad-fs"></a>AD FS  
A topologia do AD FS é necessário quando qualquer uma das seguintes condições for verdadeira:
- O Azure Stack não se conecta à internet.
- O Azure Stack pode se conectar à internet, mas se você optar por usar o AD FS para o seu provedor de identidade.
  
![Topologia de pilha do Azure usando o AD FS](media/azure-stack-identity-architecture/adfs.png)

Essa topologia apresenta as seguintes características:
- Para dar suporte o uso dessa topologia em produção, você deve integrar a instância do AD FS do Azure Stack interna com uma instância existente do AD FS que é apoiada pelo Active Directory, por meio de uma relação de confiança de Federação. 
- Você pode integrar o serviço de grafo no Azure Stack com a instância existente do Active Directory. Você também pode usar o serviço de API do Graph baseado em OData que dá suporte a APIs que são consistentes com a API do Graph do Azure AD. 

  Para interagir com a instância do Active Directory, a API do Graph requer credenciais do usuário da sua instância do Active Directory que tem permissões somente leitura. 
  - A instância do AD FS interna se baseia no Windows Server 2016. 
  - As instâncias do AD FS e o Active Directory devem ser baseadas no Windows Server 2012 ou posterior. 
  
  Entre a instância do Active Directory e a instância do AD FS interna, interações não são restritas para OpenID Connect, e eles podem usar qualquer protocolo com suporte mútuo. 
  - Contas de usuário são criadas e gerenciadas na sua instância do Active Directory local.
  - As entidades de serviço e os registros de aplicativos são gerenciados na instância do Active Directory internos.



## <a name="next-steps"></a>Próximas etapas
- [Visão geral da identidade](azure-stack-identity-overview.md)   
- [Integração do Datacenter - identidade](azure-stack-integrate-identity.md)
