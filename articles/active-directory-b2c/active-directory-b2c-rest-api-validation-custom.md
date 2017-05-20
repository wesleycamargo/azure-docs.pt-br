---
title: "Azure AD B2C integra trocas de declarações da API REST como validação em Políticas Personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
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
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>Passo a passo: Integrar as trocas de declarações da API REST nos seus percursos do usuário do Azure AD B2C como validação à entrada do usuário

A **Identity Experience Framework** (IEF) subjacente ao Azure AD B2C permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário.  

No final deste passo a passo, você poderá criar percursos do usuário do Azure AD B2C que interajam com os serviços RESTful.

A IEF envia dados em declarações e recebe dados de volta em declarações.  A interação com a API pode ser criada como uma troca de declarações de API REST, como um perfil de validação, o que acontece dentro de uma etapa de orquestrações.

- Isso normalmente valida a entrada do usuário
- Se o valor do usuário for rejeitado, o usuário poderá tentar novamente ao inserir um valor válido com a oportunidade de retornar uma mensagem de erro para o usuário.

A interação também pode ser projetada como uma etapa de orquestração. Para saber mais, veja [Passo a passo: Integrar as trocas de declarações da API REST no seu percurso do usuário do Azure AD B2C como uma etapa de orquestração](active-directory-b2c-rest-api-step-custom.md).

No exemplo de perfil de validação, usaremos o percurso do usuário de Edição de Perfil no arquivo de starter pack ProfileEdit.xml.

Podemos verificar que o nome fornecido pelo usuário na edição de perfil não faz parte de uma lista excluída.

## <a name="prerequisites"></a>Pré-requisitos

- Uma configuração de locatário do Azure AD B2C para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual se irá interagir. Um site de demonstração [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) foi configurado com um serviço da API REST que será usado para este passo a passo.

## <a name="step-1---prepare-the-rest-api-function"></a>Etapa 1 - Preparar a função da API REST

> [!NOTE]
> A configuração de funções da API REST está fora do escopo deste artigo. [Aplicativos do Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um kit de ferramentas excelente para criar serviços RESTful na nuvem.

Nós criamos um Azure Function que recebe uma declaração que ele espera como "playerTag" e valida se essa declaração existe ou não. Você pode acessar o código completo de função do Azure no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```
if (requestContentAsJObject.playerTag == null)
    {
        return request.CreateResponse(HttpStatusCode.BadRequest);
    }

    var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

    if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
    {
        return request.CreateResponse<ResponseContent>(
            HttpStatusCode.Conflict,
            new ResponseContent
            {
                version = "1.0.0",
                status = (int) HttpStatusCode.Conflict,
                userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
            },
            new JsonMediaTypeFormatter(),
            "application/json");
    }

    return request.CreateResponse(HttpStatusCode.OK);
```

A declaração `userMessage` retornada pelo Azure Function é esperada pela estrutura de experiência de identidade e será apresentada como uma cadeia de caracteres para o usuário se a validação falhar, como quando um status de conflito 409 é retornado no exemplo acima.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Etapa 2 - Configurar a troca de declarações da API RESTful como um perfil técnico no arquivo TrustFrameworkExtensions.xml

Um perfil técnico é a configuração completa da troca desejada com o serviço RESTful. Abra o arquivo `TrustFrameworkExtensions.xml` e adicione o trecho XML abaixo ao elemento `<ClaimsProviders>`.

> [!NOTE]
> Considere o "Provedor Restful, versão 1.0.0.0" descrito abaixo como o protocolo da função que irá interagir com o serviço externo.  Você encontrará uma definição completa do esquema <!-- TODO: Link to RESTful Provider schema definition>-->

```
<ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">

                    <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
                    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                    <Metadata>
                        <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                        <Item Key="AuthenticationType">None</Item>
                        <Item Key="SendClaimsIn">Body</Item>
                    </Metadata>
                    <InputClaims>
                        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
                    </InputClaims>
                    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>


            <TechnicalProfile Id="SelfAsserted-ProfileUpdate">

                <ValidationTechnicalProfiles>       
                    <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />       
                </ValidationTechnicalProfiles>

            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
```

O elemento `InputClaims` define as declarações que serão enviadas pela IEE para o serviço REST. No exemplo acima, o conteúdo das declarações `givenName` será enviado para o serviço REST como `playerTag`. Neste exemplo, a IEE não espera declarações novamente e em vez disso, aguarda uma resposta do serviço REST e age com base nos códigos de status recebidos.

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>Etapa 3 - Incluir a troca de declarações do serviço RESTful no perfil técnico autodeclarado onde você deseja validar a entrada do usuário

O uso mais comum da etapa de validação é na interação com um usuário.  Todas as interações onde o usuário deve fornecer entrada são **Perfis Técnicos Autodeclarado**. Para este exemplo, adicionaremos essa validação ao perfil técnico (TP) **Self-Asserted-ProfileUpdate**.  Esse é o TP usado pelo arquivo de política de RP `Profile Edit`.

Para adicionar a troca de declarações ao Perfil Técnico Autodeclarado:

1. Abra o arquivo TrustFrameworkBase e procure `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Examine a configuração desse TP e observe como a troca com o usuário é definida como declarações que serão feitas sobre o usuário (declarações de entrada) e declarações esperadas do provedor autodeclarado (declarações de saída)
3. Procure `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, observe que esse perfil será invocado como a Etapa 6 da Orquestração do `<UserJourney Id="ProfileEdit">`

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>Etapa 4 - Carregar e testar o arquivo de política de RP de Edição de Perfil

1. Carregue a nova versão do arquivo `TrustframeworkExtensions`.
2. Use **Executar agora** para testar o arquivo de política de edição do perfil RP.
3. Teste a validação ao fornecer um dos nomes existentes (por exemplo: mcvinny) no campo **Nome**. Se tudo estiver configurado corretamente, você deverá receber uma mensagem informando ao usuário que o `player tag` já está sendo usado.

## <a name="next-steps"></a>Próximas etapas

[Como modificar a edição de perfil e o registro de usuário para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Passo a passo: Integrar as trocas de declarações da API REST no seu percurso do usuário do Azure AD B2C como uma etapa de orquestração](active-directory-b2c-rest-api-step-custom.md)

