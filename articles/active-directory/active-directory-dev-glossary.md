<properties
   pageTitle="Glossário do Desenvolvedor do Azure Active Directory | Microsoft Azure"
   description="Uma lista de termos referentes a conceitos e recursos de desenvolvedor do Azure Active Directory usados com frequência."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/31/2016"
   ms.author="bryanla"/>

# Glossário de desenvolvedor do Azure Active Directory
Este artigo contém as definições de alguns dos conceitos básicos para desenvolvedores do Azure Active Directory (AD), que são úteis ao se aprender sobre desenvolvimento de aplicativos para o Azure AD.

## o token de acesso 
Um tipo de [token de segurança](#security-token) emitido por um [servidor de autorização](#authorization-server) e usado por um [aplicativo cliente](#client-application) para acessar um [servidor de recursos protegido](#resource-server). Normalmente na forma de um [JWT (Token Web JSON)][JWT], o token consiste na autorização concedida ao cliente pelo [proprietário do recurso](#resource-owner) para um nível de acesso solicitado. O token contém todas as [declarações](#claim) aplicáveis sobre a entidade, habilitando o aplicativo cliente a usá-lo como uma forma de credenciais ao acessar um recurso específico. Assim, o proprietário do recurso não precisa expor suas credenciais para o cliente.

Os tokens de acesso às vezes são chamados de "Usuário+Aplicativo" ou "Somente Aplicativo", dependendo das credenciais que estão sendo representadas. Por exemplo, quando um aplicativo cliente usa:

- [Concessão de autorização de "código de autorização"](#authorization-grant), o usuário final é autenticado primeiro como o proprietário do recurso, delegando a autorização ao cliente para acessar o recurso. O cliente é autenticado depois, ao obter o token de acesso. Às vezes, o token pode ser chamado mais especificamente de token de "Usuário+Aplicativo", que representa o usuário que autorizou o aplicativo cliente e o aplicativo.
- [Concessão de autorização de "credenciais de cliente"](#authorization-grant), o cliente fornece a única autenticação, funcionando sem a autenticação/autorização do proprietário do recurso. Portanto, às vezes o token pode ser chamado de token "Somente de Aplicativo".

Confira [Referência de Token do Azure AD][AAD-Tokens-Claims] para obter mais detalhes.

## manifesto do aplicativo  
Um recurso fornecido pelo [portal clássico do Azure][AZURE-classic-portal], que produz uma representação JSON da configuração de identidade do aplicativo, usada como um mecanismo para atualizar suas entidades de [Aplicativo][AAD-Graph-App-Entity] e [ServicePrincipal][AAD-Graph-Sp-Entity] associadas. Confira [Noções básicas sobre o manifesto do aplicativo do Azure Active Directory][AAD-App-Manifest] para obter mais detalhes.

## objeto de aplicativo  
Quando você registra/atualiza um aplicativo no [portal clássico do Azure][AZURE-classic-portal], o portal cria/atualiza um objeto de aplicativo e um [objeto de entidade de serviço](#service-principal-object) correspondente para esse locatário. O objeto de aplicativo *define* a configuração de identidade do aplicativo globalmente (em todos os locatários aos quais ele tem acesso) e é o modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

Confira [Objetos de aplicativo e entidade de serviço][AAD-App-SP-Objects] para obter mais informações.

## registro de aplicativo  
Para permitir que um aplicativo se integre e delegue funções de Gerenciamento de Acesso e Identidade ao Azure AD, ele deve ser registrado em um [locatário](#tenant) do Azure AD. Ao registrar seu aplicativo no Azure AD, você fornece uma configuração de identidade para o aplicativo, permitindo que ele se integre ao Azure AD e use recursos como:

- Gerenciamento robusto de Logon Único usando o Gerenciamento de Identidade do Azure AD e a implementação de protocolo [OpenID Connect][OpenIDConnect]
- Acesso agenciado a [recursos protegidos](#resource-server) por [aplicativos cliente](#client-application), por meio da implementação de [servidor de autorização](#authorization-server) OAuth 2.0 do Azure AD
- [Estrutura de consentimento](#consent) para gerenciar o acesso do cliente a recursos protegidos, com base na autorização do proprietário do recurso.

Confira [Integrar aplicativos ao Azure Active Directory][AAD-Integrating-Apps] para obter mais detalhes.

## autenticação
O ato de desafiar uma parte para o fornecimento de credenciais legítimas, fornecendo a base para a criação de uma entidade de segurança a ser usada para controle de identidade e acesso. Durante uma [concessão de autorização OAuth2](#authorization-grant), por exemplo, a parte que está realizando a autenticação está desempenhando a função de [proprietário do recurso](#resource-owner) ou [aplicativo cliente](#client-application), dependendo da concessão usada.

## authorization
O ato de conceder a uma entidade de segurança autenticada permissão para fazer algo. Há dois casos de uso principais no modelo de programação do Azure AD:

- Durante um fluxo de [concessão de autorização OAuth2](#authorization-grant): quando o [proprietário do recurso](#resource-owner) concede autorização para o [aplicativo cliente](#client-application), permitindo que o cliente acesse recursos do proprietário do recurso.
- Durante o acesso a recursos pelo cliente: conforme implementado pelo [servidor de recursos](#resource-server), usando os valores de [declaração](#claim) presentes no [token de acesso](#access-token) para tomar decisões de controle de acesso com base neles.

## código de autorização
Um "token" de curta duração fornecido a um [aplicativo cliente](#client-application) pelo [ponto de extremidade de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma das quatro [concessões de autorização](#authorization-grant) OAuth2. O código é retornado para o aplicativo cliente em resposta à autenticação de um [proprietário do recurso](#resource-owner), indicando que o proprietário do recurso delegou autorização ao aplicativo cliente para acessar recursos em seu nome. Como parte do fluxo, o código é resgatado posteriormente para um [token de acesso](#access-token).

## ponto de extremidade de autorização
Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server), usado para interagir com o [proprietário do recurso](#resource-owner) para fornecer uma [concessão de autorização](#authorization-grant) durante um fluxo de concessão de autorização OAuth2. Dependendo do fluxo de concessão de autorização usado, a concessão real fornecida pode variar, incluindo um [código de autorização](#authorization-code) ou um [token de segurança](#security-token).

Confira as seções sobre [tipos de concessão de autorização][OAuth2-AuthZ-Grant-Types] e [ponto de extremidade de autorização][OAuth2-AuthZ-Endpoint] da especificação OAuth2 e a [especificação OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## concessão de autorização
Uma credencial que representa a [autorização](#resource-owner) [do proprietário do recurso](#authorization) para acessar seus recursos protegidos, concedida a um [aplicativo cliente](#client-application). Um aplicativo cliente pode usar um dos [quatro tipos de concessão definidos pela Estrutura de Autorização OAuth2][OAuth2-AuthZ-Grant-Types] para obter uma concessão, dependendo do tipo/requisitos do cliente: "concessão de código de autorização", "concessão de credenciais de cliente", "concessão implícita" e "concessão de credenciais de senha do proprietário do recurso". A credencial retornada ao cliente é um [token de acesso](#access-token) ou um [código de autorização](#authorization-code) (que é posteriormente trocado por um token de acesso), dependendo do tipo de fluxo de concessão de autorização usado.

## Servidor de autorização
Conforme definido pela [Estrutura de Autorização OAuth2][OAuth2-Role-Def], o servidor responsável pela emissão de tokens de acesso ao [cliente](#client-application) depois de autenticar com êxito o [proprietário do recurso](#resource-owner) e obter sua autorização. Um [aplicativo cliente](#client-application) interage com o servidor de autorização em tempo de execução por meio de seus pontos de extremidade de [autorização](#authorization-endpoint) e [token](#token-endpoint), de acordo com as [concessões de autorização](#authorization-grant) definidas pelo OAuth2.

No caso de integração de aplicativos do Azure AD, o Azure AD implementa a função de servidor de autorização para aplicativos do Azure AD e APIs de serviços da Microsoft, por exemplo, [APIs do Microsoft Graph][Microsoft-Graph].

## declaração
Um [token de segurança](#security-token) contêm declarações, que fornecem asserções sobre uma entidade (como um [aplicativo cliente](#client-application) ou um [proprietário de recursos](#resource-owner)) para outra entidade (como o [servidor de recursos](#resource-server)). As declarações são pares de nome/valor que transmitem fatos sobre a entidade do token (por exemplo, a entidade de segurança que foi autenticada pelo [servidor de autorização](#authorization-server)). As declarações presentes em um token específico dependem de diversas variáveis, incluindo o tipo de token, o tipo de credencial usado para autenticar a entidade, a configuração de aplicativo etc.

Confira [Referência de Token do Azure AD][AAD-Tokens-Claims] para obter mais detalhes.

## aplicativo cliente  
Conforme definido pelo [Estrutura de Autorização OAuth2][OAuth2-Role-Def], um aplicativo que faz solicitações de recursos protegidas em nome do [proprietário do recurso](#resource-owner). O termo "cliente" não implica características de implementação de hardware específicas (por exemplo, se o aplicativo é executado em um servidor, na área de trabalho ou em outros dispositivos).

Um aplicativo cliente solicita [autorização](#authorization) de um proprietário de recurso para participar de um fluxo de [concessão de autorização OAuth2](#authorization-grant) e pode acessar APIs/dados em nome do proprietário do recurso. A estrutura de autorização OAuth2 [define dois tipos de clientes][OAuth2-Client-Types], "confidencial" e "público", com base na capacidade do cliente de manter a confidencialidade de suas credenciais. Os aplicativos podem implementar um [cliente Web (confidencial)](#web-client) que é executado em um servidor Web, um aplicativo [cliente nativo (público)](#native-client) instalado em um dispositivo e um [cliente baseado em agente de usuário (público)](#user-agent-based-client) que é executado no navegador do dispositivo.

## consentimento
O processo para que um [proprietário do recurso](#resource-owner) conceda autorização a um [aplicativo cliente](#client-application) para [permissões](#permissions) específicas para acessar recursos protegidos em nome do proprietário do recurso. Dependendo das permissões solicitadas pelo cliente, um administrador ou usuário será solicitado a consentir o acesso aos dados de sua empresa ou dados individuais, respectivamente. Observe que, em um cenário de [multilocatário](#multi-tenant-application), a [entidade de serviço](#service-principal-object) do aplicativo também é registrada no locatário do usuário isso que fornece o consentimento.

## token de ID
Um [token de segurança][OpenIDConnect-ID-Token] [OpenID Connect](#security-token) fornecido por um [ponto de extremidade de autorização](#authorization-server) [do servidor de autorização](#authorization-endpoint), que contém [declarações](#claim) referentes à autenticação de um [proprietário de recurso](#resource-owner) de usuário final. Assim como um token de acesso, os tokens de ID também são representados como um [JWT (Token Web JSON)][JWT] assinado digitalmente. Diferentemente de um token de acesso, as declarações de um token de ID não são usadas para fins relacionados ao acesso a recursos e ao controle de acesso especificamente.

Confira [Referência de Token do Azure AD][AAD-Tokens-Claims] para obter mais detalhes.

## Aplicativos multilocatários
Uma classe de [aplicativo cliente](#client-application) registrada no Azure AD, projetada para permitir entrada e [consentimento](#consent) de contas de usuário que são provisionadas em qualquer [locatário](#tenant) do Azure AD, incluindo locatários diferentes daquele em que o cliente foi registrado originalmente. Por outro lado, um aplicativo registrado como locatário único só permitiria entradas de contas de usuário provisionadas no mesmo locatário que aquele em que o aplicativo está registrado. Aplicativos de [cliente nativo](#native-client) são multilocatários por padrão, enquanto aplicativos de [cliente Web](#web-client) têm a capacidade de selecionar entre único e multilocatário.

Confira [Como entrar em qualquer usuário do Azure AD usando o padrão de aplicativo multilocatário][AAD-Multi-Tenant-Overview] para obter mais detalhes.

## cliente nativo
Um tipo de [aplicativo cliente](#client-application) que é instalado de forma nativa em um dispositivo. Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma particular/confidencial. Confira [Perfis e tipos de cliente OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## permissões
Um [aplicativo cliente](#client-application) obtém acesso a um [servidor de recursos](#resource-server) declarando solicitações de permissão. Dois tipos estão disponíveis:

- Permissões "delegadas", que solicitam acesso [com base no escopo](#scopes) na autorização delegada do [proprietário de recurso](#resource-owner) conectado e são manifestadas em tempo de execução como [declarações "scp"](#claim) no [token de acesso](#access-token) do cliente.
- Permissões de "aplicativo", que solicitam acesso [baseado em função](#roles) com as credenciais/identidade do aplicativo cliente e são manifestadas em tempo de execução como [declarações de "funções"](#claim) no token de acesso do cliente.

Também surgem durante o processo de [consentimento](#consent), oferecendo ao administrador ou ao proprietário do recurso a oportunidade de conceder/negar ao cliente o acesso aos recursos em seu locatário.

As solicitações de permissão são configuradas na guia "Aplicativos"/"Configurar" do [portal clássico do Azure][AZURE-classic-portal], em "Permissões para outros aplicativos", selecionando as "Permissões Delegadas" e as "Permissões de Aplicativo" desejadas (a segunda opção requer associação na função de Administrador Global). Como um [cliente público](#client-application) não pode manter as credenciais, só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar permissões delegadas e de aplicativo. O [objeto de aplicativo](#application-object) do cliente armazena as permissões declaradas em sua [propriedade requiredResourceAccess][AAD-Graph-App-Entity].

## proprietário do recurso
Conforme definido pela [Estrutura de Autorização OAuth2][OAuth2-Role-Def], uma entidade com a capacidade de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, é chamado de usuário final. Por exemplo, quando um [aplicativo cliente](#client-application) quer acessar a caixa de correio do usuário por meio da [API do Microsoft Graph][Microsoft-Graph], requer a permissão do proprietário do recurso da caixa de correio.

## servidor do recurso
Conforme definido pela [Estrutura de Autorização OAuth2][OAuth2-Role-Def], um servidor que hospeda recursos protegidos, capaz de aceitar e responder a solicitações de recursos protegidos de [aplicativos cliente](#client-application) que apresentam um [token de acesso](#access-token). Também conhecido como um servidor de recursos protegidos ou aplicativo de recurso.

Um servidor de recursos expõe APIs e impõe o acesso a seus recursos protegidos por meio de [escopos](#scopes) e [funções](#roles), usando a Estrutura de Autorização OAuth 2.0. Alguns exemplos são a API do Graph do Azure AD, que fornece acesso aos dados de locatário do Azure AD, e APIs do Office 365, que fornecem acesso a dados como email, calendário e documentos. Ambas também são acessíveis por meio da [API do Microsoft Graph][Microsoft-Graph].

Assim como um aplicativo cliente, a configuração de identidade do aplicativo de recurso é estabelecida via [registro](#application-registration) em um locatário do Azure AD, fornecendo o objeto de entidade de serviço e de aplicativo. Algumas APIs fornecidas pela Microsoft, como a API do Graph do Azure AD, têm entidades de serviço previamente registradas disponíveis em todos os locatários durante o provisionamento.

## roles
Assim como os [escopos](#scopes), as funções fornecem uma maneira para que um [servidor de recursos](#resource-server) governe o acesso a seus recursos protegidos. Há dois tipos: uma função de "usuário" implementa o controle de acesso baseado em função para usuários/grupos que exigem acesso ao recurso, enquanto uma função de "aplicativo" implementa o mesmo para [aplicativos cliente](#client-application) que requerem acesso.

As funções são cadeias de caracteres definidas por recursos (por exemplo, "Aprovador de despesas" ou "Somente leitura"), gerenciadas no [portal clássico do Azure][AZURE-classic-portal] por meio do recurso [manifesto de aplicativo](#application-manifest) e armazenadas na [propriedade appRoles][AAD-Graph-Sp-Entity] do recurso. Um aplicativo cliente solicita [permissão](#permissions) para acessar uma função de "aplicativo", que é apresentada ao recurso em tempo de execução na [declaração de "funções"](#claim) do [token de acesso](#access-token) do cliente. Os administradores do Azure AD podem atribuir usuários a funções de "usuário" por meio do [portal clássico do Azure][AZURE-classic-portal].

Para obter uma discussão detalhada sobre as funções de aplicativo expostas pela API do Graph do Azure AD, confira [Escopos de permissão da API do Graph][AAD-Graph-Perm-Scopes]. Para obter um exemplo de implementação passo a passo, confira [Controle de acesso baseado em função em aplicativos de nuvem usando o Azure AD][Duyshant-Role-Blog].

## escopos
Assim como as [funções](#roles), os escopos fornecem uma maneira para que um [servidor de recursos](#resource-server) governe o acesso a seus recursos protegidos. Os escopos são usados para implementar o controle de acesso [baseado em escopo][OAuth2-Access-Token-Scopes], para um [aplicativo cliente](#client-application) que recebeu acesso delegado ao recurso de seu proprietário.

Os escopos são cadeias de caracteres definidas por recursos (por exemplo "Mail.Read" ou "Directory.ReadWrite.All") gerenciadas no [portal clássico do Azure][AZURE-classic-portal] por meio do recurso [manifesto do aplicativo](#application-manifest) e armazenados na [propriedade oauth2Permissions][AAD-Graph-Sp-Entity] do recurso. Um aplicativo cliente solicita [permissão](#permissions) para acesso, que é apresentada ao recurso no tempo de execução na [declaração "scp"](#claim) do [token de acesso](#access-token) do cliente.

Uma prática recomendada para a convenção de nomenclatura é usar um formato "resource.operation.constraint". Para obter uma discussão detalhada sobre os escopos expostos pela API do Graph do Azure AD, confira [Escopos de permissão da API do Graph][AAD-Graph-Perm-Scopes]. Para escopos expostos por serviços do Office 365, confira [Referência de permissões de API do Office 365][O365-Perm-Ref].

## token de segurança
Um documento assinado que contém declarações, como um token OAuth2 ou uma asserção SAML 2.0. No caso de uma [concessão de autorização](#authorization-grant) OAuth 2.0, um [token de acesso](#access-token) (OAuth2) e um [Token de ID] (OpenID Connect) são um tipo de token de segurança. Ambos são implementados como um [JWT (Token Web JSON)][JWT].

## objeto de entidade de serviço
Quando você registra/atualiza um aplicativo no [portal clássico do Azure][AZURE-classic-portal], o portal cria/atualiza um objeto de aplicativo e um [objeto de entidade de serviço](#application-object) correspondente para esse locatário. O objeto de aplicativo *define* a configuração de identidade do aplicativo globalmente (em todos os locatários em que o aplicativo associado recebeu acesso) e é o modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

Confira [Objetos de aplicativo e entidade de serviço][AAD-App-SP-Objects] para obter mais informações.

## entrada
O processo para que um [aplicativo cliente](#client-application) inicie a autenticação do usuário final e capture o estado relacionado após a autenticação, para adquirir um [token de segurança](#security-token) e obter o escopo da sessão do aplicativo para esse estado. O estado pode incluir artefatos, como informações de perfil de usuário, e informações derivadas de declarações de token.

A função de entrada de um aplicativo normalmente é usada para implementar o SSO (logon único) e pode ser precedida por uma função de "inscrição", que é o ponto de entrada para um usuário final obter acesso a um aplicativo (após a primeira entrada). A função de inscrição é usada para coletar e persistir o estado adicional específico do usuário e pode exigir o [consentimento do usuário](#consent).

## saída
O processo de cancelamento de autenticação de um usuário final, desanexando o estado do usuário associado à sessão do [aplicativo cliente](#client-application) durante a [entrada](#sign-in)

## locatário
Uma instância de um diretório do Azure AD é chamada de locatário do Azure AD. Fornece diversos recursos, incluindo um serviço de registro para aplicativos integrados, autenticação de contas de usuário e aplicativos registrados e os pontos de extremidade REST necessários para dar suporte a vários protocolos, incluindo OAuth2 e SAML. Os pontos de extremidade incluem o [ponto de extremidade de autorização](#authorization-endpoint), o [ponto de extremidade de token](#token-endpoint) e o ponto de extremidade "comum" usado por [aplicativos multilocatário](#multi-tenant-application).

Confira [Como obter um locatário do Azure Active Directory][AAD-How-To-Tenant] para obter detalhes sobre as várias maneiras de obter acesso a um locatário.

## ponto de extremidade de token
Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server) para dar suporte a [concessões de autorização](#authorization-grant) OAuth2. Dependendo da concessão, pode ser usado para fornecer um [token de acesso](#access-token) (e, possivelmente, um token de "atualização") a um [cliente](#client-application), bem como um [token de ID](#ID-token) quando a concessão é usada em conjunto com o protocolo [OpenID Connect][OpenIDConnect].

## Cliente com base em agente de usuário
Um tipo de [aplicativo cliente](#client-application) que baixa código de um servidor Web e é executado em um agente de usuário (por exemplo, um navegador da Web), como um SPA (Aplicativo de Página Única). Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma particular/confidencial. Confira [Perfis e tipos de cliente OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## entidade de usuário
Da mesma forma como um objeto de entidade de serviço é usado para representar uma instância de aplicativo, um objeto de entidade de usuário é outro tipo de entidade de segurança, que representa um usuário. A [Entidade de usuário][AAD-Graph-User-Entity] do Graph do Azure AD define o esquema para um objeto de entidade de usuário. A Entidade de usuário consiste em propriedades relacionadas ao usuário, como nome e sobrenome, nome de entidade de usuário, associação em funções de diretório etc., fornecendo a configuração de identidade de usuário necessária para que o Azure AD estabeleça uma entidade de usuário em tempo de execução. A entidade de usuário é usada para representar um usuário autenticado ao gravar a delegação de [consentimento](#consent), tomar decisões de controle de acesso etc.

## cliente Web
Um tipo de [aplicativo cliente](#client-application) que executa todo o código em um servidor Web e, assim, é considerado um cliente "confidencial", devido à sua capacidade de armazenar credenciais de forma particular/confidencial no servidor. Confira [Perfis e tipos de cliente OAuth2][OAuth2-Client-Types] para obter mais detalhes.

## Próximas etapas
O [Guia do Desenvolvedor do Azure AD][AAD-Dev-Guide] é o portal a ser usado para todos os tópicos sobre desenvolvimento do Azure AD, incluindo uma visão geral da [integração de aplicativos][AAD-How-To-Integrate] e as noções básicas de [autenticação do Azure AD e cenários de autenticação com suporte][AAD-Auth-Scenarios].

Use a seção de comentários do Disqus abaixo para fornecer seus comentários e ajudar a refinar e a moldar o nosso conteúdo.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/pt-BR/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

<!---HONumber=AcomDC_0803_2016-->