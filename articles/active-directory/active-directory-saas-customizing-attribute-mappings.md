---
title: Personalizar mapeamentos de atributo do Azure AD | Microsoft Docs
description: "Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 18415c92d50a00c14823685857ab7e2624334ec7
ms.openlocfilehash: 19e934895279adb3a32096fffafd567b294c3009
ms.lasthandoff: 03/01/2017


---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory
O AD do Microsoft Azure dá suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, Google Apps e outros. Se você tiver provisionamento de usuário para um aplicativo SaaS de terceiro habilitado, o Portal de Gerenciamento controlará seus valores de atributo na forma de uma configuração chamada "mapeamento de atributo".

Há um conjunto predefinido de mapeamentos de atributo entre objetos de usuário do AD do Azure e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. <br> 
 Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Isso significa que você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.

No portal do AD do Azure, você pode acessar esse recurso clicando em Atributos na barra de ferramentas de um aplicativo SaaS.

> [!NOTE]
> O link **Atributos** só estará disponível se você tiver o provisionamento do usuário habilitado para um aplicativo SaaS. 
> 
> 

![Salesforce][1] 

Quando você clica em Atributos na barra de ferramentas, a lista de mapeamentos atuais configurados para um aplicativo SaaS.

A captura de tela a seguir mostra um exemplo disso:

![Salesforce][2]  

No exemplo acima, você pode ver que o atributo **firstName** de um objeto gerenciado no Salesforce é populado com o valor **givenName** do objeto do Azure AD vinculado.

Se você quiser personalizar mapeamentos de atributo ou reverter configurações personalizadas de volta à configuração padrão, pode fazer isso clicando no botão relacionado na barra de ferramentas, na parte inferior de um aplicativo.

![Salesforce][3]  

Há mapeamentos de atributo que são exigidos por um aplicativo SaaS para funcionar corretamente. Na tabela de atributos, os mapeamentos de atributo relacionados têm **Sim** como valor para o atributo **Obrigatório**. Se um mapeamento de atributo for obrigatório, você não poderá excluí-lo. Nesse caso, o recurso **Excluir** não estará disponível.

Para modificar um mapeamento de atributo existente, selecione o mapeamento e clique em **Editar**. Isso abre uma página de diálogo que permite que você modifique o mapeamento de atributo selecionado.

![Editar mapeamento de atributo][4]  

## <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo
Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro. Há quatro tipos diferentes de mapeamento com suporte:

* **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
* **Constante** – o atributo de destino é preenchido com uma cadeia de caracteres especificada por você.
* **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script. 
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se o atributo de destino estiver vazio, ele será preenchido com o valor padrão que você especificar.

Além desses quatro tipos de mapeamentos de atributo básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor **padrão** . A atribuição do valor padrão assegura que um atributo de destino seja preenchido com um valor, se não houver um valor no AD do Azure nem no objeto de destino.

O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização. A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados. É uma prática recomendada manter o número de alterações consecutivas aos seus mapeamentos de atributos no mínimo.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

