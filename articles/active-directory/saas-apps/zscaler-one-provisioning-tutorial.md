---
title: 'Tutorial: Configurar o Zscaler One para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário ao Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 26448d5056e58cf1110e825ad04c5123fca20684
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259432"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler One para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler One e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos ao Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Esse conector está atualmente em visualização pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

* Um locatário do Azure AD
* Um locatário do Zscaler One
* Uma conta de usuário no Zscaler One com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD se baseia no Zscaler um API de SCIM, que está disponível para desenvolvedores do Zscaler One para contas com o pacote Enterprise.

## <a name="adding-zscaler-one-from-the-gallery"></a>Adicionar o Zscaler One da galeria

Antes de configurar o Zscaler One para provisionamento com o Azure AD automático de usuário, você precisará adicionar o Zscaler One da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler One da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler One**, selecione **Zscaler One** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler One na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-one"></a>Atribuir usuários ao Zscaler One

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no AD do Azure precisa ter acesso ao Zscaler One. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zscaler One, seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir usuários ao Zscaler One

* É recomendável que um único usuário do Azure AD seja atribuído ao Zscaler One para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zscaler One, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-one"></a>Configurando o provisionamento automático de usuário ao Zscaler One

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler One com base no usuário e/ou as atribuições de grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML single sign-on para Zscaler One, seguindo as instruções fornecidas na [Zscaler um único logon tutorial](zscaler-One-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Zscaler One no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zscaler One**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link do Zscaler One na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** seção, insira o **URL do locatário** e **Token secreto** da sua conta Zscaler One, conforme descrito na etapa 6.

6. Para obter o **URL do locatário** e **Token secreto**, navegue até **Administração > configurações de autenticação** no Zscaler um interface de usuário do portal e clique em  **SAML** sob **tipo de autenticação**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    Clique em **configurar SAML** para abrir **configuração SAML** opções.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    Selecione **Enable SCIM-Based provisionamento** para recuperar **URL de Base** e **Token de portador**, em seguida, salve as configurações. Cópia de **Base URL** para **URL do locatário** e **Token de portador** para **Token secreto** no portal do Azure.

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Zscaler One. Se a conexão falhar, verifique se que sua conta do Zscaler One tem permissões de administrador e tente novamente.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Zscaler One**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Zscaler One na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do Zscaler One para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Zscaler One**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Zscaler One na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Zscaler One para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o Azure AD Zscaler um serviço de provisionamento, altere o **Status de provisionamento** ao **na** no **configurações** seção.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou grupos que você gostaria serem provisionados no Zscaler One escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no Zscaler One de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximos passos

* [Saiba como examinar os logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
