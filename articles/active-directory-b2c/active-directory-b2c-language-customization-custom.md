---
title: "Azure Active Directory B2C: Personalização do idioma em políticas personalizadas | Microsoft Docs"
description: "Saiba como usar o conteúdo de localização em políticas personalizadas para vários idiomas"
services: active-directory-b2c
documentationcenter: 
author: sammak
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 11/13/2017
ms.author: sama
ms.openlocfilehash: 4ed9791d6590e3982a1bc79b96f8592995bc315c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
#<a name="language-customization-in-custom-policies"></a>Personalização de idioma em políticas personalizadas

> [!NOTE]
> Esse recurso está em uma versão prévia.
> 

Nas políticas personalizadas, a personalização de idioma funciona da mesma maneira do que as políticas internas.  Veja a [documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) interna que descreve o comportamento de como um idioma é escolhido com base nos parâmetros e nas configurações do navegador.

##<a name="enable-supported-languages"></a>Habilitar idiomas com suporte
Se as localidades de interface do usuário não forem especificadas e se o navegador do usuário solicitar um desses idiomas, os idiomas com suporte serão mostrados ao usuário.  

Os idiomas com suporte são definidos em `<BuildingBlocks>` no seguinte formato:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

O idioma padrão e os idiomas com suporte se comportam da mesma maneira do que em políticas internas.

##<a name="enable-custom-language-strings"></a>Habilitar cadeias de caracteres de idioma personalizadas

A criação de cadeias de caracteres de idioma personalizadas requer duas etapas:
1. Editar o `<ContentDefinition>` para a página para especificar uma ID de recurso para os idiomas desejados
2. Criar o `<LocalizedResources>` com as IDs correspondentes no seu `<BuildingBlocks>`

Tenha em mente que você pode colocar um `<ContentDefinition>` e `<BuildingBlock>` em seu arquivo de extensão ou no arquivo de política confiável, dependendo se você quiser que as alterações sejam feitas em todas as políticas de herança ou não.

###<a name="edit-the-contentdefinition-for-the-page"></a>Editar a ContentDefinition para a página

Para cada página que deseja localizar, você pode especificar no `<ContentDefinition>` quais recursos de idioma procurar para cada código de idioma.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

Neste exemplo, as cadeias de caracteres personalizadas de francês (fr) e inglês (en) são adicionadas à página Inscrição ou entrada unificada.  O `LocalizedResourcesReferenceId` para cada `LocalizedResourcesReference` é igual à localidade, mas você pode usar qualquer cadeia de caracteres como a ID.  Para cada combinação de idioma e página, você precisa criar um `<LocalizedResources>` correspondente mostrado a seguir.


###<a name="create-the-localizedresources"></a>Criar os LocalizedResources

Suas substituições estão contidas em seu `<BuildingBlocks>` e há um `<LocalizedResources>` para cada página e idioma especificados no `<ContentDefinition>` para cada página.  Cada substituição é especificada como um `<LocalizedString>` como o exemplo a seguir:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Há quatro tipos de elementos de cadeia de caracteres na página:

**ClaimsProvider** - rótulos para seus provedores de identidade (Facebook, Google, Azure AD etc.) **ClaimType** - Os rótulos dos seus atributos e do seu texto de ajuda correspondente ou o **UxElement** dos erros de validação de campo - Outros elementos de cadeia de caracteres na página que estão lá por padrão, como **ErrorMessage** de botões, links ou texto - Mensagens de erro de validação de formulário

Certifique-se de que os `StringId`s correspondam à página em que você está usando essas substituições; caso contrário, ela será bloqueada pela validação de política no upload.  