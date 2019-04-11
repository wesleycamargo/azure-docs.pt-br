---
title: 'Tutorial: Configurar o Samanage para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca43b62e66e3a736aa52fdd10fe36e635daba245
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058009"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar o Samanage para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Samanage e no Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Samanage.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um [locatário Samanage](https://www.samanage.com/pricing/)com o pacote Professional
* Uma conta de usuário no Samanage com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Azure AD conta com a [API REST do Samanage](https://www.samanage.com/api/), que está disponível para desenvolvedores do Samanage para contas com o pacote Professional.

## <a name="adding-samanage-from-the-gallery"></a>Adição do Samanage da galeria

Antes de configurar o Samanage para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Samanage da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Samanage da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Samanage**, selecione **Samanage** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-samanage"></a>Atribuindo usuários ao Samanage

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Samanage. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Samanage seguindo estas instruções:

*   [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir usuários ao Samanage

*    Funções do Samanage são populadas dinâmica e automaticamente na interface do usuário do portal do Azure hoje. Antes de atribuir funções do Samanage aos usuários, certifique-se de que uma sincronização inicial seja concluída no Samanage para recuperar as funções mais recentes em seu locatário do Samanage.

*    É recomendável que um único usuário do Azure AD seja atribuído ao Samanage para testar sua configuração inicial de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.

*   Ao atribuir um usuário ao Samanage, você deve selecionar qualquer função específica de aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Configurando o provisionamento automático de usuário ao Samanage

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Samanage com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único com base em SAML para o Samanage, seguindo as instruções fornecidas no [Tutorial do logon único do Samanage](samanage-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Samanage no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Samanage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o **Nome de Usuário do Administrador**, e **Senha do Administrador** da conta do Samanage. Exemplos desses valores são:

   * No campo **Nome de Usuário do Administrador**, preencha o nome de usuário da conta do administrador no locatário do Samanage. Exemplo: admin@contoso.com.

   * No campo **Senha de Administrador**, preencha a senha da conta de administrador correspondente ao nome de usuário do administrador.

6. Depois de preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao Samanage. Se a conexão falhar, verifique se sua conta do Samanage tem permissões de Administrador e tente novamente.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Clique em **Salvar**.

9. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Samanage**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Revise os atributos de usuário que são sincronizados do Azure AD para Samanage na seção **Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Para habilitar mapeamentos de grupo, na seção **Mapeamentos**, selecione **Sincronizar grupos do Azure Active Directory com Samanage**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Defina **Habilitado** para **Sim** para sincronizar grupos. Revise os atributos do grupo que são sincronizados do Azure AD para Samanage na seção**Mapeamentos de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Samanage em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD no Samanage, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os usuários e/ou grupos a serem provisionados para o Samanage escolhendo os valores desejados em **Escopo** na seção **Configurações**. Ao escolher a opção **Sincronizar todos os usuários e grupos**, considere as limitações conforme descrito na seção **Limitações do conector** abaixo.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Samanage.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Se a opção **Sincronizar todos os usuários e grupos** estiver selecionada e um valor padrão estiver configurado para as **Funções** de atributo Samanage, assegure-se de que o valor desejado sob o valor padrão **seja nulo (é opcional)** campo é expresso no seguinte formato **{"displayName": "role"}** em que role é o valor padrão desejado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
