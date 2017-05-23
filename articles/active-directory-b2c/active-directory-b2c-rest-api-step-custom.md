---
title: "Passo a passo: A troca de declarações da API REST como uma etapa em suas Políticas Personalizadas de B2C | Microsoft Docs"
description: "Um tópico sobre a integração das políticas personalizadas do Azure Active Directory B2C com a API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27d066cc6f985565a765989837e4a2744387a628
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-an-orchestration-step"></a>Passo a passo: Integrar as trocas de declarações da API REST no seu percurso do usuário do Azure AD B2C como uma etapa de orquestração

A **Identity Experience Framework** (IEF) subjacente ao Azure AD B2C permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário.  

No final deste passo a passo, você poderá criar percursos do usuário do Azure AD B2C que interajam com os serviços RESTful.

A IEF envia dados em declarações e recebe dados de volta em declarações.  A troca de declarações da API REST pode ser criada como uma etapa de orquestrações.

- Isso pode disparar uma ação externa - por exemplo, ele pode registrar um evento em log em um banco de dados externo.
- Isso também pode ser usado para buscar um valor e subsequentemente armazená-lo no banco de dados do usuário.
- As declarações recebidas poderão ser usadas posteriormente para alterar o fluxo de execução.

A interação também pode ser projetada como um perfil de validação. Para saber mais sobre isso, veja [Passo a passo: Integrar as trocas de declarações da API REST no nos seus percursos do usuário do Azure AD B2C como validação à entrada do usuário](active-directory-b2c-rest-api-validation-custom.md).

O cenário é que quando um usuário executa uma edição de perfil, gostaríamos de pesquisar o usuário em um sistema externo, obter a cidade em que o usuário está registrado e retornar esse atributo como uma declaração de volta para o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma configuração de locatário do Azure AD B2C para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual interagir - neste passo a passo, usa um WebHook muito simples de aplicativos de função do Azure como um exemplo
- **Recomendado**: conclua o [passo a passo da troca de declarações da API REST como uma etapa de validação](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1---prepare-the-rest-api-function"></a>Etapa 1 - Preparar a função da API REST

> [!NOTE]
> A configuração de funções da API REST está fora do escopo deste artigo. [Aplicativos do Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um kit de ferramentas excelente para criar serviços RESTful na nuvem.

Definimos uma função do Azure que recebe uma declaração `email` e simplesmente retorna a declaração `city` com o valor atribuído de `Redmond`. A função do Azure de exemplo está aqui: [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples)

A declaração `userMessage` retornada pela função do Azure é opcional nesse contexto e será ignorada pela IEF.  Potencialmente, ele pode ser usado como uma mensagem passada para o aplicativo e apresentado ao usuário mais tarde.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Os **Aplicativos do Azure Function** facilitam a obtenção da função URL, que inclui o identificador da função específica.  Nesse caso, a URL é: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==, e você pode usá-la para fins de testes.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Etapa 2 - Configurar a troca de declarações da API RESTful como um perfil técnico no arquivo TrustFrameworkExtensions.xml

Um perfil técnico é a configuração completa da troca desejada com o serviço RESTful. Abra o arquivo `TrustFrameworkExtensions.xml` e adicione o trecho XML abaixo ao elemento `<ClaimsProvider>`.

> [!NOTE]
> Considere o "Provedor Restful, versão 1.0.0.0" descrito abaixo como o protocolo da função que irá interagir com o serviço externo.  Você encontrará uma definição completa do esquema <!-- TODO: Link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O elemento `<InputClaims>` define as declarações que serão enviadas pela IEF para o serviço REST. No exemplo acima, o conteúdo das declarações `givenName` será enviado para o serviço REST como a declaração `email`.  

O elemento `<OutputClaims>` define as declarações que a IEF espera do serviço REST. Independentemente do número de declarações recebidas, a IEF usará apenas aquelas identificadas aqui. Neste exemplo, uma declaração recebida como `city` será mapeada para uma declaração `city` da IEF.

## <a name="step-3---add-a-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Etapa 3 - Adicionar uma nova declaração `city` ao esquema do arquivo TrustFrameworkExtensions.xml

A declaração `city` não está definida em qualquer outro lugar no nosso esquema. Por isso adicionaremos uma definição ao elemento `<BuildingBlocks>`. que pode ser encontrado no começo do arquivo `TrustFrameworkExtensions.xml`.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4---include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-your-trustframeworkextensionsxml"></a>Etapa 4 - Incluir a troca de declarações do serviço REST como uma etapa de orquestração em seu percurso do usuário de edição de perfil no seu TrustFrameworkExtensions.xml

Decidimos adicionar a etapa de percurso do usuário de edição de perfil após a autenticação do usuário (Etapas de Orquestração 1 a 4 – veja abaixo) e depois que ele tenha fornecido as informações de perfil atualizado (Etapa 5).

> [!NOTE]
> Há muitos casos de uso em que a Chamada de API REST pode ser usada como uma Etapa de Orquestração.  Como uma Etapa de Orquestração, ele pode ser usado como uma atualização para um sistema externo quando um usuário for concluída com êxito uma tarefa, como o primeiro registro, ou atualizar o perfil para manter as informações sincronizadas.  Nesse caso, ele é usado para aumentar as informações fornecidas para o aplicativo depois da edição do perfil.

Copie o código XML do percurso do usuário de edição de perfil do arquivo `TrustFrameworkBase.xml` para seu arquivo `TrustFrameworkExtensions.xml` no elemento `<UserJourneys>` e faça a modificação da etapa 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Se a ordem não corresponder à sua versão, verifique se você a insira como a etapa antes do Tipo ClaimsExchange `SendClaims`

O XML UserJourney final deve ter esta aparência:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the jwt token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5---add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Etapa 5 - Adicionar a declaração "city" ao seu arquivo de política de Terceira Parte Confiável para que a declaração seja enviada para o seu aplicativo

Para fazer isso, edite o arquivo RP do `ProfileEdit.xml` e modifique o elemento `<TechnicalProfile Id="PolicyProfile">` para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="city" />`.

Após a adição da nova declaração, o TechnicalProfile terá esta aparência:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6---upload-your-changes-and-test"></a>Etapa 6 - Carregar suas alterações e testar

Você estará substituindo as versões existentes da política.

1.    (OPCIONAL) Salve a versão existente (ao baixá-la) do seu arquivo de extensões antes de continuar.  É recomendável que você não carregue várias versões do arquivo de extensões para reduzir a complexidade inicial.
2.    (OPCIONAL) você pode renomear a nova versão da PolicyId do arquivo de edição de política ao alterar PolicyId="B2C_1A_TrustFrameworkProfileEdit"
3.    Carregar o arquivo de extensões
4.    Carregar o arquivo de Terceira Parte Confiável de edição de política
5.    Use **Executar Agora** para testar a política.  Examine o token retornado pelo IEF para o aplicativo.

Se tudo estiver configurado corretamente, o token incluirá a nova declaração `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximas etapas

[Usar uma API REST como uma etapa de validação](active-directory-b2c-rest-api-validation-custom.md)

[Como modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

