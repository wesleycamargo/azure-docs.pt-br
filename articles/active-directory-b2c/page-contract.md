---
title: Selecionar um contrato de página no Azure Active Directory B2C | Microsoft Docs
description: Saiba como selecionar um contrato de página no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 39198c0093f018b64a1292f023914651b51b4faf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403775"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Selecionar um contrato de página no Azure Active Directory B2C usando políticas personalizadas

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Você pode habilitar o código do JavaScript do lado do cliente em suas políticas B2C do Azure Active Directory (Azure AD) se você estiver usando fluxos de usuário ou políticas personalizadas. Este artigo discute como selecionar um contrato de página no Azure AD B2C, configurando-o em uma [política personalizada](active-directory-b2c-overview-custom.md). Um contrato de página é uma associação de elementos que o Azure AD B2C fornece e o conteúdo que você fornece. Se pretende usar [Javascript](javascript-samples.md), você precisa definir uma versão de contrato de página para todas as suas definições de conteúdo em sua política personalizada.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, confira [Sobre como usar JavaScript e as versões de contrato de página em um fluxo de usuário](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Substituir os valores de DataUri

Em suas políticas personalizadas, você pode ter [ContentDefinitions](contentdefinitions.md) que definem os modelos HTML usados no percurso do usuário. O **ContentDefinition** contém um **DataUri** que se refere aos elementos da página fornecidos pelo Azure AD B2C. O **LoadUri** é o caminho relativo para o conteúdo HTML e CSS que você fornece.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Para selecionar um contrato de página, você deve alterar os valores de **DataUri** em seu [ContentDefinitions](contentdefinitions.md) em suas políticas. Ao mudar dos antigos valores de **DataUri** para os novos, você está selecionando um pacote imutável. A vantagem de usar esse pacote é saber que ele não sofrerá alterações e não causará um comportamento inesperado em sua página. 

Para configurar um contrato de página, use a tabela a seguir para encontrar valores de **DataUri**. 

| Valor antigo de DataUri | Novo valor de DataUri |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre como personalizar a interface do usuário dos seus aplicativos em [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



