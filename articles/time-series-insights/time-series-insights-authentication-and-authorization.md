---
title: Como autenticar e autorizar pela API no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472516"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este artigo explica como configurar a autenticação e autorização usados em um aplicativo personalizado que chama a API do Azure Time Series Insights.

> [!TIP]
> Leia sobre [concedendo acesso a dados](./time-series-insights-data-access.md) ao ambiente do Time Series Insights no Azure Active Directory.

## <a name="service-principal"></a>Entidade de serviço

Isso e as seções a seguir descrevem como configurar um aplicativo para acessar a API do Time Series Insights em nome do aplicativo. O aplicativo pode consultar ou publicar dados de referência no ambiente do Time Series Insights com credenciais do aplicativo em vez de credenciais do usuário.

## <a name="best-practices"></a>Práticas recomendadas

Quando você tiver um aplicativo que precisa acessar o Time Series Insights:

1. Configure um aplicativo do Azure Active Directory.
1. [Atribuir políticas de acesso de dados](./time-series-insights-data-access.md) no ambiente do Time Series Insights.

Usar o aplicativo em vez de suas credenciais de usuário é desejável desde:

* Você pode atribuir permissões para a identidade do aplicativo que são diferentes das suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo apenas leia dados em um ambiente de análises de séries temporais específico.
* Você não precisa alterar as credenciais do aplicativo, se alterar suas responsabilidades.
* Você pode usar um certificado ou uma chave de aplicativo para automatizar a autenticação ao executar um script autônomo.

As seções a seguir demonstram como executar essas etapas no portal do Azure. O artigo se concentra em um aplicativo de único locatário em que o aplicativo destina-se para ser executado em uma única organização. Normalmente, você usará aplicativos de locatário único para aplicativos de linha de negócios que são executados em sua organização.

## <a name="set-up-summary"></a>Configurar o resumo

O fluxo de instalação consiste em três etapas:

1. Criar um aplicativo no Azure Active Directory.
1. Autorizar esse aplicativo para acessar o ambiente de análises de séries temporais.
1. Use a ID do aplicativo e a chave para adquirir um token do `https://api.timeseries.azure.com/`. O token, em seguida, pode ser usado para chamar a API de análises de séries temporais.

## <a name="detailed-setup"></a>Detalhadas de instalação

1. No portal do Azure, selecione **Azure Active Directory** > **Registros do aplicativo** > **Novo registro de aplicativo**.

   [![Novo registro de aplicativo no Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Nomeie o aplicativo, selecione o tipo a ser assinar**aplicativo Web / API**, selecione qualquer URI válido para **URL de logon**e clique em **Criar**.

   [![Criar o aplicativo no Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Selecione seu aplicativo recém-criado e copie seu ID de aplicativo para o seu editor de texto favorito.

   [![Copie a ID do aplicativo](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Selecione **Chaves**, insira o nome da chave, selecione a expiração e clique em **Salvar**.

   [![Selecione as chaves de aplicativo](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Insira o nome da chave e a expiração e clique em Salvar](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Copie a chave para o seu editor de texto favorito.

   [![Copie a chave de aplicativo](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Para o ambiente de análises de séries temporais, selecione **Políticas de acesso de dados** e clique em **Adicionar**.

   [![Adicionar nova política de acesso de dados para o ambiente do Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. No **Selecionar usuário** diálogo caixa, cole o nome do aplicativo (da **etapa 2**) ou ID do aplicativo (da **etapa 3**).

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função (**Reader** para consultar os dados, **Colaborador** para consultar dados e alterar dados de referência) e clique em **Okey**.

   [![Escolher a leitor ou colaborador na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvar a política clicando **Okey**.

1. Use a ID do aplicativo (de **etapa 3**) e a chave de aplicativo (da **etapa 5**) para adquirir o token em nome do aplicativo. O token, em seguida, pode ser passado ao `Authorization` cabeçalho quando o aplicativo chama a API de análises de séries temporais.

    Se você estiver usando C#, você pode usar o código a seguir para adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [Consultar dados usando o C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Use o **ID do aplicativo** e **chave** em seu aplicativo para autenticar com o Azure Time Series Insight.

## <a name="next-steps"></a>Próximas etapas

- Para código de exemplo que chama a API de análises de séries temporais, consulte [Consultar dados usando o C#](time-series-insights-query-data-csharp.md).

- Para obter informações de referência da API, confira [Referência da API de Consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
