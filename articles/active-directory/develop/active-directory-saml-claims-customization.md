---
title: "Personalizando as declarações emitidas no token SAML para aplicativos pré-integrados no Azure Active Directory | Microsoft Docs"
description: "Saiba como personalizar as declarações emitidas no token SAML para aplicativos pré-integrados no Active Directory do Azure"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personalizando as declarações emitidas no token SAML para aplicativos pré-integrados no Active Directory do Azure
Hoje, o Active Directory do Azure oferece suporte a milhares de aplicativos pré-integrados na Galeria de Aplicativos Azure AD, incluindo mais de 150 que oferecem suporte a logon único usando o protocolo SAML 2.0. Quando um usuário se autentica em um aplicativo por meio do Azure AD usando SAML, o Azure AD envia um token para o aplicativo (por um redirecionamento HTTP 302). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm partes de informações sobre o usuário conhecidas como "declarações".

Em linguagem de identificação, uma "declaração" são informações que um provedor de identidade declara sobre um usuário dentro do token que ele emite para esse usuário. Em um [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), esses dados geralmente estão contidos na Declaração de Atributo SAML, e a ID exclusiva do usuário é geralmente representada no Subject SAML.

Por padrão, o Azure AD emite um token SAML para seu aplicativo que contém uma declaração NameIdentifier, com um valor do nome de usuário no Azure AD. Esse valor pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o registro do aplicativo no Portal clássico do Azure e selecione a guia **Atributos** sob o aplicativo.

![Guia Atributos][1]

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:
* O aplicativo foi escrito para exigir um conjunto diferente de URIs de declaração ou valores de declaração 
* O aplicativo foi implantado de uma forma que exige que a declaração NameIdentifier seja algo diferente do nome de usuário (também conhecido como nome UPN) armazenado no Azure Active Directory. 

Você pode editar qualquer um dos valores de declaração padrão. Selecione o ícone na forma de lápis que aparece à direita sempre que você passa o mouse sobre uma das linhas na tabela de atributos do token SAML. Você também pode remover declarações (exceto NameIdentifier) usando o ícone **X** e adicionar novas declarações usando o botão **Adicionar atributo de usuário**.

## <a name="editing-the-nameidentifier-claim"></a>Editando a declaração NameIdentifier
Para resolver o problema no qual o aplicativo foi implantado usando um nome de usuário diferente, clique no ícone de lápis próximo à declaração NameIdentifier. Essa ação apresenta uma caixa de diálogo com várias opções diferentes:

![Editar Atributo de Usuário][2]

No menu **Valor do Atributo**, selecione **user.mail** de modo a definir a declaração NameIdentifier para ser o endereço de email do usuário no diretório. Ou selecione **user.onpremisessamaccountname** para definir como o Nome da Conta SAM do usuário que foi sincronizado do Azure AD local.

Também é possível usar a função especial ExtractMailPrefix() para remover o sufixo de domínio do endereço de email ou do nome UPN. Assim, somente a primeira parte do nome do usuário está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com).

![Editar Atributo de Usuário][3]

## <a name="adding-claims"></a>Adicionando declarações
Ao adicionar uma declaração, você pode especificar o nome do atributo (que não precisa seguir rigidamente um padrão de URI de acordo com a especificação SAML). Defina o valor para qualquer atributo de usuário armazenado no diretório.

![Adicionar Atributo de Usuário][4]

Por exemplo, você precisa enviar o departamento ao qual o usuário pertence na organização como uma declaração (por exemplo, Vendas). É possível inserir qualquer valor de declaração esperado pelo aplicativo e selecionar **user.department** como o valor.

Se, para um determinado usuário, não houver valor armazenado para um atributo selecionado, essa declaração não será emitida no token.

**Observação:** há suporte apenas para **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** durante a sincronização de dados de usuário do Active Directory local com a [ferramenta Azure AD Connect](../active-directory-aadconnect.md).

## <a name="next-steps"></a>Próximas etapas
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../active-directory-saas-custom-apps.md)
* [Solução de problemas de logon único baseado em SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
