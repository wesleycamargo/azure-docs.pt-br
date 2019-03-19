---
title: 'Tutorial: Configurar o Zscaler Beta para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário ao Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 47cfa5b2d97148c6b925a071f46518afd3ec8d5e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109637"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler Beta para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler Beta e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e/ou grupos ao Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).
> 
> Esse conector está atualmente em visualização pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já possui o seguinte:

*   Um locatário do Azure AD
*   Um locatário do Zscaler Beta
*   Uma conta de usuário no Zscaler Beta com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API de SCIM do Zscaler Beta, que está disponível para desenvolvedores do Zscaler Beta para contas com o pacote Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar o Zscaler Beta da galeria
Antes de configurar o Zscaler Beta para provisionamento com o Azure AD automático de usuário, você precisará adicionar o Zscaler Beta da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler Beta da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]

3. Para adicionar o Zscaler Beta, clique o **novo aplicativo** botão na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Zscaler Beta**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/app-search.png)

5. No painel de resultados, selecione **Zscaler Beta**e, em seguida, clique no **Add** botão para adicionar o Zscaler Beta à sua lista de aplicativos SaaS.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/app-search-results.png)

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/app-creation.png)

## <a name="assigning-users-to-zscaler-beta"></a>Atribuir usuários ao Zscaler Beta

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Zscaler Beta. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zscaler Beta, seguindo as instruções aqui:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir usuários ao Zscaler Beta

*   É recomendável que um único usuário do Azure AD seja atribuído ao Zscaler Beta para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Zscaler Beta, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurando o provisionamento automático de usuário ao Zscaler Beta

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler Beta com base no usuário e/ou as atribuições de grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML logon único do Zscaler Beta, seguindo as instruções fornecidas na [tutorial do logon único Zscaler Beta](zscaler-beta-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o provisionamento automático de usuário do Zscaler Beta no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione o Zscaler Beta da sua lista de aplicativos SaaS.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/app-instance-search.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** seção, insira o **URL do locatário** e **Token secreto** da sua conta do Zscaler Beta, conforme descrito na etapa 6.

6. Para obter o **URL do locatário** e **Token secreto**, navegue até **Administração > configurações de autenticação** na interface de usuário do portal do Zscaler Beta e clique em  **SAML** sob **tipo de autenticação**. 

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)
    
    Clique em **configurar SAML** para abrir o **configuração SAML** opções. 

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)
    
    Selecione **Enable SCIM-Based provisionamento** para recuperar **URL de Base** e **Token de portador**, em seguida, salve as configurações. Cópia de **Base URL** para **URL do locatário**, e **Token de portador** para **Token secreto** no portal do Azure.

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Zscaler Beta. Se a conexão falhar, verifique se que sua conta do Zscaler Beta tem permissões de administrador e tente novamente.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/test-connection.png)
    
8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Zscaler Beta**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Zscaler Beta na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Zscaler Beta**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Zscaler Beta na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Zscaler Beta para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço Zscaler beta de provisionamento do AD do Azure, altere o **Status de provisionamento** para **na** no **configurações** seção.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definir os usuários e/ou grupos que você gostaria para provisionar o Zscaler beta, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Zscaler Beta](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no Zscaler Beta de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
