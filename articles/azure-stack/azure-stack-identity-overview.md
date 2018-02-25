---
title: "Visão geral da identidade para a pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre os sistemas de identidade que você pode usar com a pilha do Azure."
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
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Visão geral da identidade para a pilha do Azure

A pilha do Azure requer o Azure AD ou os serviços de federados do Active Directory (AD FS) feito pelo Active Directory (AD) como um provedor de identidade. Os seguintes conceitos e detalhes de autorização podem ajudá-lo a fazer a escolha entre os provedores de identidade. A escolha de um provedor é uma decisão única feitas quando você implanta pilha do Azure.  

Sua escolha entre o Azure AD e o AD FS podem ser limitado pelo modo no qual você implanta a pilha do Azure: 
- Quando você implanta em um modo conectado, você pode usar o Azure AD ou AD FS. 
- Quando você implanta em modo desconectado sem uma conexão à Internet, somente o AD FS tem suporte.

Para obter mais informações sobre essas opções, consulte os seguintes artigos dependendo do ambiente da pilha do Azure:
- Kit de implantação de pilha do Azure, consulte [considerações sobre a identidade](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemas de pilha integrado do Azure, consulte [sistemas integrados de decisões de pilha do Azure de planejamento de implantação](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Conceitos comuns de identidade
As seções a seguir abordam conceitos comuns para provedores de identidade e seu uso na pilha do Azure.
![Terminologia para provedores de identidade](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>As organizações e locatários de diretórios
Um diretório é um contêiner que armazena informações sobre *usuários*, *aplicativos*, *grupos*, e *entidades de serviço*.
 
Um locatário de diretório é um *organização*, como o Microsoft ou sua própria empresa. 
- O AD do Azure suporta vários locatários e pode dar suporte a várias organizações, cada um em seu próprio diretório. Se você usa o AD do Azure e tiver vários locatários, você pode conceder a aplicativos e usuários de acesso de um locatário para outros locatários do mesmo diretório.
- O AD FS oferece suporte a apenas um único locatário e, portanto, uma única organização. 

### <a name="users-and-groups"></a>Usuários e Grupos
Contas de usuário (identidades) são as contas padrão que autenticar usando uma ID de usuário e senha de indivíduos. Grupos podem incluir outros grupos ou usuários. 

Como criar e gerenciar usuários e grupos depende da solução de identidade que você usar. 

Na pilha do Azure, as contas de usuário: 
- São criados no  *username@domain*  formato. Embora o AD FS mapeia as contas de usuário a um Active Directory, o AD FS não suporta o uso do  _&lt;domínio >\<alias >_ formato. 
- Pode ser configurado para usar a autenticação multifator. 
- São restritos para o diretório onde primeiro registro, que é seu diretório de organizações.
- Podem ser importados de seus diretórios locais. Para obter mais informações, consulte [integrar seus diretórios locais com o Active Directory do Azure](/azure/active-directory/connect/active-directory-aadconnect) na documentação do Azure.  

Quando fizer logon no seu portal de locatário de organizações, você pode usar https://portal.local.azurestack.external. 

### <a name="guest-users"></a>Usuários convidados
Usuários convidados são contas de usuário de outros locatários de diretório que receberam acesso a recursos em seu diretório. Para dar suporte a usuário convidado, você deve usar o Azure AD e habilitar o suporte para multilocação. Quando houver suporte, você pode convidar um usuário convidado para acessar os recursos em seu locatário de diretório, que permite a colaboração com organizações externas. 

Para convidar usuários convidados, operadores de nuvem e os usuários podem usar [colaboração B2B do Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Convidou aos usuários obter acesso a documentos, aplicativos e recursos do seu diretório enquanto você manter controle sobre seus próprios recursos e dados. 

Como um usuário convidado, você pode fazer logon no outro locatário do diretório de organizações. Para fazer isso, você deve acrescentar o nome do diretório que as organizações para o portal de URL.  Por exemplo se pertencer contoso.com, mas deseja registrar no diretório Fabrikam, você use https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APLICATIVOS
Você pode registrar aplicativos para o Azure AD ou AD FS e oferecer aos usuários em sua organização. 

Os aplicativos incluem:
- **Aplicativo Web** – exemplos incluem o portal do Azure e o Azure Resource Manager.  Eles oferecem suporte a chamadas de API da Web. 
- **Cliente nativo** – exemplos do PowerShell do Azure, o Visual Studio e a interface de linha de comando (CLI) do Azure.

Aplicativos podem oferecer suporte a dois tipos de aluguel: 
- **Único locatário** aplicativos oferecem suporte a usuários e serviços somente do mesmo diretório onde o aplicativo está registrado. 

  > [!NOTE]    
  > Como o AD FS oferece suporte a apenas um único diretório, aplicativos que você cria em uma topologia do AD FS são por design, os aplicativos de locatário único.
- **Multilocatário** suporte os aplicativos usam pelos usuários e serviços do diretório onde o aplicativo é registrado e dos diretórios de locatário adicionais.  Com aplicativos multilocatários, os usuários de outra pode de diretório (outro locatário do Azure AD) do locatário entrarem no seu aplicativo.     

  Para obter mais informações sobre multilocação, consulte [habilitar multilocação](azure-stack-enable-multitenancy.md).  

  Para obter mais informações sobre como desenvolver um aplicativo multilocatário, consulte [aplicativos multilocatários](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando você registra um aplicativo, os dois objetos são criados:
- **Objeto de aplicativo** – o objeto de aplicativo é a representação global do aplicativo em todos os locatários. Essa relação é-para-um com o aplicativo de software e existe somente no diretório onde o aplicativo é primeiro registra.

 
   
- **Objeto de entidade de serviço** – uma entidade de serviço é uma credencial que é criada para um aplicativo no diretório onde o aplicativo é registrado pela primeira vez. Uma entidade de serviço também é criada no diretório de cada locatário adicional em que esse aplicativo é usado. Essa relação pode ser um um-para-muitos com o aplicativo de software.   

Para saber mais sobre o aplicativo e objetos de serviço, consulte [objetos de aplicativo e serviço principal no Azure Active Directory (AD do Azure)](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Entidades de serviço 
Uma entidade de serviço é um conjunto de *credenciais* para um aplicativo ou serviço que concedem acesso aos recursos na pilha do Azure. Uso de uma entidade de serviço separa as permissões do aplicativo das permissões do usuário que usa o aplicativo.

Uma entidade de serviço é criada em cada locatário em que o aplicativo é usado. A entidade de serviço estabelece uma identidade para entrar e acessar recursos (como usuários) que são protegidos por locatário.   

- Um aplicativo de único locatário tem apenas uma entidade de serviço, no diretório onde ele é criado.  Essa entidade de serviço é criada e consente a ser usado durante o registro do aplicativo. 
- Um aplicativo multilocatário ou a API tem uma entidade de serviço criada em cada locatário em que um usuário de locatário consente para usar o aplicativo.  

As credenciais para entidades de serviço podem ser um certificado ou chave (gerados por meio do portal do Azure).  Uso de um certificado é adequado para automação, como certificados são considerados mais seguros do que o uso de chaves. 


> [!NOTE]    
> Quando você usar o AD FS com a pilha do Azure, somente o administrador pode criar entidades de serviço. Com o AD FS, as entidades de serviço requerem certificados e são criadas por meio do ponto de extremidade privilegiado (PEP). Para obter mais informações, consulte, [fornecem acesso de aplicativos ao Azure pilha](azure-stack-create-service-principals.md).

Para saber mais sobre as entidades de serviço para a pilha do Azure, consulte [criar entidades de serviço](azure-stack-create-service-principals.md).


### <a name="services"></a>Serviços
Serviços na pilha do Azure que interagem com o provedor de identidade, são registrados como aplicativos com o provedor de identidade. Como aplicativos, o registro permite que um serviço autenticar com o sistema de identidade. 

Todos os serviços do Azure usam [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolos e [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) para estabelecer sua identidade. Devido ao uso consistente de protocolos pelo AD do Azure e do AD FS, você pode usar o Azure [biblioteca de autenticação do Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para autenticar no local ou no Azure (em um cenário conectado). ADAL também permite que você use ferramentas como o Azure PowerShell e a CLI para nuvem e gerenciamento de recursos locais. 


### <a name="identities-and-your-identity-system"></a>Identidades e seu sistema de identidade 
Identidades para a pilha do Azure incluem contas de usuário, grupos e entidades de serviço. Quando você instala a pilha do Azure, vários serviços e aplicativos internos se registrar automaticamente ao seu provedor de identidade no locatário de diretório. Alguns serviços registrados são usados para administração. Outros serviços estão disponíveis para os usuários. Os registros padrão dê identidades de serviços principais que podem interagir entre si e com identidades adicionado posteriormente.
Se você configurar o AD do Azure com multilocação, alguns aplicativos são propagadas para os diretórios de novo.  

## <a name="authentication-and-authorization"></a>Autenticação e autorização
 

### <a name="authentication-by-applications-and-users"></a>Autenticação por aplicativos e usuários
  
![Identidade entre camadas da pilha do Azure](media/azure-stack-identity-overview/identity-layers.png)

Para usuários e aplicativos, a arquitetura da pilha do Azure é descrita por quatro camadas. Interações entre cada uma dessas camadas podem usar diferentes tipos de autenticação.


|Camada    |Autenticação entre camadas  |
|---------|---------|
|Ferramentas e clientes, como o portal de administração     | Para acessar ou modificar um recurso na pilha do Azure, ferramentas e os clientes usam um [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) para fazer uma chamada para o gerenciamento de recursos do Azure.  <br><br> Gerenciador de recursos do Azure valida o JWT e exibe o *declarações* no token emitido para estimar o nível de autorização de usuário ou entidade de serviço tem na pilha do Azure.        |
|Gerenciador de recursos do Azure e seus serviços de núcleo     |Gerenciador de recursos do Azure se comunica com os provedores de recursos para transferir a comunicação de usuários. <br><br> Transfere use *imperativo direto* chamadas ou *declarativa* chama via [modelos do Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Provedores de recursos     |Chamadas passadas para os provedores de recursos são protegidas com autenticação baseada em certificado. <br><br>Gerenciador de recursos do Azure e o provedor de recursos permanecem na comunicação com a API. Para cada chamada recebida do Gerenciador de recursos do Azure, o provedor de recursos valida a chamada com esse certificado.|
|Infraestrutura e lógica de negócios     |Provedores de recursos se comunicam com lógica de negócios e infraestrutura usando um modo de autenticação de sua escolha. Os provedores de recursos de padrão que vêm com a pilha do Azure use autenticação do Windows para proteger essa comunicação.|

![Informações necessárias para autenticação](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Autenticar ao Gerenciador de recursos do Azure
Para autenticar com o provedor de identidade e receber um JWT, você deve ter as seguintes informações: 
1.  **URL para o sistema de identidade (autoridade)** – a URL na qual o seu provedor de identidade pode ser acessado. Por exemplo,  *&lt;https://login.windows.net>*. 
2.  **URI de ID de aplicativo do Azure Resource Manager** – o identificador exclusivo para o Azure Resource Manager que está registrado com seu provedor de identidade e exclusiva para cada instalação de pilha do Azure.
3.  **Credenciais** – essa credencial usada para autenticar com o provedor de identidade.  
4.  **URL do Gerenciador de recursos do Azure** – a URL é o local do serviço Gerenciador de recursos do Azure. Por exemplo,  *&lt;https://management.azure.com>* ou  *&lt;https://management.local.azurestack.external>*.

Quando uma entidade de segurança (um cliente, aplicativo ou usuário) faz uma solicitação de autenticação para acessar um recurso, se a solicitação deve incluir:
- Credenciais do servidor principal.
- O URI de ID de aplicativo do recurso que desejam acessar.  

As credenciais são validadas pelo provedor de identidade. O provedor de identidade também valida que o URI de ID de aplicativo é para um aplicativo registrado e a entidade tem os privilégios corretos para obter um token para esse recurso. Se a solicitação for válida, um JSON Web Token é concedido. 

O token deve passar no cabeçalho de uma solicitação para o Gerenciador de recursos do Azure. O Gerenciador de recursos do Azure faz as validações a seguir em nenhuma ordem específica:
- Validar o *emissor* (iss) de declaração confirmar que o token do provedor de identidade correto. 
- Validar o *público* declaração (aud) para confirmar se o token foi emitido para o Gerenciador de recursos do Azure. 
- Valide que o JWT é assinado com um certificado que é configurado por meio de OpenID e que é conhecido para o Gerenciador de recursos do Azure. 
- Examine o *emitido em* (iat) e *expiração* (exp) declarações para confirmar que o token está ativo e pode ser aceito. 

Quando todas as validações forem concluídas, o Gerenciador de recursos do Azure usa o *um objeto* (oid) e o *grupos* declarações para fazer uma lista de recursos que pode acessar a entidade de segurança. 

![Diagrama do protocolo exchange token](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Usar o controle de acesso baseado em função  
Controle de acesso baseado em função (RBAC) na pilha do Azure é consistente com a implementação no Microsoft Azure.  Você pode gerenciar o acesso aos recursos por meio da atribuição de função apropriada de RBAC aos usuários, grupos e aplicativos. Para obter informações sobre como usar o RBAC com pilha do Azure, revise os seguintes artigos:
- [Introdução ao controle de acesso baseado em função no portal do Azure](/azure/active-directory/role-based-access-control-what-is).
- [Use o controle de acesso baseado em função para gerenciar o acesso aos recursos da sua assinatura do Azure](/azure/active-directory/role-based-access-control-configure).
- [Criar funções personalizadas para o controle de acesso](/azure/active-directory/role-based-access-control-custom-roles).
- [Gerenciar o controle de acesso baseado em função](azure-stack-manage-permissions.md) na pilha do Azure.


### <a name="authenticate-with-azure-powershell"></a>Autenticar com o PowerShell do Azure  
Detalhes sobre como usar o PowerShell do Azure para autenticar com a pilha do Azure podem ser encontrados em [configurar o ambiente do PowerShell do usuário do Azure pilha](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autenticar com CLI do Azure
Detalhes sobre como usar o PowerShell do Azure para autenticar com a pilha do Azure podem ser encontrados em [instalar e configurar o CLI para uso com o Azure pilha](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de identidade](azure-stack-identity-architecture.md)   
- [Integração do data center - identidade](azure-stack-integrate-identity.md)




