---
title: "Implantar, chamar e autenticar as APIs Web e APIs REST para Aplicativo Lógico do Azure | Microsoft Docs"
description: "Implantar, autenticar e chamar APIs Web e APIs REST em fluxos de trabalho para integrações do sistema com o Aplicativo Lógico do Azure"
keywords: "APIs Web, APIs REST, conectores, fluxos de trabalho, integrações do sistema, autenticar"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: pt-br
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>Implantar, chamar e autenticar as APIs personalizadas como conectores para aplicativos lógicos

Depois que você [criar APIs personalizadas](./logic-apps-create-api-app.md) que fornecem ações ou gatilhos para usar em fluxos de trabalho de aplicativos lógicos, você deve implantar suas APIs antes de chamá-las. E embora você possa chamar qualquer API de um aplicativo lógico, para a melhor experiência, adicione [metadados do Swagger](http://swagger.io/specification/) que descrevem as operações e os parâmetros de sua API. Este arquivo Swagger ajuda sua API a funcionar melhor e se integrar aos aplicativos lógicos mais facilmente.

Você pode implantar suas APIs como [aplicativos Web](../app-service-web/app-service-web-overview.md), mas considere implantar suas APIs como [aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md), o que pode facilitar o trabalho quando você criar, hospedar e consumir APIs locais e na nuvem. Você não precisa alterar o código em suas APIs – basta implantar seu código para um aplicativo de API. Você pode hospedar suas APIs no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), uma oferta de PaaS (plataforma como serviço) que fornece uma das maneiras mais fáceis, mais escalonáveis e melhores de hospedar a API.

Para autenticar chamadas de aplicativos lógicos para suas APIs, você pode definir o Azure Active Directory no Portal do Azure para que não precise atualizar seu código. Ou você pode exigir e aplicar a autenticação por meio do seu código da API.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implantar sua API como um aplicativo Web ou aplicativo de API

Antes de chamar sua API personalizada de um aplicativo lógico, implante sua API como um aplicativo Web ou aplicativo de API do Serviço de Aplicativo do Azure. Além disso, para tornar o documento do Swagger legível pelo Designer de Aplicativo Lógico, configure as propriedades de definição da API e ative o [CORS (compartilhamento de recursos entre origens)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig) para seu aplicativo Web ou de API.

1. No Portal do Azure, selecione seu aplicativo Web ou aplicativo de API.

