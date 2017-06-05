---
title: "Azure Active Directory B2C: Como adicionar seus próprios atributos a políticas personalizadas e usar na edição de perfil | Microsoft Docs"
description: "Um passo a passo sobre como usar propriedades de extensão, atributos personalizados e incluí-los na interface de usuário"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 83748140c7b92b95a648ae3ecf78f22e2393780b
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017

---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Como criar e usar atributos personalizados em uma política e edição de perfil personalizado.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você criará um atributo personalizado no seu diretório do Azure AD B2C e usará esse novo atributo como uma declaração personalizada de percurso do usuário de edição de perfil.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Use atributos personalizados para coletar informações sobre seus clientes no Azure Active Directory B2C usando políticas personalizadas
O diretório do Azure Active Directory (Azure AD) B2C é fornecido com um conjunto interno de atributos: Nome, Sobrenome, Cidade e CEP, entre outros atributos.  Geralmente, você precisará criar seus próprios atributos.  Por exemplo:
* Um aplicativo voltado para o cliente precisa manter um atributo, como "LoyaltyNumber".
* Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como "uniqueUserGUID".
* Um percurso do usuário personalizado precisa manter o estado do usuário, como "migrationStatus".

Com o Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

[!NOTE]
Nós nos referimos a um atributo personalizado ou uma propriedade de extensão como um recurso do diretório do Azure AD B2C.  Propriedades de extensão estendem o esquema dos objetos de usuário no diretório.  Para usar um atributo personalizado como uma declaração personalizada em uma política, defina-o na política no `ClaimsSchema`.

[!NOTE]
As propriedades de extensão só podem ser registradas em um objeto do aplicativo, ainda que contenham dados de um usuário. A propriedade é anexada ao aplicativo. O objeto do aplicativo precisa obter acesso de gravação para registrar uma propriedade de extensão. É possível gravar 100 propriedades de extensão (entre TODOS os tipos e TODOS os aplicativos) em um único objeto. As propriedades de extensão são adicionadas para o tipo de diretório de destino e tornam-se imediatamente acessíveis no locatário de diretório do Azure AD B2C.
Se o aplicativo for excluído, tanto as propriedades de extensão quanto os dados contidos nelas para todos os usuários serão removidos. Se uma propriedade de extensão for excluída pelo aplicativo, ela será removida no objeto do diretório de destino e todos os dados contidos nela são removidos.

[!NOTE]
As propriedades de extensão existem apenas no contexto de um aplicativo registrado no locatário. O id de objeto do aplicativo deve ser incluído no TechnicalProfile que o usa

