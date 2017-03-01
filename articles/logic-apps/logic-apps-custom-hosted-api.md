---
title: "Chamar APIs personalizadas no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Chamar suas próprias APIs personalizadas hospedadas no Serviço de Aplicativo do Azure com Aplicativos Lógicos do Azure"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Chamar APIs personalizadas hospedadas no Serviço de Aplicativo do Azure com Aplicativos Lógicos do Azure

Embora os Aplicativos Lógicos do Azure ofereçam mais de 40 conectores para vários serviços, convém chamar a sua própria API personalizada que possa executar seu próprio código. O Serviço de Aplicativo do Azure fornece uma das maneiras mais fáceis e escalonáveis de hospedar sua própria API Web personalizada. Este artigo aborda como chamar a API Web hospedada em um aplicativo de API do Serviço de Aplicativo, aplicativo Web ou aplicativo móvel.
Saiba [como compilar APIs como um gatilho ou ação em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md).

## <a name="deploy-your-web-app"></a>Implantar o aplicativo Web

Primeiro, você precisa implantar sua API como um aplicativo Web no Serviço de Aplicativo do Azure. Saiba mais sobre [implantação básica quando você cria um aplicativo Web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md). Embora você possa chamar qualquer API a partir de um aplicativo lógico, para ter a melhor experiência, é recomendável adicionar os metadados Swagger para integrar facilmente com as ações dos aplicativos lógicos. Saiba mais sobre [como adicionar metadados Swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Configurações da API

Para o Designer de Aplicativos Lógicos analisar o Swagger, você deve habilitar o CORS e definir as propriedades de Definição de API para seu aplicativo Web. 

1.    No Portal do Azure, selecione seu aplicativo Web.
2.    Na folha que é aberta, localize **API** e selecione **Definição de API**. Defina o **Local da definição de API** como a URL de seu arquivo swagger.json.

    Geralmente, essa URL é https://{nome}.azurewebsites.net/swagger/docs/v1).

3.    Para permitir solicitações do Designer de Aplicativo Lógico, em **API**, selecione **CORS** e defina uma política de CORS como '*'.

## <a name="call-into-your-custom-api"></a>Chamar sua API personalizada

Se você configurou as propriedades do CORS e da Definição da API, deverá ser capaz de adicionar facilmente as ações da API Personalizada ao seu fluxo de trabalho no portal dos Aplicativos lógicos. No Designer de Aplicativos Lógicos, você pode navegar pelos seus sites de assinatura para listar os sites com uma URL de swagger definida. Você também pode apontar para um Swagger e listar as ações e entradas disponíveis usando a ação HTTP + Swagger. Por fim, você sempre pode criar uma solicitação usando a ação de HTTP para chamar qualquer API, mesmo aquelas que não têm ou expõem um documento do Swagger.

Há algumas maneiras diferentes de proteger sua API:

*    Não é necessário alterar o código. Use o Azure Active Directory para proteger sua API sem exigir alterações ou reimplantação de código.
*    Na código de sua API, aplique a Autenticação Básica, Autenticação de Certificado ou autenticação do Azure Active Directory.

## <a name="secure-calls-to-your-api-without-changing-code"></a>Proteger chamadas para a API sem alterar o código

Nesta seção, você criará dois aplicativos do Azure Active Directory - um para o seu aplicativo lógico e outro para seu aplicativo Web. Autentique as chamadas para seu aplicativo Web usando a entidade de serviço (id e segredo do cliente) associada ao aplicativo do Azure Active Directory de seu aplicativo lógico. Finalmente, inclua os IDs de aplicativo em sua definição da aplicativo lógico.

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurar uma identidade de aplicativo para seu aplicativo lógico

Seu aplicativo lógico usa essa identidade de aplicativo para autenticar no Azure Active Directory. Você só precisa configurar essa identidade para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos lógicos, embora também possa criar identidades exclusivas por aplicativo lógico. Configure essas identidades no Portal do Azure ou use o PowerShell.

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Criar a identidade do aplicativo no Portal Clássico do Azure

1. No Portal Clássico do Azure, acesse [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). 
2. Selecione o diretório que você usa para seu aplicativo Web.
3. Escolha a guia **Aplicativos**. Na parte inferior da página na barra de comandos, escolha **Adicionar**.
5. Dê um nome à sua identidade de aplicativo e clique na seta para avançar.
6. Em **Propriedades do aplicativo**, coloque uma cadeia de caracteres exclusiva formatada como domínio e clique na marca de seleção.
7. Escolha a guia **Configurar** . Acesse **ID do Cliente** e copie a ID do cliente para uso em seu aplicativo lógico.
8. Em **Chaves**, abra a lista **Selecionar duração** e selecione o tempo de duração de sua chave.
9. Na parte inferior da página, clique em **Salvar**. Talvez seja necessário aguardar alguns segundos.
10. Copie a chave que aparece agora em **Chaves**, para que você possa usar essa chave em seu aplicativo lógico.

#### <a name="create-the-application-identity-using-powershell"></a>Criar a identidade do aplicativo usando o PowerShell

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Não deixe de copiar a **ID do locatário**, a **ID do aplicativo** e a senha usada.

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>Parte 2: Proteger seu aplicativo Web com uma identidade de aplicativo do Azure Active Directory

Se seu aplicativo Web já estiver implantado, habilite a autorização no Portal do Azure. Caso contrário, você pode tornar a habilitação de autorização parte da sua implantação do Azure Resource Manager.

#### <a name="enable-authorization-in-the-azure-portal"></a>Habilitar autorização no Portal do Azure

1. Localize e selecione o seu aplicativo Web. Em **Configurações**, escolha **Autenticação/Autorização**.
2. Em **Autenticação do Serviço de Aplicativo**, **Ative** a autenticação e escolha **Salvar**.

Neste ponto, um aplicativo é criado automaticamente para você. Você precisará da ID do cliente desse aplicativo para a Parte 3, então execute estas etapas:

1. No Portal Clássico do Azure, acesse [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).
2.    Selecione seu diretório.
3. Na caixa de pesquisa, localize seu aplicativo.
4. Na lista, selecione seu aplicativo.
5. Escolha a guia **Configurar** onde você verá a **ID do Cliente**.

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Implantar seu aplicativo Web usando um modelo do Azure Resource Manager

Primeiro, você deve criar um aplicativo para seu aplicativo Web que é diferente do aplicativo que você usa para seu aplicativo lógico. Comece seguindo as etapas acima na Parte 1, mas, para **HomePage** e **IdentifierUris**, use a https://**URL** real do seu aplicativo Web.

> [!NOTE]
> Quando você cria o aplicativo para seu aplicativo Web, use o [Portal Clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). O cmdlet do PowerShell não configura as permissões necessárias para conectar os usuários em um site.

Quando você tiver a ID de cliente e a ID de locatário, inclua esta parte como um recurso secundário do aplicativo Web em seu modelo de implantação:

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
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Para executar automaticamente uma implantação que implanta ao mesmo tempo um aplicativo Web em branco e um aplicativo lógico usando o Azure Active Directory, clique em **Implantar no Azure**:

[![Implantar no Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para ver o modelo completo, consulte [Chamadas do Aplicativo lógico em uma API personalizada hospedada no Serviço de Aplicativo e protegida pelo Azure Active Directory](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Preencher a seção Autorização no aplicativo lógico

Na seção **Autorização** da ação **HTTP**:

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrição |
| ------- | ----------- |
| type |Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. |
| locatário |O identificador do locatário usado para identificar o locatário do AD. |
| audiência |Obrigatório. O recurso a que você está se conectando. |
| clientID |O identificador de cliente para o aplicativo do AD do Azure. |
| segredo |Obrigatório. Segredo do cliente que está solicitando o token. |

O modelo anterior já tem essa seção de autorização definida, mas se você estiver criando o aplicativo lógico diretamente, precisará incluir a seção de autorização completa.

## <a name="secure-your-api-in-code"></a>Proteger sua API em código

### <a name="certificate-authentication"></a>Autenticação de certificado

Você pode usar certificados de cliente para validar solicitações de entrada para seu aplicativo Web. Consulte [Como configurar autenticação mútua TLS para o aplicativo Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para configurar seu código.

Na seção **Autorização**, você deve incluir: 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| Elemento | Descrição |
| ------- | ----------- |
| type |Obrigatório. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`. |
| pfx |Obrigatório. Conteúdo codificado na Base64 do arquivo PFX. |
| Senha |Obrigatório. Senha para acessar o arquivo PFX. |

### <a name="basic-authentication"></a>Autenticação básica

Para validar solicitações de entrada, você pode usar a autenticação básica, por exemplo, nome de usuário e senha. A autenticação básica é um padrão comum, e você pode usar essa autenticação em qualquer linguagem usada para compilar seu aplicativo.

Na seção **Autorização**, você deve incluir:

`{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrição |
| --- | --- |
| type |Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`. |
| Nome de Usuário |Obrigatório. Nome de usuário para autenticação. |
| Senha |Obrigatório. Senha para autenticação. |

### <a name="handle-azure-active-directory-authentication-in-code"></a>Lidar com a autenticação do Azure Active Directory no código

Por padrão, a autenticação do Azure Active Directory que você habilita no Portal do Azure não fornece autorizações refinadas. Por exemplo, essa autenticação não bloqueia sua API a um usuário ou aplicativo específico, mas somente a um locatário específico.

Para restringir sua API ao seu aplicativo lógico, por exemplo, no código, extraia o cabeçalho que tem o JWT. Verifique a identidade do chamador e rejeite as solicitações que não correspondem.

Indo um pouco além, para implementar totalmente essa autenticação em seu próprio código, e não usar o recurso do Portal do Azure, consulte [Autenticar com o Active Directory local em seu aplicativo do Azure](../app-service-web/web-sites-authentication-authorization.md).
Para criar uma identidade de aplicativo para seu aplicativo Lógico e usá-la para chamar a API, execute as etapas anteriores.
