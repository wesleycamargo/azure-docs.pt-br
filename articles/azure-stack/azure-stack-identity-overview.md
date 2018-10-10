---
title: Visão geral da identidade para o Azure Stack | Microsoft Docs
description: Saiba mais sobre os sistemas de identidade que você pode usar com o Azure Stack.
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
ms.date: 10/09/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 1bc4fcda360a899fb2f58e2ac26270d160227a65
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902834"
---
# <a name="overview-of-identity-for-azure-stack"></a>Visão geral da identidade para o Azure Stack

O Azure Stack requer o Azure Active Directory (Azure AD) ou o Active Directory Federation Services (AD FS), com o respaldo do Active Directory como um provedor de identidade. A escolha de um provedor é uma decisão ocasional que fazer quando você implantar o Azure Stack. Os conceitos e detalhes de autorização neste artigo podem ajudá-lo a escolher entre provedores de identidade.

A escolha do Azure AD ou AD FS pode ser determinada pelo modo em que você implanta o Azure Stack:

- Quando você implantá-lo em um modo conectado, você pode usar o Azure AD ou AD FS.
- Quando você implantá-lo em um modo desconectado, sem uma conexão à internet, somente o AD FS tem suporte.

Para obter mais informações sobre suas opções, que dependem de seu ambiente do Azure Stack, consulte os seguintes artigos:

