---
title: Provisionar aplicativos com filtros de escopo | Microsoft Docs
description: "Saiba como usar filtros de escopo para impedir que objetos em aplicativos, que dão suporte a provisionamento automatizado de usuários, sejam provisionados, caso um objeto não satisfaça suas necessidades de negócios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e40ba3d3035d5c04017a8ed558981b01fae40a13
ms.contentlocale: pt-br
ms.lasthandoff: 09/27/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicativo com base em atributo com filtros de escopo
O objetivo desta seção é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários serão provisionados ao aplicativo.

## <a name="scoping-filter-use-cases"></a>Casos de uso do filtro de escopo

Um filtro de escopo permite que o serviço de provisionamento do Azure AD inclua ou exclua qualquer usuário contendo um atributo que corresponda a um valor específico. Por exemplo, ao provisionar usuários do Azure AD para um aplicativo SaaS utilizado por uma equipe de vendas, você poderá especificar que apenas os usuários com um atributo "Departamento" de "Vendas" deverá estar no escopo para provisionamento.

Os filtros de escopo podem ser utilizados diferentemente, dependendo do tipo de conector de provisionamento:

* **Provisionamento de saída do Azure AD para aplicativos SaaS** - Quando o Azure AD for o sistema de origem, as [atribuições de grupo e usuário](active-directory-coreapps-assign-user-azure-portal.md) serão o método mais comum para determinar quais usuários estarão no escopo para provisionamento. Essas atribuições também são utilizadas para habilitar o logon único e fornecer um método exclusivo para gerenciar o acesso e provisionamento. Os filtros de escopo podem ser utilizados opcionalmente, além de atribuições ou, em vez de eles, para filtrar usuários com base em valores de atributos.

>[!TIP]
> É possível desabilitar o provisionamento baseado em atribuições para um aplicativo empresarial, definindo o menu **[Escopo](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)** nas configurações de provisionamento para **Sincronizar todos os usuários e grupos**. Usar esta opção mais filtros de escopo baseados em atributos oferece um desempenho mais rápido que o uso de atribuições baseadas em grupo.  

* **Provisionamento de entrada de aplicativos para Azure AD e Active Directory** - Quando um [aplicativo HCM como Workday](active-directory-saas-workday-tutorial.md) for o sistema fonte, os filtros de escopo serão o método principal para determinar quais usuários deverão ser provisionados do aplicativo HCM para o Active Directory ou Azure Active Directory.

Por padrão, os conectores de provisionamento do Azure AD não possuem filtros de escopo baseados em atributos configurados. 

## <a name="scoping-filter-construction"></a>Construção do filtro de escopo

Um filtro de escopo consiste de uma ou **cláusulas**. As cláusulas determinam quais usuários poderão passar pelo filtro de escopo, avaliando os atributos de cada usuário. Por exemplo, é possível ter uma cláusula que exija que o atributo "Estado" do usuário seja igual a "Nova York", então, somente usuários de Nova York serão provisionados no aplicativo. 

Uma cláusula exclusiva define uma condição única para um valor de atributo único. Se várias cláusulas forem criadas em um único filtro de escopo, elas serão avaliadas em conjunto utilizando a lógica "E". Isso significa que todas as cláusulas devem ser avaliadas como "verdadeiras" para que um usuário ser provisionado.

Finalmente, vários filtros de escopo podem ser criados para um único aplicativo. Se houver múltiplos filtros de escopo, eles serão avaliados em conjunto utilizando a lógica "OU". Isso significa que, se todas as cláusulas em qualquer um dos filtros de escopo configurados forem válidas para "verdadeiro", o usuário será provisionado.

Cada usuário ou grupo processado pelo serviço de provisionamento do Azure AD sempre é avaliado individualmente em relação a cada filtro de escopo.

Para um exemplo, considere o filtro de escopo abaixo:

![Filtro de Escopo](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

De acordo com esse filtro de escopo, os usuários devem atender aos seguintes critérios para serem provisionados:

1. Eles devem estar em Nova York
2. Eles devem trabalhar no departamento de engenharia
3. A ID do funcionário da empresa deve estar entre 1000000 e 2000000
4. O cargo não deve ser nulo ou vazio

## <a name="creating-scoping-filters"></a>Criando filtros de escopo
Os filtros de escopo são configurados como parte dos mapeamentos de atributos para cada conector de provisionamento de usuário do Azure AD. O procedimento abaixo assume que você já configurou o provisionamento automático para um dos [aplicativos com suporte](active-directory-saas-tutorial-list.md) e está adicionando um filtro de escopo a ele.

### <a name="to-create-a-scoping-filter"></a>Para criar um filtro de escopo:
1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.
2. Selecione o aplicativo para o qual você configurou provisionamento automático. Exemplo: "ServiceNow"
3. Selecione a guia **Provisionamento**.
4. No **Mapeamentos**, selecione o mapeamento para o qual você deseja configurar um filtro de escopo. Exemplo: "Sincronizar Usuários do Azure Active Directory para ServiceNow"
5. Selecione o menu **Escopo do objeto de origem**.
6. Selecione **Adicionar filtro de escopo**.
7. Defina uma cláusula selecionando um **Nome do Atributo** de origem, um **Operador** e um **Valor do Atributo** para corresponder. Abaixo estão os operadores com suporte:
   * **IGUAL A** - A cláusula retorna "verdadeiro" se o atributo avaliado corresponder exatamente ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).
   * **DIFERENTE DE** - A cláusula retorna "verdadeiro" se o atributo avaliado não corresponder ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).
   * **VERDADEIRO** - A cláusula retorna "verdadeiro" se o atributo avaliado contiver um valor de verdadeiro booliano.
   * **FALSO** - A cláusula retorna "verdadeiro" se o atributo avaliado contiver um valor de falso booliano.
   * **NULO** - A cláusula retorna "verdadeiro" se o atributo avaliado estiver vazio.
   * **NÃO NULO** - A cláusula retorna "verdadeiro" se o atributo avaliado não estiver vazio.
   * **COINCIDIR EXREG** - A cláusula retorna "verdadeiro" se o atributo avaliado corresponder a um padrão de expressão regular. Exemplo: ([1-9] [0-9]) corresponde a qualquer número entre 10 e 99
   * **NÃO COINCIDIR EXREG** - A cláusula retorna "verdadeiro" se o atributo avaliado não corresponder a um padrão de expressão regular.
8. Selecione **Adicionar nova cláusula de escopo**.
9. Opcionalmente, repita as etapas de 7 a 8 para adicionar outras cláusulas de escopo.
10. Em **Título do Filtro de Escopo**, adicione um nome para o filtro de escopo.
11. Selecione **OK**.
12. Selecione **OK** novamente nos Filtros de Escopo de tela (ou repita as etapas de 6 a 11 para adicionar outro filtro de escopo).
13. Selecione **Salvar** na tela de Mapeamento de Atributo. 

>[!IMPORTANT] 
> Salvar um novo filtro de escopo dispara uma nova sincronização completa para o aplicativo, onde todos os usuários no sistema de origem são avaliados novamente em relação ao novo filtro de escopo. Se um usuário no aplicativo estava anteriormente no escopo de provisionamento, mas sair do escopo, sua conta será desabilitada ou desprovisionada no aplicativo.


## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)


