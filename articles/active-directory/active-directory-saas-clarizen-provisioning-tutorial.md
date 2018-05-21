---
title: 'Tutorial: Configurar Clarizen para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Clarizen.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 779fa09781b5ea6e13450d609703bec0c08e38e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Tutorial: Configurar Clarizen para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Clarizen e no Microsoft Azure Active Directory (Azure Active Directory) para configurar o Microsoft Azure Active Directory para provisionar e desprovisionar automaticamente usuários e/ou grupos no Clarizen.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

*   Um locatário do Azure AD
*   Um locatário do Clarizen com o plano [Enterprise Edition](https://www.clarizen.com/product/pricing/) ou outro melhor habilitado 
*   Uma conta de usuário no Clarizen com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Microsoft Azure Active Directory depende da [API Clarizen](https://api.clarizen.com/v2.0/services/), disponível para as equipes do Clarizen no plano Enterprise Edition ou outro melhor.

## <a name="adding-clarizen-from-the-gallery"></a>Adicionando o Clarizen da galeria
Antes de configurar o Clarizen para o provisionamento automático de usuário com o Microsoft Azure Active Directory, é necessário adicionar o Clarizen por meio da galeria de aplicativos do Microsoft Azure Active Directory à lista de aplicativos SaaS gerenciados.

**Para adicionar o Clarizen por meio da galeria de aplicativos do Microsoft Azure Active Directory, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]
    
3. Para adicionar o Clarizen, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Clarizen**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. No painel de resultados, selecione **Clarizen** e, em seguida, clique no botão **Adicionar** para adicionar o Clarizen à lista de aplicativos SaaS.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Como atribuir usuários ao Clarizen

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Microsoft Azure Active Directory precisam acessar o Clarizen. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Clarizen seguindo estas instruções:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Dicas importantes para atribuir usuários ao Clarizen

*   É recomendável que um único usuário do Microsoft Azure Active Directory seja atribuído ao Clarizen para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Clarizen, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Configurando o provisionamento automático de usuário no Clarizen 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Microsoft Azure Active Directory para criar, atualizar e desabilitar usuários e/ou grupos no Clarizen com base em atribuições de usuário e/ou grupo no Microsoft Azure Active Directory.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Clarizen, seguindo as instruções fornecidas no [tutorial do logon único do Clarizen](active-directory-saas-clarizen-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Clarizen no Microsoft Azure Active Directory:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione o Clarizen na lista de aplicativos SaaS.
 
    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Selecione a guia **Provisionamento**.
    
    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de Administrador**, insira o **Domínio**, **Nome de Usuário Administrador** e a **Senha do Administrador** de sua conta do Clarizen. Exemplos desses valores são:

    *   No campo **Nome de Usuário Administrador**, popule o nome de usuário da conta do administrador no Locatário do Clarizen. Exemplo: admin@contoso.com.

    *   No campo **Senha de Administrador**, preencha a senha da conta de administrador correspondente ao nome de usuário do administrador.

    *   No campo **Domínio**, preencha o subdomínio com base na Etapa 6.
    
6. Recupere o **serverLocation** para sua conta do Clarizen com base em etapas mencionadas em **Autenticação** do [Guia de API Rest do Clarizen](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Após obter o serverLocation, use o subdomínio da URL, como destacado abaixo, para preencher o campo **Domínio**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Ao popular os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Microsoft Azure Active Directory pode se conectar ao Clarizen. Se a conexão falhar, verifique se a conta do Clarizen tem permissões de Administrador e tente novamente.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Clarizen**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Examine os atributos de usuário que serão sincronizados do Microsoft Azure Active Directory para o Clarizen na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Clarizen em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar grupos do Azure Active Directory com o Clarizen**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Examine os atributos de grupo que serão sincronizados do Microsoft Azure Active Directory para o Clarizen na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do Clarizen em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Microsoft Azure Active Directory no Clarizen, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os usuários e/ou grupos a serem provisionados para o Clarizen escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Microsoft Azure Active Directory no Clarizen.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
