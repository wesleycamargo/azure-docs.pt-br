---
title: Como rastrear o comportamento do usuário usando eventos no Application Insights do Azure Active Directory B2C | Microsoft Docs
description: Guia passo a passo para habilitar logs de eventos no Application Insights dos percursos do usuário do Azure Active Directory B2C utilizando políticas personalizadas - VERSÃO PRÉVIA
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Rastrear o comportamento do usuário nos percursos do Azure Active Directory B2C utilizando o Application Insights

O Azure Active Directory B2C funciona bem com o Azure Application Insights.  Eles fornecem logs de eventos personalizados e detalhados para os percursos do usuário personalizados criados.  Este guia mostra uma introdução para que você possa: 
* obter insights sobre o comportamento do usuário
* solucionar problemas de suas próprias políticas em desenvolvimento ou em produção
* medir o desempenho
* criar notificações do Application Insights

## <a name="how-it-works"></a>Como ele funciona
Um novo provedor foi adicionado à Estrutura de Experiência de Identidade do Azure Active Directory B2C: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Ele envia dados de eventos diretamente para o Application Insights usando a Chave de Instrumentação fornecida para o Azure Active Directory B2C.  Um perfil técnico usa esse provedor para definir um evento do B2C.  O perfil especifica o nome do evento, as declarações que serão registradas e a chave de instrumentação.  Para postar um evento, o perfil técnico é adicionado como `orchestration step` ou como `validation technical profile` em um percurso do usuário personalizado.  Os eventos podem ser unificados pelo Application Insights usando uma ID de correlação para registrar uma sessão do usuário.  O Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas analíticas, de exportação e visualização.



## <a name="prerequisites"></a>pré-requisitos
Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).  Este guia assume que o pacote inicial da política personalizada está sendo utilizado.  No entanto, não é necessário.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Etapa 1. Criar um recurso do Application Insights e obter a Chave de Instrumentação

O Application Insights é uma ferramenta avançada. Ao utilizá-lo com o Azure Active Directory B2C, o único requisito é criar um recurso e obter uma Chave de Instrumentação.  O Application Insights deve ser criado [Portal do Azure.](https://portal.azure.com)

[Documentação completa do Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Clique `+ Create a resource` no Portal do Azure, dentro do seu locatário de assinatura.  Esse locatário não é o seu locatário do Azure Active Directory B2C.  
2. Pesquise e selecione `Application Insights`  
3. Crie um recurso de `Application Type` `ASP.NET web application` sob uma assinatura de sua preferência.
4. Após criado, abra o recurso do Application Insights e observe `Instrumentation Key` 

![Visão geral do Application Insights e Chave de Instrumentação](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Etapa 2. Adicionar novas definições de ClaimType ao arquivo de extensão da Estrutura Confiável

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Abra o arquivo de extensão do Starter Pack e adicione os elementos a seguir no nó `<BuildingBlocks>`.  As extensões de nome de arquivo são tipicamente `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

Perfis técnicos podem ser considerados funções na Estrutura de Experiência de Identidade do Azure Active Directory B2C.  Cinco perfis técnicos são definidos neste exemplo para abrir uma sessão e postar eventos.

| Perfil técnico       | Tarefa |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | conjunto comum de parâmetros a serem incluídos em todos os Perfis Técnicos do Azure-Insights     | 
| JourneyContextForInsights   | abre a sessão no Application Insights e envia uma ID de correlação |
| AzureInsights-SignInRequest     | cria um Evento "SignIn" com um conjunto de declarações quando uma solicitação de entrada for recebida      | 
| AzureInsights-UserSignup | cria um Evento chamado "UserSignup" quando a opção de inscrição for disparada pelo usuário em um percurso de entrada/inscrição     | 
| AzureInsights-SignInComplete | registra a conclusão com êxito de uma autenticação quando um token for enviado ao aplicativo de terceira parte confiável     | 

Adicione os perfis ao arquivo de extensão do Starter Pack, adicionando esses elementos ao nó `<ClaimsProviders>`.  As extensões de nome de arquivo são tipicamente `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Altere `Instrumentation Key` no perfil técnico `ApplicationInsights-Common` para o GUID fornecido pelo recurso do Application Insights.

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Etapa 4. Adicionar os perfis técnicos para Application-Insights como etapas de orquestração em um percurso do usuário existente.

Chamar `JournyeContextForInsights` como etapa de orquestração 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Chame `Azure-Insights-SignInRequest` como etapa de orquestração 2 para rastrear se uma solicitação de inscrição/entrada foi recebida.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Imediatamente **antes** da etapa de orquestração `SendClaims`, adicione uma nova etapa que chama `Azure-Insights-UserSignup`. É disparada quando o botão de inscrição for clicado em um percurso de entrada/inscrição.

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

Imediatamente após a etapa de orquestração `SendClaims` chame `Azure-Insights-SignInComplete`.   Essa etapa refletirá um percurso concluído com êxito.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Etapa 5. Fazer upload do arquivo de extensões modificado, executar a política e exibir eventos no Application Insights

Salve e faça upload do novo arquivo de extensões de estrutura confiável.  Em seguida, chame a política de Terceira Parte Confiável do aplicativo ou utilize `Run Now` na interface do Azure Active Directory B2C.  Em segundos, os eventos estarão disponíveis no Application Insights.

1. Abra o recurso do Application Insights no locatário do Azure Active Directory.
2. Clique em `Events` no submenu `USAGE`.
3. Defina `During` para `Last hour` e `By` para `3 minutes`.  Talvez seja necessário clicar em `Refresh` para exibir os resultados

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>PRÓXIMAS ETAPAS

Adicione tipos de declarações e eventos adicionais ao percurso do usuário para ajustar às suas necessidades.  Aqui está uma lista de possíveis declarações usando resolvedores de declarações adicionais.

### <a name="culture-specific-claims"></a>Declarações específicas de cultura

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Declarações específicas de política

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>Declarações específicas de OpenIDConnect

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parâmetros não protocolo incluídos nas solicitações OAuth2 e OIDC

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Qualquer nome de parâmetro incluído como parte de uma solicitação OIDC ou OAuth2 pode ser mapeado para uma declaração no percurso do usuário.  Portanto, pode ser registrado no evento. Por exemplo, a solicitação do aplicativo pode incluir um parâmetro da cadeia de caracteres de consulta com um nome de `app_session`, `loyalty_number` ou `any_string`.

Solicitação de exemplo do aplicativo:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
As declarações podem ser adicionadas, adicionando um elemento de Declaração de Entrada ao evento do Application Insights utilizando:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
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