- O kit de implantação do Azure Stack: [considerações sobre identidade](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemas integrados do Azure Stack: [sistemas integrados de decisões para o Azure Stack de planejamento de implantação](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Conceitos comuns de identidade

As seções a seguir abordam conceitos comuns sobre provedores de identidade e seu uso no Azure Stack.

![Terminologia para provedores de identidade](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>As organizações e locatários de diretório

Um diretório é um contêiner que mantém informações sobre *os usuários*, *aplicativos*, *grupos*, e *entidades de serviço*.

Um locatário de diretório é um *organização*, como Microsoft ou sua própria empresa.

- Azure AD dá suporte a vários locatários, e ele pode dar suporte a várias organizações, cada um em seu próprio diretório. Se você usa o Azure AD e tiver vários locatários, você pode conceder a aplicativos e usuários do acesso de um locatário para outros locatários do mesmo diretório.
- O AD FS dá suporte a apenas um único locatário e, portanto, apenas uma única organização.

### <a name="users-and-groups"></a>Usuários e grupos

Contas de usuário (identidades) são contas padrão que se autenticam indivíduos usando uma ID de usuário e senha. Grupos podem incluir outros grupos ou usuários.

Como você pode cria e gerenciar usuários e grupos depende de você usar a solução de identidade.

No Azure Stack, contas de usuário:

- São criados na *username@domain* formato. Embora o AD FS mapeia as contas de usuário a uma instância do Active Directory, AD FS não suporta o uso do  *\\ \<domínio >\\\<alias >* formato.
- Pode ser configurado para usar a autenticação multifator.
- São restritos para o diretório em que eles primeiro registram, que é o diretório da organização.
- Podem ser importados de seus diretórios locais. Para obter mais informações, consulte [integrar seus diretórios locais ao Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Quando você entrar no portal de locatário da sua organização, você usar o *https://portal.local.azurestack.external* URL. Ao entrar no portal do Azure Stack de domínios diferente daquela usada para registrar o Azure Stack, o nome de domínio usado para registrar o Azure Stack deve ser acrescentado ao portal do url. Por exemplo, se o Azure Stack foi registrado com fabrikam.onmicrosoft.com e a conta de usuário, registro em log no está admin@contoso.com, a url a ser usada para fazer logon no portal do usuário seria: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Usuários convidados

Usuários convidados são contas de usuário de outros locatários de diretório que receberam acesso a recursos em seu diretório. Para dar suporte a usuários convidados, você pode usa o Azure AD e habilitar o suporte para multilocação. Quando o suporte está habilitado, você pode convidar usuários para acessar os recursos em seu locatário de diretório, que por sua vez, permite que sua colaboração com organizações externas.

Para convidar usuários convidados, os operadores de nuvem e os usuários podem usar [colaboração B2B do Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Os usuários convidados obtém acesso aos documentos, recursos e aplicativos de seu diretório e manter o controle sobre seus próprios recursos e dados. 

Como um usuário convidado, você pode entrar no locatário do diretório da outra organização. Para fazer isso, você acrescentar o nome de diretório da organização ao portal do URL. Por exemplo, se você pertence à organização Contoso e deseja entrar no diretório da Fabrikam, usar https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APLICATIVOS

Você pode registrar aplicativos para o Azure AD ou AD FS e, em seguida, oferecer os aplicativos para usuários em sua organização.

Os aplicativos incluem:

- **Aplicativo Web**: os exemplos incluem o portal do Azure e o Azure Resource Manager. Eles oferecem suporte a chamadas de API da Web.
- **Cliente nativo**: os exemplos incluem o Azure PowerShell, o Visual Studio e a CLI do Azure.

Aplicativos podem oferecer suporte a dois tipos de locação:

- **Locatário único**: dá suporte a usuários e serviços somente do mesmo diretório em que o aplicativo está registrado.

  > [!NOTE]
  > Como o AD FS oferece suporte a apenas um único diretório, aplicativos que você criou em uma topologia do AD FS são, por design, aplicativos de locatário único.

- **Multilocatário**: oferece suporte ao uso por usuários e serviços de diretório em que o aplicativo está registrado e diretórios adicionais de locatário. Com aplicativos de multilocatário, os usuários de outro pode de diretório (outro locatário do Azure AD) do locatário entrar no seu aplicativo. 

  Para obter mais informações sobre multilocação, consulte [habilitar multilocação](azure-stack-enable-multitenancy.md).

  Para obter mais informações sobre como desenvolver um aplicativo multilocatário, consulte [aplicativos de multilocatário](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando você registra um aplicativo, você pode criar dois objetos:

- **Objeto de aplicativo**: A representação global do aplicativo em todos os locatários. Essa relação é-para-um com o aplicativo de software e existe somente no diretório onde o aplicativo pela primeira vez é registrado.

- **Objeto de entidade de serviço**: uma credencial que é criada para um aplicativo no diretório onde o aplicativo pela primeira vez é registrado. Uma entidade de serviço também é criada no diretório de cada locatário adicional em que esse aplicativo é usado. Essa relação pode ser um-para-muitos com o aplicativo de software.

Para saber mais sobre o aplicativo e objetos de entidade de serviço, consulte [aplicativo e objetos de entidade de serviço no Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Entidades de serviço

Uma entidade de serviço é um conjunto de *credenciais* para um aplicativo ou serviço que concedem acesso a recursos no Azure Stack. O uso de uma entidade de serviço separa as permissões do aplicativo das permissões do usuário do aplicativo.

Uma entidade de serviço é criada em cada locatário em que o aplicativo é usado. A entidade de serviço estabelece uma identidade para entrada e acesso aos recursos (como usuários) que são protegidos pelo locatário.

- Um aplicativo de locatário único tem apenas uma entidade de serviço, no diretório em que ele é criado. Essa entidade de serviço é criada e dá consentimento a que está sendo usado durante o registro do aplicativo.
- Um aplicativo web multilocatário ou a API tem uma entidade de serviço é criada em cada locatário em que um usuário de locatário dá consentimento para o uso do aplicativo.

As credenciais para as entidades de serviço podem ser um de uma chave que é gerada por meio do portal do Azure ou um certificado. O uso de um certificado é adequado para a automação porque os certificados são considerados mais seguros do que as chaves. 

> [!NOTE]
> Quando você usa o AD FS com o Azure Stack, somente o administrador pode criar entidades de serviço. Com o AD FS, as entidades de serviço exigem certificados e são criadas por meio do ponto de extremidade com privilégios (PEP). Para obter mais informações, consulte [fornecer acesso a aplicativos para o Azure Stack](azure-stack-create-service-principals.md).

Para saber mais sobre entidades de serviço para o Azure Stack, consulte [criar entidades de serviço](azure-stack-create-service-principals.md).

### <a name="services"></a>Serviços

Serviços no Azure Stack que interagem com o provedor de identidade são registrados como aplicativos com o provedor de identidade. Assim como aplicativos, o registro permite que um serviço para autenticar com o sistema de identidade.

Todos os serviços do Azure usam [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolos e [Tokens Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) para estabelecer sua identidade. Porque o Azure AD e o AD FS usam protocolos de forma consistente, você pode usar [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para autenticar no local ou no Azure (em um cenário conectado). Com a ADAL, você também pode usar ferramentas como o Azure PowerShell e CLI do Azure para gerenciamento de recursos de nuvem e locais.

### <a name="identities-and-your-identity-system"></a>As identidades e o sistema de identidade

Identidades para o Azure Stack incluem as contas de usuário, grupos e entidades de serviço.

Quando você instala o Azure Stack, vários aplicativos internos e serviços registrados automaticamente no seu provedor de identidade no locatário de diretório. Alguns serviços registrados são usados para administração. Outros serviços estão disponíveis para os usuários. Os registros padrão oferecem identidades de serviços principais que podem interagir entre si e com as identidades que você adicionar mais tarde.

Se configurar o Azure AD com a multilocação, alguns aplicativos serão propagadas para os diretórios de novo.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

### <a name="authentication-by-applications-and-users"></a>Autenticação por aplicativos e usuários

![Identidade entre camadas da pilha do Azure](media/azure-stack-identity-overview/identity-layers.png)

Para usuários e aplicativos, a arquitetura do Azure Stack é descrita por quatro camadas. Interações entre cada uma dessas camadas podem usar diferentes tipos de autenticação.

|Camada    |Autenticação entre camadas  |
|---------|---------|
|Ferramentas e clientes, como o portal de administração     | Para acessar ou modificar um recurso no Azure Stack, ferramentas e os clientes usam um [Token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) para fazer uma chamada para o Azure Resource Manager. <br>O Azure Resource Manager valida o JSON Web Token e espia a *declarações* no token emitido para estimar o nível de autorização esse usuário ou entidade de serviço tem no Azure Stack. |
|O Azure Resource Manager e seus serviços de núcleo     |O Azure Resource Manager se comunica com os provedores de recursos para transferir a comunicação dos usuários. <br> Transferências de uso *imperativo direto* chamadas ou *declarativa* chama via [modelos do Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates).|
|Provedores de recursos     |Chamadas que são passadas para provedores de recursos são protegidas com a autenticação baseada em certificado. <br>O Azure Resource Manager e o provedor de recursos, em seguida, permanecem na comunicação por meio de uma API. Para cada chamada que é recebida do Azure Resource Manager, o provedor de recursos valida a chamada com esse certificado.|
|Lógica de negócios e infraestrutura     |Provedores de recursos se comunicar com a lógica de negócios e a infraestrutura usando um modo de autenticação de sua preferência. Os provedores de recursos padrão que vêm com o Azure Stack usam a autenticação do Windows para proteger essa comunicação.|

![Informações necessárias para autenticação](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Autenticar para o Azure Resource Manager

Para autenticar com o provedor de identidade e receber um Token Web JSON, você deve ter as seguintes informações:

1. **URL para o sistema de identidade (autoridade de certificação)**: A URL na qual o seu provedor de identidade pode ser acessado. Por exemplo, *https://login.windows.net*.
2. **URI da ID do aplicativo do Azure Resource Manager**: O identificador exclusivo para o Azure Resource Manager que está registrado com seu provedor de identidade. Também é exclusiva para cada instalação do Azure Stack.
3. **Credenciais**: A credencial que você pode usar para autenticar com o provedor de identidade.
4. **URL para o Azure Resource Manager**: A URL é o local do serviço do Azure Resource Manager. Por exemplo, *https://management.azure.com* ou *https://management.local.azurestack.external*.

Quando uma entidade de segurança (um cliente, aplicativo ou usuário) faz uma solicitação de autenticação para acessar um recurso, a solicitação deve incluir:

- As credenciais da entidade.
- O URI da ID do recurso que deseja que a entidade de segurança para acessar o aplicativo.

As credenciais são validadas pelo provedor de identidade. O provedor de identidade também valida que o aplicativo do URI da ID é para um aplicativo registrado e se a entidade tem os privilégios corretos para obter um token para esse recurso. Se a solicitação for válida, recebe um Token Web JSON.

O token deve, em seguida, passar o cabeçalho de uma solicitação ao Azure Resource Manager. O Azure Resource Manager faz o seguinte, em nenhuma ordem específica:

- Valida o *emissor* declaração (iss) para confirmar que o token é o correta do provedor de identidade.
- Valida o *público* declaração (aud) para confirmar se o token foi emitido para o Azure Resource Manager.
- Valida o JSON Web Token é assinado com um certificado que é configurado por meio do OpenID é conhecido para o Azure Resource Manager.
- Examine os *emitido em* (iat) e *expiração* (exp) de declarações para confirmar que o token está ativo e pode ser aceito.

Quando todas as validações forem concluídas, o Gerenciador de recursos do Azure usa o *um objeto* (oid) e o *grupos* de declarações para fazer uma lista de recursos que a entidade de segurança pode acessar.

![Diagrama do protocolo de troca de token](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Após a implantação, a permissão de administrador global do Azure Active Directory não é necessária. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador do provedor de recursos ou um novo recurso que exigem uma permissão para ser concedida. Você temporariamente pode designar novamente as permissões da conta administrador global ou usar uma conta de administrador global separado que é proprietário do *padrão de assinatura do provedor*.

### <a name="use-role-based-access-control"></a>Usar o controle de acesso baseado em função

Controle de acesso baseado em função (RBAC) no Azure Stack é consistente com a implementação no Microsoft Azure. Você pode gerenciar o acesso aos recursos, atribuindo a função RBAC apropriada a usuários, grupos e aplicativos.
Para obter informações sobre como usar o RBAC com o Azure Stack, consulte os seguintes artigos:

- [Introdução ao Controle de Acesso Baseado em Função no Portal do Azure](/azure/role-based-access-control/overview).
- [Use o controle de acesso baseado em função para gerenciar o acesso aos recursos de sua assinatura do Azure](/azure/role-based-access-control/role-assignments-portal).
- [Criar funções personalizadas para o controle de acesso baseado em função](/azure/role-based-access-control/custom-roles).
- [Gerenciar controle de acesso baseado em função](azure-stack-manage-permissions.md) no Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Autenticar com o Azure PowerShell

Detalhes sobre como usar o PowerShell do Azure para autenticar com o Azure Stack podem ser encontrados em [configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autenticar com a CLI do Azure

Para obter informações sobre como usar o PowerShell do Azure para autenticar com o Azure Stack, consulte [instalar e configurar a CLI do Azure para uso com o Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Próximas etapas

- [Arquitetura de identidade](azure-stack-identity-architecture.md)
- [Integração do Datacenter - identidade](azure-stack-integrate-identity.md)
