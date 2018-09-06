---
title: Rastrear o comportamento do usuário usando eventos no Application Insights do Azure Active Directory B2C | Microsoft Docs
description: Guia passo a passo para habilitar logs de eventos no Application Insights dos percursos do usuário do Azure Active Directory B2C utilizando políticas personalizadas (versão prévia)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c77feed3b86358c74f741b53aa03ecb454dc9a62
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337095"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Rastrear o comportamento do usuário nos percursos do Azure Active Directory B2C utilizando o Application Insights

O Azure Active Directory B2C (Azure AD B2C) funciona bem com o Azure Application Insights. Eles fornecem logs de eventos personalizados e detalhados para os percursos do usuário criados de forma personalizada. Este artigo mostra uma introdução para que você possa:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.

> [!NOTE]
> Esse recurso está em visualização.

## <a name="how-it-works"></a>Como ele funciona

A Estrutura de Experiência de Identidade no Azure Active Directory B2C agora inclui o provedor `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Ele envia dados de eventos diretamente para o Application Insights usando a chave de instrumentação fornecida para o Azure Active Directory B2C.

Um perfil técnico usa esse provedor para definir um evento do B2C.  O perfil especifica o nome do evento, as declarações que serão registradas e a chave de instrumentação.  Para postar um evento, o perfil técnico é adicionado como `orchestration step` ou como `validation technical profile` em um percurso do usuário personalizado.

O Application Insights pode unificar os eventos usando uma ID de correlação para registrar uma sessão do usuário. O Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas analíticas, de exportação e visualização.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Este artigo pressupõe que você esteja usando o pacote de inicializador de política personalizada. Mas o pacote de inicializador não é necessário.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Etapa 1. Criar um recurso do Application Insights e obter a chave de instrumentação

Ao usar o Application Insights com o Azure Active Directory B2C, o único requisito é criar um recurso e obter uma chave de instrumentação. Você cria um recurso no [Portal do Azure.](https://portal.azure.com)

1. No Portal do Azure, dentro do seu locatário de assinatura, selecione **+ Criar um recurso**. Esse locatário não é o seu locatário do Azure Active Directory B2C.  
2. Pesquise e selecione **Application Insights**.  
3. Criar um recurso que usa **aplicativo Web ASP.NET** como **tipo de aplicativo**, em uma assinatura de sua preferência.
4. Depois de criar o recurso do Application Insights, abra-o e anote a chave de instrumentação.

![Visão geral do Application Insights e Chave de Instrumentação](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Etapa 2. Adicionar novas definições de ClaimType ao arquivo de extensão da estrutura confiável

Abra o arquivo de extensão do Starter Pack e adicione os elementos a seguir no nó `<BuildingBlocks>`. O nome do arquivo normalmente é `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Etapa 3. Adicionar novos perfis técnicos que utilizam o provedor do Application Insights

Perfis técnicos podem ser considerados funções na Estrutura de Experiência de Identidade do Azure Active Directory B2C. Este exemplo define cinco perfis técnicos para abrir uma sessão e postar eventos:

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AzureInsights-Common | Cria umconjunto comum de parâmetros a serem incluídos em todos os Perfis Técnicos do Azure-Insights | 
| JourneyContextForInsights | Abre a sessão no Application Insights e envia uma ID de correlação |
| AzureInsights-SignInRequest | Cria um Evento `SignIn` com um conjunto de declarações quando uma solicitação de entrada tiver sido recebida | 
| AzureInsights-UserSignup | Cria um evento UserSignup quando o usuário aciona a opção de entrada em um percurso de inscrição/entrada | 
| AzureInsights-SignInComplete | Registra a conclusão com êxito de uma autenticação quando um token for enviado ao aplicativo de terceira parte confiável | 

