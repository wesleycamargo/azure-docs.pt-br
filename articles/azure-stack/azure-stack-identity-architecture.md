---
title: Arquitetura de identidade para a pilha do Azure | Microsoft Docs
description: "Saiba mais sobre a arquitetura de identidade que você pode usar com a pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Arquitetura de identidade para a pilha do Azure
Antes de escolher um provedor de identidade para usar com a pilha do Azure, compreenda as diferenças importantes entre as opções do Azure Active Directory (AD do Azure) e o Active Directory Federated Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Recursos e limitações 
O provedor de identidade que você escolher pode limitar as opções, incluindo suporte para multilocação. 

  

|Cenário ou recurso        |AD do Azure  |AD FS  |
|------------------------------|----------|-------|
|Conectado à internet     |Sim       |Opcional|
|Suporte para multilocação     |Sim       |Não       |
|Distribuição do Marketplace       |Sim       |Sim - requer o uso do [Syndication offline do Marketplace](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) ferramenta.|
|Suporte para o Active Directory Authentication Library (ADAL) |Sim |Sim|
|Suporte para as ferramentas como interface de linha de comando (CLI) do Azure, o Visual Studio (VS) e o PowerShell  |Sim |Sim|
|Criar entidades de serviço por meio do portal     |Sim |Não |
|Criar entidades de serviço com certificados      |Sim |Sim|
|Criar entidades de serviço com segredos (chaves)    |Sim |Não |
|Os aplicativos podem usar o serviço de gráfico           |Sim |Não |
|Aplicativos podem usar o provedor de identidade para entrar |Sim |Sim - requer aplicativos para federar com seu local do AD FS. |

## <a name="topologies"></a>Topologias
As seções a seguir fornecem informações sobre topologias de identidade, que você pode usar.

### <a name="azure-ad--single-tenant"></a>AD do Azure – único locatário 
Por padrão, quando você instala a pilha do Azure e usa o AD do Azure, a pilha do Azure usa uma topologia de único locatário. 

Uma topologia de locatário único é útil quando:
- Todos os usuários fazem parte do mesmo locatário.
- Um provedor de serviços hospeda uma instância de pilha do Azure para uma organização.  

![Topologia de pilha do Azure usando uma topologia de locatário único com o Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Com esta topologia:
- A pilha do Azure registra todos os aplicativos e serviços para o mesmo Azure AD locatário de diretório. 
- A pilha do Azure autentica apenas os usuários e aplicativos de diretório, incluindo Tokens. 
- Identidades de usuários de locatários e administradores (operadores de nuvem) estão no mesmo locatário de diretório. 
- Para habilitar um usuário em outro diretório para acessar este ambiente de pilha do Azure, você deve [convidar usuários como convidado](azure-stack-identity-overview.md#guest-users) para o diretório do locatário.  

### <a name="azure-ad--multi-tenant"></a>O AD do Azure – multilocatário
Operadores de nuvem podem configurar a pilha do Azure para permitir o acesso a aplicativos por locatários de organizações de um ou mais. Os usuários acessam aplicativos por meio do portal do usuário. Nessa configuração, o portal de administração (usado pelo operador de nuvem) é limitado aos usuários de um único diretório. 

Uma topologia de vários locatários é útil quando:
- Um provedor de serviços deseja permitir que os usuários de várias organizações para acessar a pilha do Azure.

![Topologia de pilha do Azure usando uma topologia de vários locatários com o Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Com esta topologia:
- Acesso a recursos deve ser em uma base por organização. 
- Os usuários de uma organização não devem ser capazes de conceder acesso a recursos para usuários que estão fora de sua organização.  
- Identidades para administradores (operadores de nuvem) podem estar em um locatário de diretório separado que as identidades de usuários. Essa separação fornece isolamento de conta no nível do provedor de identidade. 
 
### <a name="ad-fs"></a>AD FS  
A topologia do AD FS é necessário quando qualquer uma das seguintes condições for verdadeira:
- A pilha do Azure não se conecta à Internet.
- A pilha do Azure pode se conectar à Internet, mas você optar por usar o AD FS para o seu provedor de identidade.
  
![Topologia de pilha do Azure usando o AD FS](media/azure-stack-identity-architecture/adfs.png)

Com esta topologia:
- Para dar suporte ao uso em produção, você deve integrar a instância de pilha do Azure AD FS interna com uma instância existente do AD FS feita pelo Active Directory (AD), por meio de uma relação de confiança de Federação. 
- Você pode integrar o serviço de gráfico na pilha do Azure com seu AD existente.  Você também pode usar o OData com base em serviço de API do Graph que dá suporte a APIs que são consistentes com a API do Azure AD Graph.  

  Para interagir com o AD, a API do Graph exige credenciais de usuário do AD do que têm permissão somente leitura do AD. 
  - O AD FS interno baseia-se no Server 2016. 
  - O AD FS e o anúncio devem ser baseado no Server 2012 ou anterior. 
  
  Entre o AD e o interna do AD FS, interações não são restritos para OpenID Connect e podem usar qualquer protocolo mutuamente com suporte.  
  - Contas de usuário são criadas e gerenciadas em suas instalações AD.
  - Entidades de serviço e os registros de aplicativos são gerenciados no AD interno.



## <a name="next-steps"></a>Próximas etapas
- [Visão geral da identidade](azure-stack-identity-overview.md)   
- [Integração do data center - identidade](azure-stack-integrate-identity.md)