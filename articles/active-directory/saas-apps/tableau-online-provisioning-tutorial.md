---
title: 'Tutorial: Configurar o Tableau Online para o provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f732eebd410a6b52a21a46925a29bf4676f7c8cb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270779"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para o provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Tableau Online e no Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Tableau Online.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

*   Um locatário do Azure AD
*   Um [locatário do Tableau Online](https://www.tableau.com/)
*   Uma conta de usuário do Tableau Online com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API REST do Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), que está disponível para os desenvolvedores do Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Adição do Tableau Online da galeria
Antes de configurar o Tableau Online para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Tableau Online por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Online da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Tableauonline**, selecione **Tableauonline** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicativo.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-tableau-online"></a>Atribuindo usuários ao Tableau Online

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Azure AD precisam acessar o Tableau Online. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Tableau Online seguindo estas instruções:

*   [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir usuários ao Tableau Online

*   É recomendável que um único usuário do Azure AD seja atribuído ao Tableau Online para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Tableau Online, você precisa selecionar qualquer função específica de aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Configurando o provisionamento automático de usuário no Tableau Online

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Tableau Online com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Tableau Online, seguindo as instruções fornecidas no [Tutorial do logon único do Tableau Online](tableauonline-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Tableau Online no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Tableau Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link do Tableau Online na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o **Domínio**, o **Nome de Usuário Administrador**, a **Senha de Administrador** e a **URL de Conteúdo** de sua conta do Tableau Online:

   * No campo **Domínio**, preencha o subdomínio com base na Etapa 6.

   * No campo **Nome de Usuário Administrador**, popule o nome de usuário da conta do administrador no Locatário do Clarizen. Exemplo: admin@contoso.com.

   * No campo **Senha de Administrador**, preencha a senha da conta de administrador correspondente ao nome de usuário do administrador.

   * No campo **URL de Conteúdo**, popule o subdomínio com base na Etapa 6.

6. Depois que você fizer logon em sua conta administrativa do Tableau Online, os valores do **Domínio** e da **URL de Conteúdo** poderão ser extraídos da URL da página Administrador.

    * O **Domínio** da conta do Tableau Online pode ser copiado desta parte da URL:

        ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **URL de Conteúdo** da conta do Tableau Online pode ser copiada desta seção, e ela é um valor definido durante a configuração de conta. Neste exemplo, o valor é "contoso":

        ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > O **Domínio** pode ser diferente daquele mostrado aqui.

7. Ao popular os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao Tableau Online. Se a conexão falhar, verifique se a conta do Tableau Online tem permissões de Administrador e tente novamente.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Tableau**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Tableau Online na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Tableau**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Tableau Online na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário no Tableau Online para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Tableau Online, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários e/ou grupos a serem provisionados no Tableau Online escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Tableau Online.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
