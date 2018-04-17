---
title: Visão geral da identidade para a pilha do Azure | Microsoft Docs
description: Saiba mais sobre os sistemas de identidade que você pode usar com a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 607c7938a789b3504a425057645b291bd4c8235b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Visão geral da identidade para a pilha do Azure

Pilha do Azure requer o Azure Active Directory (AD do Azure) ou o Active Directory Federation Services (AD FS), com o apoio de Active Directory como um provedor de identidade. A escolha de um provedor é uma decisão única que você faz quando você implanta pilha do Azure. Os conceitos e detalhes de autorização neste artigo podem ajudá-lo a escolher entre os provedores de identidade. 

A escolha do Azure AD ou AD FS pode ser determinada pelo modo no qual você implanta a pilha do Azure: 
- Quando você implantá-lo em um modo conectado, você pode usar o Azure AD ou AD FS. 
- Quando você implantá-lo em modo desconectado, sem uma conexão à internet, somente o AD FS tem suporte.

Para obter mais informações sobre as opções, que dependem de seu ambiente de pilha do Azure, consulte os seguintes artigos:
- Kit de implantação do Azure pilha: [considerações sobre a identidade](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemas integrados de pilha do Azure: [sistemas integrados de decisões de pilha do Azure de planejamento de implantação](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Conceitos comuns de identidade
As seções a seguir abordam conceitos comuns sobre provedores de identidade e seu uso na pilha do Azure.

![Terminologia para provedores de identidade](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Organizações e locatários de diretório
Um diretório é um contêiner que armazena informações sobre *usuários*, *aplicativos*, *grupos*, e *entidades de serviço*.
 
Um locatário de diretório é um *organização*, como a Microsoft ou sua própria empresa. 
- AD do Azure suporta vários locatários e pode dar suporte a várias organizações, cada um em seu próprio diretório. Se você usa o AD do Azure e tiver vários locatários, você pode conceder a aplicativos e usuários de acesso de um locatário para outros locatários do mesmo diretório.
- O AD FS oferece suporte a apenas um único locatário e, portanto, somente uma única organização. 

### <a name="users-and-groups"></a>Usuários e grupos
Contas de usuário (identidades) são as contas padrão que pessoas se autenticar usando uma ID de usuário e senha. Grupos podem incluir outros grupos ou usuários. 

Como criar e gerenciar usuários e grupos depende da solução de identidade que você usar. 

Na pilha do Azure, as contas de usuário: 
- São criados no *username@domain* formato. Embora o AD FS mapeia as contas de usuário a uma instância do Active Directory, o AD FS não suporta o uso do  *\<domínio >\<alias >* formato. 
- Pode ser configurado para usar a autenticação multifator. 
- São restritos para o diretório onde primeiro registro, que é o diretório da organização.
- Podem ser importados de seus diretórios locais. Para obter mais informações, consulte [integrar seus diretórios locais com o Active Directory do Azure](/azure/active-directory/connect/active-directory-aadconnect). 

Quando você entrar no portal do locatário da sua organização, você usar o *https://portal.local.azurestack.external* URL. 

### <a name="guest-users"></a>Usuários convidados
Usuários convidados são contas de usuário de outros locatários de diretório que receberam acesso a recursos em seu diretório. Para dar suporte a usuários convidados, você usa o AD do Azure e habilita o suporte para multilocação. Quando o suporte está habilitado, você pode convidar usuários de convidado para acessar recursos em seu locatário de diretório, que por sua vez, permite sua colaboração com organizações externas. 

Para convidar usuários convidados, operadores de nuvem e os usuários podem usar [colaboração B2B do Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Usuários convidados obtém acesso a documentos, aplicativos e recursos do seu diretório e manter o controle sobre seus próprios recursos e dados. 

Como um usuário convidado, você pode entrar locatário de diretório da outra organização. Para fazer isso, você acrescentar o nome de diretório da organização para o portal de URL. Por exemplo, se você pertence à organização Contoso e para entrar no diretório da Fabrikam, use https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplicativos
Você pode registrar aplicativos para o Azure AD ou AD FS e, em seguida, oferecem aplicativos para usuários em sua organização. 

Os aplicativos incluem:
- **Aplicativo Web**: os exemplos incluem o portal do Azure e o Azure Resource Manager. Eles oferecem suporte a chamadas de API da Web. 
- **Cliente nativo**: exemplos do PowerShell do Azure, o Visual Studio e a CLI do Azure.

Aplicativos podem oferecer suporte a dois tipos de aluguel: 
- **Único locatário**: dá suporte a usuários e serviços somente do mesmo diretório onde o aplicativo está registrado. 

  > [!NOTE]    
  > Como o AD FS oferece suporte a apenas um único diretório, aplicativos que você cria em uma topologia do AD FS são, por design, os aplicativos de locatário único.

- **Multilocatário**: oferece suporte ao uso por usuários e serviços do diretório onde o aplicativo é registrado e diretórios adicionais de locatário. Com aplicativos multilocatários, os usuários de outra pode de diretório (outro locatário do Azure AD) do locatário entrarem no seu aplicativo. 

  Para obter mais informações sobre multilocação, consulte [habilitar multilocação](azure-stack-enable-multitenancy.md). 

  Para obter mais informações sobre como desenvolver um aplicativo multilocatário, consulte [aplicativos multilocatários](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando você registra um aplicativo, você pode criar dois objetos:

- **Objeto de aplicativo**: A representação global do aplicativo em todos os locatários. Essa relação é-para-um com o aplicativo de software e existe somente no diretório onde o aplicativo é registrado pela primeira vez.

- **Objeto de entidade de serviço**: uma credencial que é criada para um aplicativo no diretório onde o aplicativo é registrado pela primeira vez. Uma entidade de serviço também é criada no diretório de cada locatário adicional em que esse aplicativo é usado. Essa relação pode ser um-para-muitos com o aplicativo de software. 

Para saber mais sobre o aplicativo e objetos de serviço, consulte [objetos de aplicativo e serviço principal no Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Entidades de serviço 
Uma entidade de serviço é um conjunto de *credenciais* para um aplicativo ou serviço que concedem acesso aos recursos na pilha do Azure. O uso de uma entidade de serviço separa as permissões do aplicativo das permissões do usuário do aplicativo.

Uma entidade de serviço é criada em cada locatário em que o aplicativo é usado. A entidade de serviço estabelece uma identidade para entrar e acessar recursos (como usuários) que são protegidos por locatário. 

- Um aplicativo de único locatário tem apenas uma entidade de serviço, no diretório onde ele é criado. Essa entidade de serviço é criada e consente que está sendo usado durante o registro do aplicativo. 
- Um aplicativo multilocatário ou a API tem uma entidade de serviço é criada em cada locatário em que um usuário de locatário consente o uso do aplicativo. 

As credenciais para entidades de serviço podem ser um de uma chave que é gerada por meio do portal do Azure ou um certificado. O uso de um certificado é adequado para automação, como certificados são considerados mais seguros do que as chaves. 


> [!NOTE]    
> Quando você usar o AD FS com a pilha do Azure, somente o administrador pode criar entidades de serviço. Com o AD FS, as entidades de serviço requerem certificados e são criadas por meio do ponto de extremidade privilegiado (PEP). Para obter mais informações, consulte [fornecem acesso de aplicativos ao Azure pilha](azure-stack-create-service-principals.md).

Para saber mais sobre as entidades de serviço para a pilha do Azure, consulte [criar entidades de serviço](azure-stack-create-service-principals.md).


### <a name="services"></a>Serviços
Serviços na pilha do Azure que interagem com o provedor de identidade são registrados como aplicativos com o provedor de identidade. Como aplicativos, o registro permite que um serviço autenticar com o sistema de identidade. 

Todos os serviços do Azure usam [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolos e [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) para estabelecer sua identidade. Porque o Azure AD e o AD FS usam protocolos de forma consistente, você pode usar [biblioteca de autenticação do Azure Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para autenticar no local ou no Azure (em um cenário conectado). Com a ADAL, você também pode usar ferramentas como o Azure PowerShell e a CLI do Azure para gerenciamento de recursos de nuvem e local. 


### <a name="identities-and-your-identity-system"></a>Identidades e seu sistema de identidade 
Identidades para a pilha do Azure incluem contas de usuário, grupos e entidades de serviço. 

Quando você instala a pilha do Azure, vários serviços e aplicativos internos se registrar automaticamente ao seu provedor de identidade no locatário de diretório. Alguns serviços registrados são usados para administração. Outros serviços estão disponíveis para os usuários. Os registros padrão oferecem identidades de serviços principais que podem interagir entre si e com identidades que forem adicionadas posteriormente.

Se você configurar o AD do Azure com multilocação, alguns aplicativos são propagadas para os diretórios de novo. 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
 

### <a name="authentication-by-applications-and-users"></a>Autenticação por aplicativos e usuários
  
![Identidade entre camadas da pilha do Azure](media/azure-stack-identity-overview/identity-layers.png)

Para usuários e aplicativos, a arquitetura da pilha do Azure é descrita por quatro camadas. Interações entre cada uma dessas camadas podem usar diferentes tipos de autenticação.


|Camada    |Autenticação entre camadas  |
|---------|---------|
|Ferramentas e clientes, como o portal de administração     | Para acessar ou modificar um recurso na pilha do Azure, ferramentas e os clientes usam um [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) para fazer uma chamada para o Gerenciador de recursos do Azure. <br>Gerenciador de recursos do Azure valida o JSON Web Token e exibe o *declarações* no token emitido para estimar o nível de autorização de usuário ou entidade de serviço tem na pilha do Azure. |
|Gerenciador de recursos do Azure e seus serviços de núcleo     |Gerenciador de recursos do Azure se comunica com os provedores de recursos para transferir a comunicação de usuários. <br> Transfere use *imperativo direto* chamadas ou *declarativa* chama via [modelos do Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Provedores de recurso     |Chamadas que são passadas para provedores de recursos são protegidas com autenticação baseada em certificado. <br>Gerenciador de recursos do Azure e o provedor de recursos permanecem na comunicação com a API. Para cada chamada é recebida do Gerenciador de recursos do Azure, o provedor de recursos valida a chamada com esse certificado.|
|Lógica de negócios e infraestrutura     |Provedores de recursos se comunicam com lógica de negócios e infraestrutura usando um modo de autenticação de sua escolha. Os provedores de recursos padrão que acompanham a pilha do Azure usam autenticação do Windows para proteger essa comunicação.|

![Informações necessárias para autenticação](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Autenticar ao Gerenciador de recursos do Azure
Para autenticar com o provedor de identidade e receber um JSON Web Token, você deve ter as seguintes informações: 
1.  **URL para o sistema de identidade (autoridade)**: A URL na qual o seu provedor de identidade pode ser acessado. Por exemplo, *https://login.windows.net*. 
2.  **URI de ID de aplicativo do Azure Resource Manager**: O identificador exclusivo para o Gerenciador de recursos do Azure que está registrado com seu provedor de identidade. Também é exclusiva para cada instalação de pilha do Azure.
3.  **Credenciais**: A credencial que você pode usar para autenticar com o provedor de identidade. 
4.  **URL do Gerenciador de recursos do Azure**: A URL é o local do serviço Gerenciador de recursos do Azure. Por exemplo, *https://management.azure.com* ou *https://management.local.azurestack.external*.

Quando uma entidade de segurança (um cliente, aplicativo ou usuário) faz uma solicitação de autenticação para acessar um recurso, a solicitação deve incluir:
- Credenciais do servidor principal.
- O URI da ID do recurso que deseja que a entidade de segurança para acessar o aplicativo. 

As credenciais são validadas pelo provedor de identidade. O provedor de identidade também valida que o aplicativo do URI de ID é para um aplicativo registrado e a entidade tem os privilégios corretos para obter um token para esse recurso. Se a solicitação for válida, um JSON Web Token é concedido. 

O token deve passar no cabeçalho de uma solicitação para o Gerenciador de recursos do Azure. Gerenciador de recursos do Azure faz o seguinte, em nenhuma ordem específica:
- Valida o *emissor* (iss) de declaração confirmar que o token do provedor de identidade correto. 
- Valida o *público* declaração (aud) para confirmar se o token foi emitido para o Gerenciador de recursos do Azure. 
- Valida o JSON Web Token é assinado com um certificado que é configurado por meio de OpenID é conhecido para o Gerenciador de recursos do Azure. 
- Examine o *emitido em* (iat) e *validade* (exp) declarações para confirmar que o token está ativo e pode ser aceito. 

Quando todas as validações forem concluídas, o Gerenciador de recursos do Azure usa o *um objeto* (oid) e o *grupos* declarações para fazer uma lista de recursos que pode acessar a entidade de segurança. 

![Diagrama do protocolo exchange token](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Usar o controle de acesso baseado em função  
Controle de acesso baseado em função (RBAC) na pilha do Azure é consistente com a implementação no Microsoft Azure. Você pode gerenciar o acesso aos recursos por meio da atribuição de função apropriada de RBAC aos usuários, grupos e aplicativos. Para obter informações sobre como usar o RBAC com pilha do Azure, consulte os seguintes artigos:
- [Introdução ao Controle de Acesso Baseado em Função no Portal do Azure](/azure/role-based-access-control/overview).
- [Use o controle de acesso baseado em função para gerenciar o acesso aos recursos da sua assinatura do Azure](/azure/role-based-access-control/role-assignments-portal).
- [Criar funções personalizadas para o controle de acesso](/azure/role-based-access-control/custom-roles).
- [Gerenciar o controle de acesso baseado em função](azure-stack-manage-permissions.md) na pilha do Azure.


### <a name="authenticate-with-azure-powershell"></a>Autenticar com o PowerShell do Azure  
Detalhes sobre como usar o PowerShell do Azure para autenticar com a pilha do Azure podem ser encontrados em [configurar o ambiente do PowerShell do usuário do Azure pilha](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autenticar com CLI do Azure
Para obter informações sobre como usar o PowerShell do Azure para autenticar com a pilha do Azure, consulte [instalar e configurar a CLI do Azure para uso com o Azure pilha](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de identidade](azure-stack-identity-architecture.md)   
- [Integração do data center - identidade](azure-stack-integrate-identity.md)




