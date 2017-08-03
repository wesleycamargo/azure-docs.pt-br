---
title: Integrando aplicativos ao Azure Active Directory | Microsoft Docs
description: Detalhes sobre como adicionar, atualizar ou remover um aplicativo no AD do Azure (Active Directory do Azure).
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: lenalepa
ms.custom: aaddev
ms.reviewer: luleon
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e8cc9b790224891a0770b18fe2edb8e1bbfd5b72
ms.contentlocale: pt-br
ms.lasthandoff: 03/31/2017

---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrando aplicativos com o Active Directory do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados ao AD do Azure (Active Directory do Azure) para fornecer conexão segura e autorização para os respectivos serviços. Para integrar um aplicativo ou serviço ao AD do Azure, um desenvolvedor deve, primeiramente, registrar os detalhes sobre o respectivo aplicativo no AD do Azure usando o portal clássico do Azure.

Este artigo mostra como adicionar, atualizar ou remover um aplicativo no AD do Azure. Você aprenderá sobre os diferentes tipos de aplicativo que podem ser integrados ao AD do Azure, como configurar aplicativos para acessar outros recursos, como APIs Web, e muito mais.

Para saber mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de entidade de serviço e aplicativo](active-directory-application-objects.md); para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Adicionando um aplicativo
Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao AD do Azure detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.