[!NOTE]
O diretório do Azure AD B2C geralmente inclui um aplicativo de API da Web chamado `b2c-extensions-app`.  Este aplicativo é usado principalmente pelas políticas internas b2c para as declarações personalizadas criadas por meio do portal do Azure.  Recomenda-se que apenas usuários avançados usem este aplicativo para registrar extensões para as políticas personalizadas b2c.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Criação de um aplicativo novo para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue até o [portal do Azure](https://portal.azure.com) e entre com as credenciais administrativas do diretório do B2C que deseja configurar.
1. Clique em `Azure Active Directory` no menu de navegação esquerdo. Talvez seja necessário localizá-lo, para isso, selecione Mais serviços>.
1. Selecione `App registrations` e clique em `New application registration`
1. Forneça as seguintes entradas recomendadas:
  * Especifique um nome para o aplicativo web: `WebApp-GraphAPI-DirectoryExtensions`
  * Tipo de aplicativo: API/Aplicativo Web
  * Logon URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Selecione `Create`. A conclusão bem-sucedida aparece no`notifications`
1. Selecione o aplicativo web criado recentemente: `WebApp-GraphAPI-DirectoryExtensions`
1. Selecione Configurações: `Required permissions`
1. Selecione a API `Windows Active Directory`
1. Coloque uma marca de seleção em permissões de aplicativo: `Read and write directory data` e`Save`
1. Escolha `Grant permissions` e confirme `Yes`.
1. Copie para a área de transferência e salve os seguintes identificadores de WebApp-GraphAPI-DirectoryExtensions>Settings>Properties>
*  `Application ID` . Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`
* `Object ID`. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`

## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Como modificar a política personalizada para adicionar o `ApplicationObjectId`

Para cada TechnicalProfile que lerá ou gravará atributos de extensão, você deve adicionar um elemento `<Metadata>` com os dois itens: ApplicationObjectId e ClientId, que foram obtidos nas etapas anteriores.

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```
[!NOTE]
<TechnicalProfile Id="AAD-Common"> é "comum" pois seus elementos são reutilizados em todos os TechnicalProfiles do Azure Active Directory usando o elemento:
```
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
```
[!NOTE]
Às vezes, quando o TechnicalProfile grava pela primeira vez para a propriedade de extensão recém-criada, pode ocorrer um erro ocasional como: a propriedade criada não foi encontrada.  .*  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Como usar a nova propriedade de extensão / atributo personalizado em um percurso do usuário


1. Abra o arquivo da terceira parte confiável (RP) que descreve o seu percurso do usuário de edição da política.  Se você estiver começando, pode ser aconselhável baixar a versão já configurada do arquivo RP-PolicyEdit diretamente na seção Política de Personalização do Azure B2C no portal do Azure.  Como alternativa, abra o arquivo XML da pasta de armazenamento.
2. Adicione uma declaração personalizada `loyaltyId`.  Ao incluir uma declaração personalizada no elemento `<RelyingParty>`, ela será indicada como parâmetro para o TechnicalProfiles de percurso do usuário, além de ser incluída no token para o aplicativo.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Adicione uma definição de declaração ao arquivo de política de extensão `TrustFrameworkExtensions.xml` dentro do elemento ``<ClaimsSchema>``, conforme mostrado abaixo.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Adicione a mesma definição de declaração ao arquivo de política base `TrustFrameworkBase.xml` .  
Observação: geralmente, não é necessário adicionar uma definição `ClaimType` nos arquivos de extensões e base, no entanto, como as próximas etapas adicionarão o extension_loyaltyId ao TechnicalProfiles nos arquivos base, o validador de política rejeitará o carregamento do arquivo base sem ele.

Observação: pode ser útil rastrear a execução do percurso do usuário chamado "ProfileEdit" no arquivo TrustFrameworkBase.xml.  Pesquise o percurso do usuário do mesmo nome no seu editor e observe que a orquestração da etapa 5 invoca o TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Pesquise e inspecione esse TechnicalProfile para se familiarizar com o fluxo.

5. Adicione o loyaltyId como declaração de entrada e saída no TechnicalProfile "SelfAsserted-ProfileUpdate"

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Adicione a declaração no TechnicalProfile "AAD-UserWriteProfileUsingObjectId" para manter o valor da declaração na propriedade de extensão para o usuário atual no diretório.

```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Adicione a declaração no TechnicalProfile "AAD-UserReadUsingObjectId" para ler o valor do atributo de extensão sempre que um usuário fizer logon.
Observação: até o momento, os TechnicalProfiles só foram alterados no fluxo de contas locais.  Se quiser o novo atributo no fluxo de uma conta social/federada, será necessário alterar um conjunto diferente de TechnicalProfiles. Confira as próximas etapas.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```

[!IMPORTANT]
O elemento IncludeTechnicalProfile acima adiciona todos os elementos do AAD comum para esse TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Teste a política personalizada usando a opção "Executar Agora"

     1. Open the **Azure AD B2C Blade** and navigate to **Identity Experience Framework > Custom policies**.
     2. Select the custom policy that you uploaded, and click the **Run now** button.
     3. You should be able to sign up using an email address.

O token de id enviado novamente para o seu aplicativo incluirá a propriedade de extensão nova como uma declaração personalizada precedida por extension_loyaltyId. Confira o exemplo.

```
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximas etapas

Adicione a nova declaração aos fluxos para logons de conta social alterando os TechnicalProfiles listados abaixo. Esses dois TechnicalProfiles são usados por logons de conta social/federados para gravar e ler os dados do usuário usando o alternativeSecurityId como o localizador do objeto de usuário.
```
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```


## <a name="reference"></a>Referência

* O **Perfil técnico (TP)** é um tipo de elemento que pode ser pensado como uma *função* que define o nome de um ponto de extremidade, seus metadados, seu protocolo e os detalhes da troca de declarações que o Identity Experience Framework deve executar.  Quando essa *função* é chamada em uma etapa de orquestração ou de outro TechnicalProfile, o InputClaims e o OutputClaims são fornecidos como parâmetros pelo chamador.


* Para tratamento completo em propriedades de extensão, confira o artigo [EXTENSÕES DE ESQUEMA DE DIRETÓRIO | CONCEITOS DA API DO GRAPH ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)
[!NOTE]
Atributos de extensão na API do Graph são nomeados usando a convenção de políticas personalizadas `extension_ApplicationObjectID_attributename`, consulte os atributos de extensões como extension_attributename, e omitindo o ApplicationObjectId no XML

