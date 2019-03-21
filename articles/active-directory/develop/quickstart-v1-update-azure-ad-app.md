---
title: Integrando aplicativos com o Active Directory do Azure
description: Saiba como atualizar um aplicativo no Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103801"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Início Rápido: atualizar um aplicativo no Azure Active Directory

Os desenvolvedores corporativos e os provedores de SaaS (software como serviço) que registraram aplicativos no Azure AD (Azure Active Directory) podem precisar configurar seus aplicativos para acessar outros recursos, como APIs Web, disponibilizá-los em outras organizações, entre outras ações.

Neste Início Rápido, você aprenderá as várias maneiras de configurar ou atualizar seu aplicativo para atender aos requisitos e às necessidades da sua organização e de outras.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estas etapas:

* Leia a visão geral da [estrutura de consentimento do Azure AD](consent-framework.md), que é importante entender ao criar aplicativos que precisam ser usados por outros usuários ou aplicativos.
* Obtenha um locatário do Azure AD com aplicativos registrados.
  * Se você ainda não tiver um locatário [saiba como obter um](quickstart-create-new-tenant.md).
  * Se não houver aplicativos registrados no seu locatário, [saiba como adicionar um aplicativo no Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Configure um aplicativo cliente para acessar APIs Web

Para que um aplicativo cliente Web/confidencial possa participar de um fluxo de concessão de autorização que requer autenticação (e obter um token de acesso), ele deverá estabelecer credenciais seguras. O método de autenticação padrão com suporte no portal do Azure é a ID do Cliente + chave secreta. Esta seção aborda as etapas de configuração necessárias para fornecer a chave secreta com as credenciais do seu cliente.

Para que um cliente possa acessar uma API Web exposta por um aplicativo de recurso (como a API do Microsoft Graph), a estrutura de consentimento garante que o cliente obtenha a concessão de permissão necessária, com base nas permissões solicitadas. Por padrão, todos os aplicativos podem escolher permissões do **Azure Active Directory** (API do Graph) e do modelo de implantação clássico do Azure. A [permissão de "Entrar e ler perfil do usuário" da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) também é selecionada por padrão. Se o cliente estiver sendo registrado em um locatário que tenha contas inscritas no Office 365, APIs Web e permissões para o SharePoint e Exchange Online estarão disponíveis para seleção. Você pode selecionar entre dois tipos de permissões para cada API Web desejada:

- Permissões de aplicativo: o aplicativo cliente precisa acessar a API Web diretamente por si só (sem contexto de usuário). Esse tipo de permissão requer o consentimento do administrador e também não está disponível para aplicativos cliente nativos.
- Permissões delegadas: o aplicativo cliente precisa acessar a API Web como o usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a um aplicativo não dá automaticamente consentimento aos usuários no locatário. Os usuários devem consentir manualmente para as permissões delegadas adicionadas em tempo de execução, a menos que o administrador dê consentimento em nome de todos os usuários.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Adicionar credenciais do aplicativo ou permissões para acessar APIs Web

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
3. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory**, selecione **Registros de aplicativo** e, em seguida, localize e selecione o aplicativo que você deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Você será levado à página principal de registro do aplicativo, que contém a página **Configurações** do aplicativo. Para adicionar uma credencial ao seu aplicativo Web:
   1. Selecione a seção **Chaves** na página **Configurações**.
   1. Para adicionar um certificado:
      - Selecione **Carregar chave pública**.
      - Selecione o arquivo que você gostaria de carregar. Ele deve ser um dos seguintes tipos de arquivo: .cer, .pem, .crt.
   1. Para adicionar uma senha:
      - Adicione uma descrição para a sua chave.
      - Selecione uma duração.
      - Clique em **Salvar**. A coluna mais à direita conterá o valor da chave, depois que você salvar as alterações de configuração. **Copie a chave** para uso no código do aplicativo cliente, já que ela não estará acessível depois que sair desta página.

5. Para adicionar permissões para acessar as APIs do recurso do seu cliente
   1. Selecione a seção **Permissões necessárias** na página **Configurações** e, em seguida, selecione **Adicionar**.
   1. Selecione **Selecionar uma API** para escolher o tipo de recursos que você deseja.
   1. Percorra a lista de APIs disponíveis ou usar a caixa Pesquisar para selecionar os aplicativos de recursos disponíveis em seu diretório que expõem uma API da web. Escolha o recurso em que você está interessado e clique em **Selecionar**.
   1. Na página **Habilitar acesso**, selecione as permissões do aplicativo e/ou as permissões delegadas de que seu aplicativo precisa para acessar a API.
   
   ![Atualizar um registro de aplicativo – api de permissão](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![Atualizar um registro de aplicativo – perms de permissão](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Quando terminar, clique no botão **Selecionar** na página **Habilitar Acesso** e, em seguida, no botão **Concluído** na página **Adicionar acesso à API**. Você é levado de volta à página **Permissões Necessárias**, em que o novo recurso é adicionado à lista de APIs.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurando um aplicativo de recurso para expor APIs Web

Você pode desenvolver uma API Web e disponibilizá-la aos aplicativos cliente expondo [escopos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) de acesso. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a API do Graph e as APIs do Office 365. Os escopos de acesso são expostos por meio do [manifesto do aplicativo](developer-glossary.md#application-manifest), que é um arquivo JSON que representa a configuração de identidade do aplicativo.

A seção a seguir mostra como expor escopos de acesso modificando o manifesto do aplicativo de recurso.

### <a name="add-access-scopes-to-your-resource-application"></a>Adicionar escopos de acesso ao aplicativo de recurso

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
3. No painel de navegação à esquerda, selecione **Azure Active Directory > Registros de aplicativo** e, em seguida, localize/selecione o aplicativo que deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo. Mude para a página **Editar manifesto** clicando em **Manifesto** na página de registro do aplicativo. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Opcionalmente, você pode clicar em **Baixar** e editar localmente, então usar **Carregar** para reaplicar ao seu aplicativo.
5. Neste exemplo, vamos expor um novo escopo chamado `Employees.Read.All` em nossa API/recursos adicionando o seguinte elemento JSON à coleção `oauth2Permissions`. O escopo `user_impersonation` existente é fornecido por padrão durante o registro. `user_impersonation` permite que um aplicativo cliente solicite permissão para acessar o recurso sob a identidade do usuário conectado. Adicione a vírgula após o elemento de escopo `user_impersonation` existente e mude os valores de propriedade para atender às necessidades do seu recurso. 

   ```json
   {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
   }
   ```

   > [!NOTE]
   > O valor `id` precisa ser gerado de modo programático ou usando uma ferramenta de geração de GUID como [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). O `id` representa um identificador exclusivo para o escopo exposto pela API Web. Depois que um cliente está configurado adequadamente com permissões para acessar a sua API Web, o Azure AD emite um token de acesso OAuth2.0. Quando o cliente chama a API Web, ele apresenta o token de acesso que tem a declaração de escopo (scp) definida para as permissões solicitadas no seu registro de aplicativo.
   >
   > É possível expor escopos adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor vários escopos associados a uma variedade de funções diferentes. O recurso pode controlar o acesso à API Web em tempo de execução, avaliando declarações de escopo (`scp`) no token de acesso OAuth 2.0 recebido.

6. Ao terminar, clique em **Salvar**. Agora sua API Web está configurada para ser usada por outros aplicativos do seu diretório.

   ![Atualizar o registro de um aplicativo](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verificar se a API Web está exposta a outros aplicativos em seu locatário

1. Volte para o locatário do Azure AD, clique em **Registros de aplicativo** novamente e, em seguida, localize/clique no aplicativo cliente que você deseja configurar.

   ![Atualizar o registro de um aplicativo](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Repita a etapa 5 como fez em [Configurar um aplicativo cliente para acessar APIs Web](#configure-a-client-application-to-access-web-apis). Quando você chega à etapa **Selecionar uma API**, pesquise seu recurso digitando seu nome de aplicativo no campo de pesquisa e clique em **Selecionar**.

3. Na página **Habilitar Acesso**, você deve ver o novo escopo, disponível para solicitações de permissão do cliente.

   ![Novas permissões são mostradas](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto do aplicativo

O manifesto do aplicativo funciona como um mecanismo para atualizar a entidade do aplicativo, que define todos os atributos de configuração de identidade de um aplicativo do Azure AD, incluindo os escopos de acesso de API que abordamos. Para obter mais informações sobre a entidade Aplicativo e seu esquema, confira a [Documentação da entidade do Aplicativo da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completas sobre os membros da entidade do Aplicativo usados para especificar permissões para a sua API, incluindo:  

- O membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), usado para definir [permissões de aplicativo](developer-glossary.md#permissions) para uma API Web. 
- O membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), usado para definir as [permissões delegadas](developer-glossary.md#permissions) para uma API Web.

Para obter mais informações sobre os conceitos do manifesto do aplicativo em geral, confira [Manifesto do aplicativo do Azure AD](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Acessar o Azure AD Graph e o Office 365 pelas APIs do Microsoft Graph  

Conforme mencionado anteriormente, além de expor/acessar APIs para seus próprios aplicativos, você também pode registrar seu aplicativo cliente para acessar APIs expostas pelos recursos da Microsoft. A API do Microsoft Graph, conhecida como "Microsoft Graph" na lista de recursos/APIs do portal, está disponível a todos os aplicativos registrados com o Azure AD. Se você estiver registrando seu aplicativo cliente em um locatário que contenha contas que se inscreveram para uma assinatura do Office 365, você também poderá acessar os escopos expostos pelos vários recursos do Office 365.

Para obter uma discussão completa sobre os escopos expostos pela API do Microsoft Graph, consulte o artigo [ referência de permissões do Microsoft Graph ](https://docs.microsoft.com/graph/permissions-reference).

> [!NOTE]
> Devido a uma limitação atual, os aplicativos cliente nativos só poderão chamar a API do Graph do AD do Azure se eles usarem a permissão "Acessar diretório da sua organização". Essa restrição não se aplica a aplicativos Web.

## <a name="configuring-multi-tenant-applications"></a>Configurando aplicativos multilocatários

Ao registrar um aplicativo no Azure AD, talvez você queira que ele seja acessado apenas por usuários da sua organização. Ou talvez você queira que o aplicativo fique acessível a usuários em organizações externas. Esses dois tipos de aplicativo são chamados de aplicativos de locatário único e multilocatário. Esta seção discute como modificar a configuração de um aplicativo de locatário único para torná-lo um aplicativo multilocatário.

É importante observar as diferenças entre um aplicativo de locatário único e multilocatário:  

- Um aplicativo de locatário único é destinado para uso em uma organização. Normalmente, trata-se de um aplicativo LoB (linha de negócios) escrito por um desenvolvedor corporativo. Um aplicativo de locatário único só pode ser acessado por usuários com contas no mesmo locatário que o registro do aplicativo. Como resultado, ele só precisa ser provisionado em um diretório.
- Um aplicativo multilocatário é destinado a uso em muitas organizações. Chamado de aplicativo Web SaaS (software como serviço), costuma ser escrito por um ISV (fornecedor independente de software). Aplicativos multilocatário devem ser provisionados em cada locatário que os usuários precisem acessar. Para locatários diferentes daquele em que o aplicativo está registrado, o consentimento do usuário ou do administrador é necessário para registrá-los. Observe que os aplicativos cliente nativos são multilocatários por padrão, pois são instalados no dispositivo do proprietário do recurso. Confira a seção anterior Visão geral da estrutura de consentimento para obter detalhes sobre a estrutura de consentimento.

Tornar um aplicativo multilocatário requer tanto alterações de registro de aplicativo quanto alterações no próprio aplicativo Web. As seções a seguir cobrem ambas.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Alterar o registro do aplicativo para dar suporte a multilocatário

Se você estiver escrevendo um aplicativo que queira disponibilizar aos seus clientes ou parceiros fora da sua organização, você precisará atualizar a definição de aplicativo no portal do Azure.

> [!IMPORTANT]
> O Azure AD exige que o URI de ID do aplicativo de aplicativos multilocatário seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente que o URI da ID do Aplicativo seja exclusivo nesse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários.
> A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD. Por exemplo, se o nome do locatário fosse contoso.onmicrosoft.com, então, o URI da ID do aplicativo válido seria https://contoso.onmicrosoft.com/myapp. Se o locatário tivesse um domínio verificado de contoso.com, então, um URI da ID do aplicativo válido também seria https://contoso.com/myapp. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

Para dar a usuários externos a capacidade de acessar seu aplicativo:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Na página **Configurações**, clique em **Propriedades** e mude a opção **Multilocatário** para **Sim**.

Depois de ter feito as alterações, usuários e administradores de outras organizações podem conceder aos próprios usuários a capacidade de entrar no aplicativo, permitindo que o aplicativo acesse recursos protegidos pelo locatário deles.

### <a name="changing-the-application-to-support-multi-tenant"></a>Alterar o aplicativo para dar suporte a multilocatário

O suporte para aplicativos multilocatário depende muito da estrutura de consentimento do Azure AD. O consentimento é o mecanismo que permite que um usuário de outro locatário conceda ao aplicativo acesso aos recursos protegidos pelo locatário do usuário. Essa experiência é chamada de "consentimento do usuário".

Seu aplicativo Web também pode oferecer:

- A capacidade de os administradores "inscreverem a minha empresa". Essa experiência, chamada de "consentimento do administrador", oferece ao administrador a capacidade de conceder consentimento em nome de *todos os usuários* em sua organização. Somente um usuário autenticado com uma conta que pertença à função de Administrador Global pode dar consentimento do administrador; os outros recebem um erro.
- Uma experiência de inscrição para os usuários. Espera-se que o usuário receba um botão de "inscrição" que redirecionará o navegador para o ponto de extremidade `/authorize` do Azure AD OAuth2.0 ou um ponto de extremidade `/userinfo` do OpenID Connect. Esses pontos de extremidade permitem ao aplicativo obter informações sobre o novo usuário inspecionando o id_token. Após a fase de inscrição, o usuário receberá uma solicitação de consentimento semelhante àquela mostrada na seção Visão geral da estrutura de consentimento.

Para obter mais informações sobre as alterações de aplicativo necessárias para dar suporte ao acesso multilocatário e experiências de inscrição/entrada, consulte:

- [Como entrar em qualquer usuário do Azure AD (Active Directory) usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md)
- A lista de [Exemplos de código de multilocatário](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Início Rápido: adicionar identidade visual da empresa à sua página de entrada no Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitando a concessão implícita do OAuth 2.0 para aplicativos de página única

Os SPAs (aplicativos de página única) normalmente são estruturados com um front-end que usa muito JavaScript e é executado no navegador, que chama o back-end da API Web do aplicativo para executar sua lógica de negócios. Para SPAs hospedados no Azure AD, use a Concessão Implícita do OAuth 2.0 para autenticar o usuário com o Azure AD e obter um token que possa ser usado para proteger chamadas do cliente JavaScript do aplicativo para sua API Web de back-end.

Depois que o usuário tiver dado consentimento, esse mesmo protocolo de autenticação poderá ser usado para obter tokens para proteger chamadas entre o cliente e outros recursos de API Web configurados para o aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, consulte [Noções básicas sobre o fluxo de concessão implícita de OAuth2 no Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Por padrão, a Concessão Implícita do OAuth 2.0 está desabilitada para aplicativos. Você pode habilitar a Concessão Implícita do OAuth 2.0 para seu aplicativo configurando o valor `oauth2AllowImplicitFlow` em seu [manifesto do aplicativo](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar a Concessão Implícita do OAuth 2.0

> [!NOTE]
> Para obter detalhes sobre como editar o manifesto do aplicativo, primeiro examine a seção anterior, [Configuração de um aplicativo de recurso para expor APIs Web](#configuring-a-resource-application-to-expose-web-apis).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** então localize/clique no aplicativo que você deseja configurar. Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
4. Mude para a página **Editar manifesto** clicando em **Manifesto** da página de registro do aplicativo. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Localize e defina o valor de "oauth2AllowImplicitFlow" como "true". Por padrão, é definido como "false".
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. Salve o manifesto atualizado. Uma vez salva, a API Web agora está configurada para usar a Concessão Implícita do OAuth 2.0 para autenticar usuários.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre esses outros inícios rápidos de gerenciamento de aplicativos relacionados para aplicativos que usam o ponto de extremidade do Azure AD v1.0:
- [Adicionar um aplicativo ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Remover um aplicativo do Azure AD](quickstart-v1-remove-azure-ad-app.md)

Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
