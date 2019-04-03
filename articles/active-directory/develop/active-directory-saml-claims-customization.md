---
title: Personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD | Microsoft Docs
description: Aprenda a personalizar as declarações emitidas no token SAML para aplicativos empresariais no Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878563"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais

Hoje, Azure Active Directory (AD do Azure) dá suporte a logon único (SSO) com a maioria dos aplicativos empresariais, incluindo os dois aplicativos pré-integrados na Galeria de aplicativos do Azure AD, bem como aplicativos personalizados. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando o protocolo SAML 2.0, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm partes de informações sobre o usuário conhecidas como *declarações*.

Uma *declaração* são informações que um provedor de identidade declara sobre um usuário dentro do token que emite para esse usuário. No [Token SAML](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na Instrução de Atributo SAML. A ID única do usuário é normalmente representada na SAML Subject, também denominada Identificador de Nome.

Por padrão, o Azure AD emite um token SAML para seu aplicativo que contém um `NameIdentifier` uma declaração com um valor de nome de usuário do usuário (também conhecido como o nome UPN) no Azure AD, que pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no Portal do Azure. Em seguida, abra o **atributos de usuário & declarações** seção.

![Seção atributos de usuário e declarações](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:

* O aplicativo requer o `NameIdentifier` ou NameID alegam ser algo diferente do nome de usuário (ou nome UPN) armazenado no Azure AD.
* O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.

## <a name="editing-nameid"></a>Edição de NameID

Para editar o NameID (valor de identificador de nome):

1. Abra o **Nomeie o valor do identificador** página.
1. Selecione o atributo ou a transformação que você deseja aplicar ao atributo. Opcionalmente, você pode especificar o formato desejado a declaração NameID para ter a ele.

   ![Edite o valor de NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato de NameID

Se a solicitação SAML contém o elemento NameIDPolicy com um formato específico, do Azure AD poderão usar o formato na solicitação.

Se a solicitação SAML não contiver um elemento para NameIDPolicy, o Azure AD emitirá o NameID com o formato que você especificar. Se nenhum formato é especificado o AD do Azure usará o formato de fonte padrão associado à fonte de declaração selecionada.

Dos **formato do identificador de escolha** menu suspenso, você pode selecionar uma das opções a seguir.

| Formato de NameID | DESCRIÇÃO |
|---------------|-------------|
| **Padrão** | Azure AD usará o formato de fonte padrão. |
| **Persistente** | Azure AD usará persistente como o formato de NameID. |
| **EmailAddress** | Azure AD usará o endereço de email como o formato de NameID. |
| **Não especificado** | Azure AD usará não especificado como o formato de NameID. |
| **Transitório** | Azure AD usará transitório como o formato de NameID. |

Para saber mais sobre o atributo NameIDPolicy, consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte desejada para a declaração `NameIdentifier` (ou NameID). Você pode selecionar entre as opções a seguir.

| NOME | DESCRIÇÃO |
|------|-------------|
| Email | Endereço de email do usuário |
| userprincipalName | Nome principal do usuário (UPN) do usuário |
| onpremisessamaccount | Nome da conta SAM sincronizada do Azure AD local |
| objectid | ObjectId do usuário no AD do Azure |
| employeeid | EmployeeID do usuário |
| Extensões de diretório | Extensões de diretório [sincronizadas do Active Directory local usando a Sincronização do Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de Extensão 1-15 | Atributos de extensão locais usados para estender o esquema do Azure AD |

Para obter mais informações, consulte [tabela 3: Valores de ID válidos por origem](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Declarações especiais – transformações

Você também pode usar as funções de transformações de declarações.

| Função | DESCRIÇÃO |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo do domínio do endereço de email ou nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Une um atributo a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para anexar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor de identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adicionar declarações específicas do aplicativo

Para adicionar declarações específicas de aplicativo:

1. Na **atributos de usuário e declarações**, selecione **adicionar nova declaração** para abrir o **gerenciar declarações de usuário** página.
1. Insira o **nome** das declarações. O valor não precisa estritamente seguem um padrão URI, por que a especificação SAML. Se você precisar de um padrão de URI, você poderia colocar o **Namespace** campo.
1. Selecione o **origem** onde a declaração vai recuperar seu valor. Você pode selecionar um atributo de usuário na lista suspensa de atributo de origem ou aplicar uma transformação para o atributo de usuário antes de emiti-lo como uma declaração.

### <a name="application-specific-claims---transformations"></a>Declarações específicas do aplicativo – transformações

Você também pode usar as funções de transformações de declarações.

| Função | DESCRIÇÃO |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo do domínio do endereço de email ou nome principal do usuário. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Cria um novo valor ao unir dois atributos. Opcionalmente, você pode usar um separador entre os dois atributos. |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contains()** | Gera um atributo ou uma constante se a entrada corresponde ao valor especificado. Caso contrário, você pode especificar o outra saída se não houver nenhuma correspondência.<br/>Por exemplo, se você quiser emitir uma declaração onde o valor é o endereço de email do usuário, se ele contém o domínio "@contoso.com", caso contrário, você deseja o nome UPN de saída. Para fazer isso, você poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: User<br/>*Valor*: "@contoso.com"<br/>O parâmetro 2 (saída): User<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): User. userPrincipalName |
| **EndWith()** | Gera um atributo ou uma constante se a entrada termina com o valor especificado. Caso contrário, você pode especificar o outra saída se não houver nenhuma correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é o employeeid do usuário se employeeid termina com "000", caso contrário, você deseja gerar um atributo de extensão. Para fazer isso, você poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: EmployeeID<br/>*Valor*: "000"<br/>O parâmetro 2 (saída): EmployeeID<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.extensionattribute1 |
| **StartWith()** | Gera a saída de um atributo ou uma constante de entrada começa com o valor especificado. Caso contrário, você pode especificar o outra saída se não houver nenhuma correspondência.<br/>Por exemplo, se você quiser emitir uma declaração onde o valor é o employeeid do usuário, se o país começa com "US", caso contrário, você deseja gerar um atributo de extensão. Para fazer isso, você poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: User. Country<br/>*Valor*: "US"<br/>O parâmetro 2 (saída): EmployeeID<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.extensionattribute1 |
| **Extract () - após a correspondência** | Retorna a subcadeia de caracteres depois que ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "Finance_BSimon", o valor correspondente é "Finance_", então saída da declaração é "BSimon". |
| **Extract () - antes da correspondência** | Retorna a subcadeia de caracteres até que ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "BSimon_US", o valor correspondente é "_US", então saída da declaração é "BSimon". |
| **Extract () - entre correspondência** | Retorna a subcadeia de caracteres até que ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "Finance_BSimon_US", o primeiro valor correspondente é "Finance_", o segundo valor correspondente é "_US" e saída da declaração é "BSimon". |
| **ExtractAlpha() - prefixo** | Retorna a parte em ordem alfabética de prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "BSimon_123", em seguida, ele retorna "BSimon". |
| **ExtractAlpha() - sufixo** | Retorna a parte em ordem alfabética de sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "123_Simon", em seguida, ele retorna "BSimon". |
| **ExtractNumeric() - prefixo** | Retorna a parte numérica do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "123_BSimon", em seguida, ele retorna "123". |
| **ExtractNumeric() - sufixo** | Retorna a parte numérica do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "BSimon_123", em seguida, ele retorna "123". |
| **IfEmpty()** | Gera um atributo ou uma constante se a entrada for nulo ou vazio.<br/>Por exemplo, se deseja gerar um atributo armazenado em um extensionattribute se employeeid para um determinado usuário está vazia. Para fazer isso, você poderia configurar os seguintes valores:<br/>Parâmetro 1(input): EmployeeID<br/>Parameter 2 (output): user.extensionattribute1<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): EmployeeID |
| **IfNotEmpty()** | Gera um atributo ou uma constante se a entrada não for nulo ou vazio.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um extensionattribute se employeeid para um determinado usuário não está vazio. Para fazer isso, você poderia configurar os seguintes valores:<br/>Parâmetro 1(input): EmployeeID<br/>Parameter 2 (output): user.extensionattribute1 |

Se você precisar transformações adicionais, envie sua ideia na [Fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sob o *aplicativo de SaaS* categoria.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciamento de aplicativo no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar o logon único em aplicativos que não estão na Galeria de aplicativos do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Solucionar problemas com base em SAML SSO](howto-v1-debug-saml-sso-issues.md)