Se estiver criando um aplicativo Web que precise apenas oferecer suporte à entrada de usuários no Azure AD, você pode simplesmente seguir as instruções abaixo. Se seu aplicativo precisar de credenciais ou de permissões para acessar uma API Web ou se precisar permitir que os usuários de outros locatários do Azure AD a acessem, veja a seção [Atualizando um aplicativo](#updating-an-application) para continuar a configurar seu aplicativo.

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Para registrar um novo aplicativo no portal do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **Mais Serviços**, clique em **Registros de Aplicativo** e clique em **Adicionar**.
4. Siga os avisos e crie um novo aplicativo. Se você desejar exemplos específicos para aplicativos da Web ou aplicativos nativos, confira nossos [inícios rápidos](active-directory-developers-guide.md).
  * Para os Aplicativos Web, forneça a **URL de Entrada**, que é a URL base do seu aplicativo, onde os usuários podem entrar como `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Para os Aplicativos Nativos, forneça um **URI de redirecionamento**, que será usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo, `http://MyFirstAADApp`
5. Depois de concluir o registro, o Azure AD atribuirá a seu aplicativo um identificador de cliente único, a ID do Aplicativo. O aplicativo foi adicionado e você será levado para a página Início Rápido do seu aplicativo. Dependendo do tipo de aplicativo, Web ou nativo, você verá diferentes opções sobre como adicionar mais recursos ao aplicativo. Depois que o aplicativo tiver sido adicionado, você poderá começar a atualizá-lo para permitir que os usuários se conectem, acessem APIs Web em outros aplicativos, ou a configurar o aplicativo multilocatário (o que permite que outras organizações acessem o aplicativo).

> [!NOTE]
> Por padrão, o registro de aplicativo recentemente criado é configurado para permitir que os usuários do seu diretório entrem no aplicativo.
> 
> 

## <a name="updating-an-application"></a>Atualizando um aplicativo
Depois que o aplicativo tiver sido registrado no AD do Azure, talvez ele tenha que ser atualizado para fornecer acesso a APIs Web, disponibilizado para outras organizações e muito mais. Esta seção descreve várias maneiras em que talvez seja necessário configurar ainda mais seu aplicativo. Primeiro, vamos começar com uma visão geral da Estrutura de Consentimento, em que é importante entender se você está compilando aplicativos de API/recursos que serão consumidos por aplicativos cliente criados por desenvolvedores na sua organização ou em outra organização.

Para obter mais informações sobre como funciona a autenticação no AD do Azure, consulte [Cenários de autenticação do AD do Azure](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Visão geral da estrutura de consentimento
A estrutura de autorização do Azure AD facilita o desenvolvimento de aplicativos cliente nativos ou Web multilocatários que precisam acessar APIs Web protegidas por um locatário do Azure AD, diferentemente daquele em que o aplicativo cliente é registrado. Essas APIs da Web incluem a API do Microsoft Graph (para acessar o Azure Active Directory, o Intune e serviços no Office 365) e outras APIs de serviços da Microsoft, além de suas próprias APIs da Web. A estrutura se baseia em um usuário ou administrador que dá autorização a um aplicativo que solicita seu registro no diretório, o que pode envolver acesso aos dados do diretório.

Por exemplo, se um aplicativo cliente da Web precisar ler informações de calendário sobre o usuário do Office 365, esse usuário precisará dar consentimento ao aplicativo. Depois que o autorização for dado, o aplicativo cliente poderá chamar a API do Microsoft Graph em nome do usuário e usar as informações de calendário conforme a necessidade. A [API do Graph da Microsoft](https://graph.microsoft.io) fornece acesso aos dados do Office 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do OneDrive, blocos de anotações do OneNote, tarefas do Planner, pastas de trabalho do Excel etc.), bem como usuários e grupos do Azure AD e outros objetos de dados de outros serviços do Microsoft Cloud. 

A estrutura de consentimento foi criada com base no OAuth 2.0 e seus vários fluxos, como concessão de código de autorização e concessão de credenciais de cliente, usando clientes públicos ou confidenciais. Com o OAuth 2.0, o AD do Azure permite criar muitos tipos diferentes de aplicativo cliente, como em um telefone, tablet, servidor, ou um aplicativo Web, assim como obter acesso aos recursos necessários.

Para saber mais sobre a estrutura de consentimento, consulte [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md), e para obter informações sobre como obter acesso autorizado ao Office 365 por meio do Microsoft Graph, consulte [Autenticação de aplicativos com o Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Exemplo da experiência de consentimento
As etapas a seguir mostrarão como funciona a experiência de consentimento para o desenvolvedor e o usuário do aplicativo.

1. Na página de configuração do aplicativo cliente no portal do Azure, defina as permissões que o aplicativo exige usando os menus da seção Permissões Necessárias.
   
    ![Permissões para outros aplicativos](./media/active-directory-integrating-applications/requiredpermissions.png)
2. Pense na possibilidade de que as permissões do aplicativo tenham sido atualizadas, de que o aplicativo esteja em execução e de que um usuário esteja prestes a usá-lo pela primeira vez. Se o aplicativo ainda não tiver adquirido um token de atualização ou acesso, ele precisará ir até o ponto de extremidade de autorização do AD do Azure para obter um código de autorização que possa ser usado para adquirir um novo token de atualização e acesso.
3. Se o usuário ainda não estiver autenticado, será solicitado que ele entre no AD do Azure.
   
    ![Conexão do usuário ou administrador no AD do Azure](./media/active-directory-integrating-applications/usersignin.png)
4. Depois que o usuário tiver se conectado, o AD do Azure determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo. Se o consentimento ainda não tiver sido dado, o AD do Azure solicitará o consentimento ao usuário e exibirá as permissões necessárias que ele precisa para funcionar. O conjunto de permissões exibido na caixa de diálogo de autorização é igual ao que foi selecionado nas Permissões Delegadas no portal do Azure.
   
    ![Experiência de consentimento do usuário](./media/active-directory-integrating-applications/consent.png)
5. Depois que o usuário dá consentimento, um código de autorização é retornado ao aplicativo, que pode ser resgatado para aquisição de um token de acesso e de atualização. Para saber mais sobre esse fluxo, consulte a seção [Aplicativo Web para API Web](active-directory-authentication-scenarios.md#web-application-to-web-api) em [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).

6. Como administrador, você pode também concorda com permissões do aplicativo em nome de todos os usuários em seu locatário. Isso impedirá que a caixa de diálogo de consentimento que aparecem para cada usuário no locatário. Você pode fazer isso a partir de [portal do Azure](https://portal.azure.com) da sua página de aplicativo. Do **configurações** lâmina para seu aplicativo, clique em **permissões necessárias** e clique no **conceder permissões** botão. 

    ![Conceda permissões para consentimento explícito de admin](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> Concessão explícita consentimento usando o **conceder permissões** botão é necessário no momento para aplicativos de página única (SPA) usando o adal.js, como o token de acesso é solicitado sem um prompt de consentimento, falhará se o consentimento já não foi concedido.   

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configurando um aplicativo cliente para acessar APIs Web
Para que um aplicativo cliente Web/confidencial possa participar de um fluxo de concessão de autorização que requer autenticação (e possa obter um token de acesso), ele deverá estabelecer credenciais seguras. O método de autenticação padrão com suporte do portal do Azure é a ID do Cliente + chave simétrica. Esta seção abordará as etapas de configuração necessárias para fornecer a chave secreta das credenciais do seu cliente.

Além disso, antes que um cliente possa acessar uma API Web exposta por um aplicativo de recurso (isto é, API do Microsoft Graph), a estrutura de autorização garantirá que o cliente obtenha a concessão de permissão necessária, com base nas permissões solicitadas. Por padrão, todos os aplicativos podem escolher permissões no Active Directory do Azure (Graph API) e na API de Gerenciamento de Serviços do Azure, com a permissão "Permitir logon e leitura do perfil do usuário" do AD do Azure já selecionada por padrão. Se o aplicativo cliente estiver sendo registrado em um locatário do Azure AD do Office 365, as APIs Web e permissões para o SharePoint e Exchange Online também serão disponibilizadas para seleção. Você pode selecionar entre [dois tipos de permissão](active-directory-dev-glossary.md#permissions) nos menus suspensos ao lado da API Web desejada:

* Permissões de Aplicativo: o aplicativo cliente precisa acessar a API Web diretamente por si só (sem contexto de usuário). Esse tipo de permissão requer o consentimento do administrador e também não está disponível para aplicativos cliente nativos.
* Permissões Delegadas: o aplicativo cliente precisa acessar a API Web como usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão esteja configurada para pedir consentimento do administrador. 

> [!NOTE]
> Adicionando uma permissão delegada para um aplicativo não concede automaticamente consentimento para os usuários dentro do locatário, como fazia no Portal clássico do Azure. Os usuários devem consentir manualmente para as permissões delegadas adicionadas em tempo de execução, a menos que o administrador clica o **conceder permissões** botão do **permissões necessárias** seção da página do aplicativo no portal do Azure. 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Para adicionar credenciais ou permissões para acessar APIs Web
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Para adicionar uma chave secreta para credenciais do seu aplicativo web, clique na seção "Chaves" na lâmina configurações.  
   
   * Adicionar uma descrição para a sua chave e selecionar 1 ou duração de 2 anos. 
   * A coluna mais à direita conterá o valor da chave, depois que você salvar as alterações de configuração. Não se esqueça de voltar a esta seção e a copie depois de clicar em salvar e, portanto, você a usará em seu aplicativo cliente durante a autenticação em tempo de execução.
5. Para adicionar permissões para acessar o recurso APIs do cliente, clique na seção "Permissões necessárias" na lâmina configurações. 
   
   * Primeiro, clique no botão “Adicionar”.
   * Clique em "Selecione uma API" para selecionar o tipo de recursos que você deseja escolher.
   * Percorra a lista de APIs disponíveis ou usar a caixa Pesquisar para selecionar os aplicativos de recursos disponíveis em seu diretório que expõem uma API da web. Clique no recurso que você está interessado e clique em **selecione**.
   * Quando selecionada, você pode mover para o **selecione permissões** menu, onde você pode selecionar as "Permissões de aplicativo" e "Permissões delegadas" para seu aplicativo.
   
6. Quando terminar, clique o **feito** botão.

> [!NOTE]
> Clicar no botão **Concluído** também define automaticamente as permissões para o aplicativo no seu diretório com base nas permissões para outros aplicativos que você configurou.  É possível ver essas permissões de aplicativo na folha **Configurações** do aplicativo.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurando um aplicativo de recurso para expor APIs Web
Você pode desenvolver uma API Web e disponibilizá-la aos aplicativos cliente expondo [escopos](active-directory-dev-glossary.md#scopes) e [funções](active-directory-dev-glossary.md#roles) de acesso. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365 Os escopos de acesso são expostos por meio do [manifesto do aplicativo](active-directory-dev-glossary.md#application-manifest), que é um arquivo JSON que representa a configuração de identidade do aplicativo.  

A seção a seguir mostrará como expor escopos de acesso ao modificar o manifesto do aplicativo de recurso.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Adicionando escopos de acesso ao aplicativo de recurso
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Clique em **Manifesto** na página do aplicativo para abrir o editor de manifesto embutido. 
5. Substitua o nó de "oauth2Permissions" com o seguinte trecho JSON. Este trecho de código é um exemplo de como expor um escopo conhecido como "representação de usuário", que permite que o proprietário do recurso dê ao aplicativo cliente um tipo de acesso delegado a um recurso. Verifique se você mudou o texto e os valores do seu próprio aplicativo:
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    O valor da ID deve ser um novo GUID gerado que você cria usando uma [ferramenta de geração de GUID](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) ou de modo programático. Ele representa um identificador exclusivo para a permissão que é exposto pela API Web. Depois que o cliente estiver configurado corretamente para solicitar acesso à sua API Web e chamar a API Web, ele apresentará um token JWT do OAuth 2.0 que tem a declaração de escopo (scp) definida com o valor acima que, nesse caso, é user_impersonation.
   
   > [!NOTE]
   > É possível expor escopos adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor vários escopos associados a uma variedade de funções diferentes. Agora, você pode controlar o acesso à API Web usando a declaração de escopo (scp) no token JWT do OAuth 2.0 recebido.
   > 
   > 
6. Clique em **Salvar** para salvar o manifesto. A API Web está configurada para ser usada por outros aplicativos do seu diretório.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Para verificar se a API da Web é exposta a outros aplicativos em seu diretório
1. No menu superior, clique em **Registros do Aplicativo**, selecione o aplicativo cliente para o qual deseja configurar o acesso à API Web e navegue até a folha Configurações.
2. Do **permissões necessárias** seção, selecione a API que expôs uma permissão para web. No menu suspenso Permissões Delegadas, escolha a nova permissão.

![Permissões da lista de tarefas são mostradas](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto do aplicativo
O manifesto do aplicativo, na verdade, funciona como um mecanismo para atualizar a entidade de aplicativo, que define todos os atributos de configuração de identidade de um aplicativo do Azure AD, incluindo os escopos de acesso de API que discutimos. Para saber mais sobre a entidade de Aplicativo, confira a [Documentação da entidade de aplicativo da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Nela, você encontrará informações de referência completas sobre os membros da entidade de aplicativo usados para especificar permissões para sua API:  

* o membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) que podem ser usadas para definir as **Permissões de Aplicativo** para uma API Web  
* o membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) que podem ser usadas para definir as **Permissões Delegadas** para uma API Web

Para obter mais informações gerais sobre os conceitos do manifesto do aplicativo, veja [Entendendo o manifesto de aplicativo do Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Acessar o Azure AD Graph e o Office 365 pelas APIs do Microsoft Graph  
Conforme mencionado anteriormente, além de expor/acessar APIs em seus próprios aplicativos de recurso, você também pode atualizar o aplicativo cliente para acessar APIs expostas pelos recursos da Microsoft.  A API do Microsoft Graph, chamada de "Microsoft Graph" na lista de Permissões para outros aplicativos, está disponível para todos os aplicativos registrados no Azure AD. Se você estiver registrando seu aplicativo cliente em um locatário do Azure AD que foi provisionado pelo Office 365, também poderá acessar todas as permissões expostas pela API do Microsoft Graph a vários recursos do Office 365.

Para conferir uma discussão completa sobre os escopos de acesso expostos pela API do Microsoft Graph, consulte o artigo [Escopos de permissão | Conceitos da API do Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes) artigo.

> [!NOTE]
> Devido a uma limitação atual, os aplicativos cliente nativos só poderão chamar a Graph API do AD do Azure se eles usarem a permissão "Acessar diretório da sua organização".  Essa restrição não se aplica a aplicativos Web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configurando aplicativos multilocatários
Ao adicionar um aplicativo ao AD do Azure, talvez você queira que ele seja acessado apenas por usuários da sua organização. Ou talvez você queira que o aplicativo seja acessado por usuários em organizações externas. Esses dois tipos de aplicativo são chamados de aplicativos de locatário único e multilocatários. Você pode modificar a configuração de um aplicativo de locatário único para torná-lo um aplicativo multilocatário, que é discutido nesta seção.

É importante observar as diferenças entre um aplicativo de locatário único e multilocatário:  

* Um aplicativo de locatário único é destinado para uso em uma organização. Normalmente, trata-se de um aplicativo LoB (linha de negócios) escrito por um desenvolvedor corporativo. Um aplicativo de locatário único só precisa ser acessado por usuários em um diretório e, como resultado, ele só precisa ser provisionado em um diretório.
* Um aplicativo multilocatário é destinado para uso em muitas organizações. Geralmente, trata-se de um aplicativo Web SaaS (software como serviço) escrito por um ISV (fornecedor independente de software). Os aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados, o que requer o autorização do usuário ou administrador para registrá-los, com suporte da estrutura de autorização do AD do Azure. Observe que todos os aplicativos cliente nativos são multilocatários por padrão, pois eles são instalados no dispositivo do proprietário do recurso. Confira a seção acima, Visão geral da estrutura de autorização, para obter mais detalhes sobre a estrutura de autorização.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>Permitir que usuários externos concedam ao aplicativo acesso aos seus recursos
Se estiver escrevendo um aplicativo que queira disponibilizar para seus clientes ou parceiros fora da organização, você precisará atualizar a definição do aplicativo no portal do Azure.

> [!NOTE]
> Ao habilitar o multilocatário, você deve garantir que o URI da ID do Aplicativo do seu aplicativo pertença a um domínio verificado. Além disso, a URL de Retorno deve começar com https://. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](active-directory-application-objects.md).
> 
> 

Para habilitar o acesso ao seu aplicativo para usuários externos: 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Na folha Configurações, clique em **Propriedades** e inverta a opção **Multilocatário** para **Sim**.

Depois de ter feito a alteração acima, os usuários e administradores em outras organizações poderão conceder ao aplicativo acesso aos respectivos diretórios e outros dados.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Disparando a estrutura de autorização do AD do Azure em tempo de execução
Para usar a estrutura de autorização, os aplicativos cliente multilocatários devem solicitar autorização usando o OAuth 2.0. Os [códigos de exemplo](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) estão disponíveis para mostrar como um aplicativo Web, um aplicativo Nativo ou um aplicativo de servidor/daemon solicita códigos de autorização e tokens de acesso para chamar APIs Web.

Seu aplicativo Web também pode proporcionar uma experiência de inscrição para usuários. Se você oferecer uma experiência de inscrição, é esperado que o usuário clique em um botão de inscrição que redirecionará o navegador para o ponto de extremidade de autorização OAuth2.0 do AD do Azure ou um ponto de extremidade userinfo do OpenID Connect. Esses pontos de extremidade permitem ao aplicativo obter informações sobre o novo usuário inspecionando o id_token. Depois da fase de inscrição, o usuário verá uma solicitação de autorização semelhante àquela mostrada acima na seção Visão geral da estrutura de autorização.

Como alternativa, o aplicativo Web também pode oferecer uma experiência que permita aos administradores "inscreverem-se em minha empresa". Essa experiência também redireciona o usuário para o ponto de extremidade de autorização OAuth 2.0. Nesse caso, você passa um parâmetro prompt=admin_consent ao ponto de extremidade de autorização para forçar a experiência de autorização do administrador, na qual o administrador dará autorização em nome da respectiva organização. Somente um usuário autenticado com uma conta que pertença à função de Administrador Global pode dar autorização; caso contrário, um erro será exibido. No consentimento bem-sucedido, a resposta conterá admin_consent=true. Ao resgatar um token de acesso, você também receberá um id_token que fornecerá informações sobre a organização e o administrador que se inscreveu no seu aplicativo.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitando a concessão implícita do OAuth 2.0 para Aplicativos de Uma Página
Os Aplicativos de Uma Página (SPAs) normalmente são estruturados com um front-end pesado de JavaScript executado no navegador, que chama o back-end da API Web do aplicativo para executar sua lógica de negócios. Para SPAs hospedados no Azure AD, use concessão implícita do OAuth 2.0 para autenticar o usuário no Azure AD e obter um token que pode ser usado para proteger chamadas do cliente JavaScript do aplicativo para sua API Web de back-end. Depois que o usuário tiver dado consentimento, esse mesmo protocolo de autenticação poderá ser usado para obter tokens para proteger chamadas entre o cliente e outros recursos de API Web configurados para o aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, consulte [Noções básicas sobre o fluxo de concessão implícita de OAuth2 no Azure Active Directory ](active-directory-dev-understanding-oauth2-implicit-grant.md).

Por padrão, a Concessão Implícita do OAuth 2.0 está desabilitada para aplicativos. Você pode habilitar a Concessão Implícita do OAuth 2.0 para seu aplicativo definindo o valor `oauth2AllowImplicitFlow` em seu [manifesto do aplicativo](active-directory-application-manifest.md), que é um arquivo JSON que representa a configuração da identidade do seu aplicativo.

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar a Concessão Implícita do OAuth 2.0
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Na página do aplicativo, clique em **Manifesto** para abrir o editor de manifesto embutido.
   Localize e defina o valor de "oauth2AllowImplicitFlow" como "true". Por padrão, é "false".
   
    `"oauth2AllowImplicitFlow": true,`
5. Salve o manifesto atualizado. Uma vez salva, a API Web agora está configurada para usar a Concessão Implícita do OAuth 2.0 para autenticar usuários.


## <a name="removing-an-application"></a>Removendo um aplicativo
Esta seção descreve como remover um aplicativo do locatário do AD do Azure.

### <a name="removing-an-application-authored-by-your-organization"></a>Removendo um aplicativo autorizado pela sua organização
Esses são os aplicativos que são mostrados no filtro "Aplicativos que minha empresa possui" na página principal "Aplicativos" do locatário do AD do Azure. Em termos técnicos, esses são os aplicativos que você registrou manualmente pelo portal clássico do Azure ou de modo programático pelo PowerShell ou pela API do Graph. Mais especificamente, eles são representados por um objeto de Aplicativo e Entidade de Serviço em seu locatário. Para saber mais, confira [Objetos de Aplicativo e de Entidade de Serviço](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para remover um aplicativo de locatário único do diretório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Na página do aplicativo, clique em **excluir**.
5. Clique em **Sim** na mensagem de confirmação.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Para remover um aplicativo multilocatário do diretório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD ao selecionar sua conta no canto superior direito da página.
3. No menu superior, escolha **Azure Active Directory**, clique em **Registros do Aplicativo** e clique no aplicativo que você deseja configurar. Isso levará você à página Início Rápido do aplicativo e abrirá a folha Configurações do aplicativo.
4. Na lâmina configurações, escolha **propriedades** e inverter a **multilocatário** alternar para **não**. Isso converte seu aplicativo em locatário único, mas o aplicativo continuará em uma organização que já tenha sido consentida para ele.
5. Clique no **excluir** botão da página de aplicativo.
6. Clique em **Sim** na mensagem de confirmação.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Removendo um aplicativo multilocatário autorizado por outra organização
Esses são um subconjunto dos aplicativos que são mostrados no filtro "Aplicativos que minha empresa usa" na página principal "Aplicativos" do seu locatário do AD do Azure, mais especificamente aqueles que não estão listados na lista "Aplicativos que minha empresa possui". Em termos técnicos, são aplicativos multilocatários registrados durante o processo de autorização. Mais especificamente, eles são representados apenas por um objeto de Entidade de Serviço em seu locatário. Para saber mais, confira [Objetos de Aplicativo e de Entidade de Serviço](active-directory-application-objects.md) .

Para remover o acesso de um aplicativo multilocatário ao seu diretório (depois de ter dado autorização), o administrador da empresa deve ter uma assinatura do Azure para remover o acesso usando o portal do Azure. Como alternativa, o administrador de empresa pode usar os [Cmdlets do PowerShell do Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas
* Veja [Diretrizes de identidade visual para aplicativos integrados](active-directory-branding-guidelines.md) para obter dicas sobre a orientação visual para seu aplicativo.
* Para obter mais detalhes sobre a relação entre os objetos de Entidade de serviço e de Aplicativo do aplicativo, veja [Objetos de Aplicativo e objetos de Entidade de Serviço](active-directory-application-objects.md).
* Para saber mais sobre a função do manifesto do aplicativo, veja [Noções básicas sobre o manifesto do aplicativo do Azure Active Directory](active-directory-application-manifest.md)
* Veja as definições do [Glossário de desenvolvedor do Azure AD](active-directory-dev-glossary.md) de alguns dos conceitos de desenvolvedor do Azure Active Directory (AD).
* Visite o [Guia do desenvolvedor do Active Directory](active-directory-developers-guide.md) para obter uma visão geral de todo o conteúdo relacionado de desenvolvedores.