2. Na folha que é aberta, em **API**, escolha **Definição da API**. Defina o **Localização da definição de API** como a URL de seu arquivo swagger.json.

   Normalmente, a URL é exibida neste formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para o arquivo Swagger para sua API personalizada](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. Em **API**, escolha **CORS**. Defina a política de CORS de **Origens permitidas** como **'*'** (permitir todos).

   Essa configuração permite solicitações do Designer de Aplicativo Lógico.

   ![Permitir solicitações do Designer de Aplicativo Lógico para sua API personalizada](media/logic-apps-custom-hosted-api/custom-api-cors.png)

Para obter mais informações, consulte estes artigos:

* [Adicionar metadados do Swagger para APIs Web do ASP.NET](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [Implantar APIs Web do ASP.NET no Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chamar sua API personalizada de fluxos de trabalho de aplicativo lógico

Depois de configurar as propriedades de definição de API e os CORS, os gatilhos e ações da sua API personalizada devem ficar disponíveis para você incluir em seu fluxo de trabalho de aplicativo lógico. 

*  Para exibir sites que têm URLs do Swagger, você pode navegar pelos seus sites de assinatura no Designer de Aplicativos Lógicos.

*  Para exibir as ações e entradas disponíveis apontando para um documento do Swagger, use a [ação HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo APIs que não têm ou expõem um documento do Swagger, você sempre pode criar uma solicitação com a [ação HTTP](../connectors/connectors-native-http.md).

## <a name="authenticate-calls-to-your-custom-api"></a>Autenticar chamadas à API personalizada

Você pode proteger as chamadas à API personalizada das seguintes maneiras:

* [Sem alterações de código](#no-code): proteja sua API com [Azure AD (Azure Active Directory)](../active-directory/active-directory-whatis.md) por meio do Portal do Azure, para que você não precise atualizar o código ou reimplantar sua API.

  > [!NOTE]
  > Por padrão, a autenticação do Azure AD que você ativa no Portal do Azure não fornece autorizações refinadas. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

* [Atualizar o código da API](#update-code): proteja sua API impondo a [autenticação de certificado](#certificate), a [autenticação básica](#basic) ou a [autenticação do Azure AD](#azure-ad-code) por meio de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticar chamadas para a API sem alterar o código

Eis as etapas gerais para este método:

1. Crie duas [identidades de aplicativo do Azure AD (Azure Active Directory)](../app-service-api/app-service-api-dotnet-service-principal-auth.md): uma para seu aplicativo lógico e um para seu aplicativo Web (ou aplicativo de API).

2. Para autenticar chamadas à API, use as credenciais (ID do cliente e segredo) para a [entidade de serviço](../app-service-api/app-service-api-dotnet-service-principal-auth.md) associada à identidade do aplicativo do Azure AD para seu aplicativo lógico.

3. Inclua as IDs de aplicativo na definição do aplicativo lógico.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Criar uma identidade do aplicativo do Azure AD para seu aplicativo lógico

Seu aplicativo lógico usa essa identidade do aplicativo do Azure AD para autenticar no Azure AD. Você só precisa configurar essa identidade uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos lógicos, embora possa criar identidades exclusivas para cada aplicativo lógico. Você pode configurar essas identidades no Portal do Azure, [Portal Clássico do Azure](#app-identity-logic-classic) ou usar o [PowerShell](#powershell).

**Criar a identidade do aplicativo para seu aplicativo lógico no Portal do Azure**

1. No [Portal do Azure](https://portal.azure.com "https://portal.azure.com"), escolha **Azure Active Directory**. 

2. Confirme que você está no mesmo diretório que o aplicativo Web ou aplicativo de API.

   > [!TIP]
   > Para mudar de diretório, clique em seu perfil e selecione outro diretório. Ou escolha **Visão Geral** > **Mudar diretório**.

3. No menu de diretório, em **Gerenciar**, escolha **Registros do aplicativo** > **Novo registro do aplicativo**.

   > [!TIP]
   > Por padrão, a lista de registros do aplicativo mostra todos os registros do aplicativo em seu diretório. Para exibir apenas seus registros do aplicativo, ao lado da caixa de pesquisa, selecione **Meus aplicativos**. 

   ![Criar novo registro de aplicativo](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. Dê um nome à identidade do aplicativo, deixe **Tipo do aplicativo** definido como **Aplicativo Web/API**, forneça uma cadeia de caracteres formatada única como um domínio para **URL de Entrada** e escolha **Criar**.

   ![Forneça o nome e a URL de entrada para a identidade do aplicativo](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   A identidade do aplicativo que você criou para seu aplicativo lógico agora aparece na lista de registros de aplicativo.

   ![Identidade do aplicativo para seu aplicativo lógico](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. Na lista de registros de aplicativo, selecione sua nova identidade do aplicativo. Copie e salve a **ID do Aplicativo** para usar como a “ID do cliente” para o aplicativo lógico na Parte 3.

   ![Copie e salve a ID do aplicativo para o aplicativo lógico](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. Se as configurações de identidade do aplicativo não estiverem visíveis, escolha **Configurações** ou **Todas as configurações**.

7. Em **Acesso à API**, escolha **Chaves**. Em **Descrição**, forneça um nome para sua chave. Em **Expira**, selecione uma duração para a chave.

   A chave que você está criando atua como o “segredo” ou senha da identidade do aplicativo para seu aplicativo lógico.

   ![Criar chave para identidade de aplicativo lógico](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **Salvar**. Em **Valor**, agora sua chave aparece. 
**Certifique-se de copiar e salvar sua chave** para uso posterior porque a chave é ocultada quando você sai da folha de chave.

   Quando você configurar seu aplicativo lógico na Parte 3, poderá especificar esta chave como o "segredo" ou a senha.

   ![Copiar e salvar a chave para mais tarde](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**Criar a identidade do aplicativo para seu aplicativo lógico no Portal Clássico do Azure**

1. No Portal Clássico do Azure, escolha [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2. Selecione o mesmo diretório que você usa para seu aplicativo Web ou aplicativo de API.

3. Na guia **Aplicativos**, escolha **Adicionar** na parte inferior da página.

4. Dê um nome à identidade do aplicativo e escolha **Avançar** (seta para a direita).

5. Em **Propriedades do aplicativo**, forneça uma única cadeia de caracteres formatada como um domínio para **URL de Entrada** e **URI da ID do Aplicativo** e escolha **Concluir** (marca de seleção).

6. Na guia **Configurar**, copie e salve a **ID do Cliente** para seu aplicativo lógico usar na Parte 3.

7. Em **Chaves**, abra a lista **Selecionar duração**. Selecione uma duração para a chave.

   A chave que você está criando atua como o “segredo” ou senha da identidade do aplicativo para seu aplicativo lógico.

8. Na parte inferior da página, escolha **Salvar**. Talvez seja necessário aguardar alguns segundos.

9. Em **Chaves**, certifique-se de copiar e salvar a chave que agora é exibida. 

   Quando você configurar seu aplicativo lógico na Parte 3, poderá especificar esta chave como o "segredo" ou a senha.

Para obter mais informações, saiba como [configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

<a name="powershell"></a>

**Criar a identidade do aplicativo para seu aplicativo lógico no PowerShell**

Você pode executar essa tarefa por meio do Azure Resource Manager com o PowerShell. No PowerShell, execute estes comandos:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Certifique-se de copiar a **ID do Locatário** (GUID para o locatário do Azure AD), a **ID do Aplicativo** e a senha usada.

Para obter mais informações, saiba como [criar uma entidade de serviço com o PowerShell para acessar os recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Criar uma identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API

Se seu aplicativo Web ou aplicativo de API já estiver implantado, você poderá ativar a autenticação e criar a identidade do aplicativo no Portal do Azure. Caso contrário, você pode [ativar a autenticação quando implantar com um modelo do Azure Resource Manager](#authen-deploy). 

**Criar a identidade do aplicativo e ativar a autenticação no Portal do Azure para aplicativos implantados**

1. No [Portal do Azure](https://portal.azure.com "https://portal.azure.com"), localize e selecione o aplicativo Web ou o aplicativo de API. 

2. Em **Configurações**, escolha **Autenticação/Autorização**. Em **Autenticação do Serviço de Aplicativo**, **Ative** a autenticação. Em **Provedores de Autenticação**, escolha **Azure Active Directory**.

   ![Ativar a autenticação](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. Agora crie uma identidade do aplicativo para seu aplicativo Web ou aplicativo de API conforme mostrado aqui. Na folha **Configurações do Azure Active Directory**, defina **Modo de gerenciamento** como **Express**. Escolha **Criar novo aplicativo do AD**. Dê um nome à identidade do aplicativo e escolha **OK**. 

   ![Criar identidade do aplicativo para seu aplicativo Web ou aplicativo de API](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. Na **folha Autenticação/Autorização**, escolha **Salvar**.

Agora você deve encontrar a ID do cliente e a ID do locatário para a identidade do aplicativo associada ao aplicativo Web ou aplicativo de API. Você usará essas IDs na Parte 3. Continue com estas etapas para o Portal do Azure ou [Portal Clássico do Azure](#find-id-classic).

**Localizar a ID do cliente e a ID do locatário da identidade do aplicativo do aplicativo Web ou aplicativo de API no Portal do Azure**

1. Em **Provedores de Autenticação**, escolha **Azure Active Directory**. 

   ![Escolha "Azure Active Directory"](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. Na folha **Configurações do Azure Active Directory**, defina **Modo de gerenciamento** como **Avançado**.

3. Copie a **ID do Cliente** e salve o GUID para uso na Parte 3.

   > [!TIP] 
   > Se a **ID do Cliente** e a **URL do Emissor** não aparecerem, tente atualizar o Portal do Azure e repita a Etapa 1.

4. Em **URL do Emissor**, copie e salve apenas o GUID para a Parte 3. Você também pode usar esse GUID no modelo de implantação do aplicativo Web ou aplicativo de API, se necessário.

   Esse GUID é GUID específico do locatário ("ID de locatário") e deve aparecer nessa URL: `https://sts.windows.net/{GUID}`

5. Sem salvar as alterações, feche a folha **Configurações do Azure Active Directory**.

<a name="find-id-classic"></a>

**Localizar a ID do cliente e a ID do locatário da identidade do aplicativo do aplicativo Web ou aplicativo de API no Portal Clássico do Azure**

1. No Portal Clássico do Azure, escolha [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2.  Selecione o diretório que você usa para seu aplicativo Web ou aplicativo de API.

3. Na caixa **Pesquisar**, encontre e selecione a identidade do aplicativo para seu aplicativo Web ou aplicativo de API.

4. Na guia **Configurar**, copie a **ID do Cliente** e salve o GUID para uso na Parte 3.

5. Depois de obter a ID do cliente, na parte inferior da guia **Configurar**, escolha **Exibir pontos de extremidade**.

6. Copie a URL para **Documento de Metadados Federados** e navegue para a URL.

7. No documento de metadados aberto, localize o elemento **EntityDescriptor ID** raiz, que tem um atributo **entityID** nesse formato: `https://sts.windows.net/{GUID}` 

      O GUID nesse atributo é o GUID específico do locatário (ID do Locatário).

8. Copie a ID do locatário e salve essa ID para usar na Parte 3 e também para usar no modelo de implantação do seu aplicativo Web ou aplicativo de API, se necessário.

Para saber mais, consulte esses tópicos:

* [Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**Ativar a autenticação quando implantar com um modelo do Azure Resource Manager**

Você ainda deve criar uma identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API que seja diferente da identidade de aplicativo para seu aplicativo lógico. Para criar a identidade do aplicativo, siga as etapas anteriores na Parte 2 para o Portal do Azure. Você também pode seguir as etapas na Parte 1, mas certifique-se de usar o `https://{URL}` real do aplicativo Web ou aplicativo de API para **URL de Entrada** e **URI da ID do Aplicativo**. Nessas etapas, você precisa salvar a ID do cliente e a ID do locatário para uso no modelo de implantação do aplicativo e na Parte 3.

> [!NOTE]
> Quando você cria a identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API, deve usar o Portal do Azure ou o Portal Clássico do Azure em vez do PowerShell. O cmdlet do PowerShell não configura as permissões necessárias para conectar os usuários em um site.

Depois que você obtiver a ID do cliente e a ID do locatário, inclua essas IDs como um recurso secundário do seu aplicativo Web ou aplicativo de API no seu modelo de implantação:

   ```
   "resources": [
       {
           "apiVersion": "2015-08-01",
           "name": "web",
           "type": "config",
           "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
           "properties": {
               "siteAuthEnabled": true,
               "siteAuthSettings": {
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

Para implantar automaticamente um aplicativo Web em branco e um aplicativo lógico com a autenticação do Azure Active Directory, [exiba o modelo completo aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) ou clique em **Implantar no Azure** aqui:

[![Implantar no Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Preencher a seção Autorização no aplicativo lógico

O modelo anterior já tem essa seção de autorização definida, mas se você estiver criando o aplicativo lógico diretamente, precisará incluir a seção de autorização completa.

Abra a definição do seu aplicativo lógico na exibição do código, acesse a seção da ação **HTTP**, localize a seção **Autorização** e inclua essa linha:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elemento | Descrição |
| ------- | ----------- |
| locatário |O GUID para o locatário do Azure AD |
| audiência |Obrigatório. O GUID do recurso de destino que você deseja acessar: a ID do cliente da identidade de aplicativo para seu aplicativo Web ou aplicativo de API |
| clientId |O GUID do cliente solicitando acesso: a ID do cliente da identidade do aplicativo para seu aplicativo lógico |
| segredo |Obrigatório. A chave ou a senha da identidade do aplicativo para o cliente que está solicitando o token de acesso |
| Tipo |O tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. |

Por exemplo:

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Proteger chamadas à API por meio de código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificado

Para validar as solicitações recebidas do aplicativo lógico para o aplicativo Web ou aplicativo de API, você pode usar certificados do cliente. Para configurar seu código, [aprenda a configurar a autenticação mútua TLS](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

Na seção **Autorização**, inclua essa linha: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elemento | Descrição |
| ------- | ----------- |
| type |Obrigatório. O tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`. |
| Senha |Obrigatório. A senha para acessar o certificado do cliente (arquivo PFX) |
| pfx |Obrigatório. Conteúdo codificado na Base64 do certificado do cliente (arquivo PFX) |

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar solicitações de entrada de seu aplicativo lógico para o aplicativo Web ou aplicativo de API, você pode usar a autenticação básica, como um nome de usuário e senha. A autenticação básica é um padrão comum e você pode usar essa autenticação em qualquer linguagem usada para compilar seu aplicativo Web ou aplicativo de API.

Na seção **Autorização**, inclua essa linha:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elemento | Descrição |
| --- | --- |
| type |Obrigatório. O tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`. |
| Nome de Usuário |Obrigatório. O nome de usuário para a autenticação |
| Senha |Obrigatório. A senha para autenticação |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação do Azure Active Directory pelo código

Por padrão, a autenticação do Azure AD que você ativa no Portal do Azure não fornece autorizações refinadas. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

Para restringir o acesso à API para seu aplicativo lógico pelo código, extraia o cabeçalho que tem o JWT (Token Web JSON). Verifique a identidade do chamador e rejeite as solicitações que não correspondem.

Indo um pouco além, para implementar totalmente essa autenticação em seu próprio código e não usar o Portal do Azure, saiba como [autenticar com o Active Directory local no aplicativo do Azure](../app-service-web/web-sites-authentication-authorization.md).

Para criar uma identidade do aplicativo para seu aplicativo lógico e usá-la para chamar a API, você deve seguir as etapas anteriores.

## <a name="next-steps"></a>Próximas etapas

* [Verificar o desempenho do aplicativo lógico com alertas e logs de diagnóstico](logic-apps-monitor-your-logic-apps.md)