Adicione os perfis ao arquivo de extensão do Starter Pack, adicionando esses elementos ao nó `<ClaimsProviders>`.  O nome do arquivo normalmente é `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Altere a chave de instrumentação no perfil técnico `ApplicationInsights-Common` para o GUID que seu recurso do Application Insights fornece.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Etapa 4. Adicionar os perfis técnicos para Application Insights como etapas de orquestração em um percurso do usuário existente

Chame `JournyeContextForInsights` como etapa de orquestração 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Chame `Azure-Insights-SignInRequest` como etapa de orquestração 2 para rastrear se uma solicitação de inscrição/entrada foi recebida:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* da etapa de orquestração `SendClaims`, adicione uma nova etapa que chama `Azure-Insights-UserSignup`. É disparado quando o usuário seleciona o botão de inscrição em um percurso de inscrição/entrada.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Imediatamente após a etapa de orquestração `SendClaims` chame `Azure-Insights-SignInComplete`. Essa etapa reflete um percurso concluído com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Etapa 5. Fazer upload do arquivo de extensões modificado, executar a política e exibir eventos no Application Insights

Salve e faça upload do novo arquivo de extensão de estrutura confiável. Em seguida, chame a política de Terceira Parte Confiável do aplicativo ou utilize `Run Now` na interface do Azure Active Directory B2C. Em segundos, os eventos estarão disponíveis no Application Insights.

1. Abra o recurso do **Application Insights** no locatário do Azure Active Directory.
2. Selecionar **Eventos** > **de Uso**.
3. Defina **Durante** para **Última hora** e **Por** para **3 minutos**.  Talvez você precise selecionar **Atualizar** para exibir os resultados.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Próximas etapas

Adicione tipos de declarações e eventos ao percurso do usuário para ajustar às suas necessidades. Aqui está uma lista de possíveis declarações usando resolvedores de declarações adicionais

### <a name="culture-specific-claims"></a>Declarações específicas de cultura

```xml
Referenced using: {Culture:One of the property names below}
```

| Declaração | Definição | Exemplo |
| ----- | -----------| --------|
| LanguageName | As duas letras do código ISO para a linguagem | en |
| RegionName | As duas letras do código ISO para a região | EUA |
| RFC5646 | O código da linguagem RFC5646 | pt-BR |
| LCID   | O código da linguagem LCID | 1033 |

### <a name="policy-specific-claims"></a>Declarações específicas de política

```xml
Referenced using {Policy:One of the property names below}
```

| Declaração | Definição | Exemplo |
| ----- | -----------| --------|
| TrustFrameworkTenantId | A ID do locatário TrustFrameworkTenantId | N/D |
| RelyingPartyTenantId | A ID do locatário da parte confiável | N/D |
| PolicyId | A ID da política da política | N/D |
| TenantObjectId | A ID de objeto de locatário da política | N/D |

### <a name="openid-connect-specific-claims"></a>Declarações específicas do OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Declaração | Parâmetro OpenIdConnect | Exemplo |
| ----- | ----------------------- | --------|
| Prompt | prompt | N/D |
| LoginHint |  login_hint | N/D |
| LoginHint | domain_hint | N/D |
|  MaxAge | max_age | N/D |
| ClientId | client_id | N/D |
| Nome de Usuário | login_hint | N/D |
|  Recurso | recurso| N/D |
| AuthenticationContextReferences | acr_values | N/D |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parâmetros não protocolo incluídos nas solicitações OAuth2 e OIDC

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Qualquer nome de parâmetro incluído como parte de uma solicitação OIDC ou OAuth2 pode ser mapeado para uma declaração no percurso do usuário. Você pode, então, registrá-lo no evento. Por exemplo, a solicitação do aplicativo pode incluir um parâmetro da cadeia de caracteres de consulta com um nome de `app_session`, `loyalty_number` ou `any_string`.

Aqui está uma solicitação de exemplo do aplicativo:

```
https://sampletenant.b2clogin.com/tfp/sampletenant.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
As declarações podem ser adicionadas, adicionando um elemento `Input Claim` ao evento do Application Insights: Propriedades de um evento são adicionadas por meio da sintaxe de {property:NAME}, onde NOME é propriedade que está sendo adicionada ao evento. Por exemplo: 

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Outras declarações do sistema

Algumas declarações do sistema devem ser adicionadas ao recipiente de declarações, antes de estarem disponíveis para registro como eventos. O perfil técnico `SimpleUJContext` deverá ser chamado como uma etapa de orquestração ou um perfil técnico de validação, antes que essas declarações estejam disponíveis.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


