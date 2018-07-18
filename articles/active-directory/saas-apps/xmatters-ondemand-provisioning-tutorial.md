---
title: 'Tutorial: Configurar a xMatters OnDemand para provisionamento automático de usuário com Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para xMatters OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: b5038d176c302a4a2f75e2dd5235ea8f4789f10b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222627"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar a xMatters OnDemand para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas na xMatters OnDemand e no Microsoft Azure AD (Azure Active Directory) para configurar o Microsoft Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para a xMatters OnDemand.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

*   Um locatário do Azure AD
*   Um locatário do xMatters OnDemand com o plano [Starter](https://www.xmatters.com/pricing) ou melhor habilitado 
*   Uma conta de usuário na xMatters OnDemand com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do xMatters OnDemand](https://help.xmatters.com/xmAPI), disponível para as equipes do xMatters OnDemand no plano Starter ou em outro com mais recursos.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar o xMatters OnDemand da galeria

Antes de configurar a xMatters OnDemand para provisionamento automático de usuário com Microsoft Azure AD, será necessário adicionar a xMatters OnDemand da galeria de aplicativos do Microsoft Azure AD na sua lista de aplicativos SaaS gerenciados.

**Para adicionar a xMatters OnDemand da galeria de aplicativos do Microsoft Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]
    
3. Para adicionar a xMatters OnDemand, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **xMatters OnDemand**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. No painel de resultados, selecione **xMatters OnDemand** e, em seguida, clique no botão **Adicionar** para adicionar a xMatters OnDemand à lista de aplicativos SaaS.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Atribuir usuários ao xMatters OnDemand

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, será necessário decidir quais usuários e/ou grupos no Microsoft Azure AD precisarão acessar a xMatters OnDemand. Uma vez decidido, será possível atribuir esses usuários e/ou grupos à xMatters OnDemand seguindo estas instruções:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Dicas importantes para atribuir usuários à xMatters OnDemand

*   É recomendável que um único usuário do Microsoft Azure AD seja atribuído à xMatters OnDemand para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário à xMatters OnDemand, você deverá selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Configurar o provisionamento automático de usuário para a xMatters OnDemand 

Esta seção guia-o através das etapas para configurar o serviço de provisionamento do Microsoft Azure AD para criar, atualizar e desabilitar usuários e/ou grupos na xMatters OnDemand com base em atribuições de usuário e/ou grupo no Microsoft Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o xMatters OnDemand, seguindo as instruções fornecidas no [tutorial do logon único do xMatters OnDemand](xmatters-ondemand-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para a xMatters OnDemand no Microsoft Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione a xMatters OnDemand da lista de aplicativos SaaS.
 
    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Selecione a guia **Provisionamento**.
    
    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o **Nome de Usuário do Administrador**, **Senha de Administrador** e **Domínio** da conta da xMatters OnDemand.

    *   No campo **Nome de Usuário do Administrador**, preencha o nome de usuário da conta de administrador no locatário da xMatters OnDemand. Exemplo: admin@contoso.com.

    *   No campo **Senha de Administrador**, preencha a senha correspondente ao nome de usuário do administrador.

    *   No campo **Domínio**, preencha o subdomínio do seu locatário do xMatters OnDemand.
    Exemplo: para uma conta com uma URL de locatário de https://my-tenant.xmatters.com, o subdomínio seria **my-tenant**.

6. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Microsoft Azure AD possa conectar-se à xMatters OnDemand. Se a conexão falhar, certifique-se de que a conta da xMatters OnDemand tenha permissões de administrador e tente novamente.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com a xMatters OnDemand**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Revise os atributos de usuário que são sincronizados do Microsoft Azure AD com a xMatters OnDemand na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder as contas de usuários na xMatters OnDemand para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com a xMatters OnDemand**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Revise os atributos de grupo que são sincronizados do Microsoft Azure AD com a xMatters OnDemand na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são utilizados para corresponder aos grupos na xMatters OnDemand para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../active-directory-saas-scoping-filters.md).

14. Para habilitar o serviço de provisionamento do Microsoft Azure AD para a xMatters OnDemand, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os usuários e/ou grupos a serem provisionados para a xMatters OnDemand, escolhendo os valores desejados na seção **Escopo** nas **Configurações**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do xMatters OnDemand](./media/xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento do Microsoft Azure AD na xMatters OnDemand.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
