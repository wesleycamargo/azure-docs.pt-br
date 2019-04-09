---
title: 'Tutorial: Configurar o Zendesk para provisionamento automático do usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278574"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para provisionamento automático do usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zendesk e no Microsoft Azure Active Directory (Azure Active Directory) para configurar o Microsoft Azure Active Directory para provisionar e desprovisionar automaticamente usuários e/ou grupos no Zendesk.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um locatário do Zendesk com o [plano Enterprise](https://www.zendesk.com/product/pricing/) ou outro melhor habilitado
* Uma conta de usuário no Zendesk com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API Rest do Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), disponível para as equipes do Zendesk no plano Enterprise ou outro melhor.

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria

Antes de configurar o Zendesk para o provisionamento automático de usuário com o Microsoft Azure Active Directory, é necessário adicionar o Zendesk por meio da galeria de aplicativos do Microsoft Azure Active Directory à lista de aplicativos SaaS gerenciados.

**Para adicionar o Zendesk da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zendesk**, selecione **Zendesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Atribuição de usuários ao Zendesk

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Microsoft Azure Active Directory precisam acessar o Zendesk. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zendesk seguindo estas instruções:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir usuários ao Zendesk

* Funções do Zendesk são populadas dinâmica e automaticamente na interface do usuário do portal do Azure hoje. Antes de atribuir funções do Zendesk aos usuários, certifique-se de que uma sincronização inicial seja concluída no Zendesk para recuperar as funções mais recentes em seu locatário do Zendesk.

* É recomendável que um único usuário do Azure AD seja atribuído ao Zendesk para testar sua configuração inicial de provisionamento automático do usuário. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.
  
* É recomendável que um único usuário do Microsoft Azure Active Directory seja atribuído ao Zendesk para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zendesk, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configurando o provisionamento automático de usuário no Zendesk 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Microsoft Azure Active Directory para criar, atualizar e desabilitar usuários e/ou grupos no Zendesk com base em atribuições de usuário e/ou grupo no Microsoft Azure Active Directory.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Zendesk, seguindo as instruções fornecidas no [tutorial do logon único do Zendesk](zendesk-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Zendesk no Microsoft Azure Active Directory:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zendesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zendesk**.

    ![O link do Zendesk na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Na seção **Credenciais de Administrador**, insira o **Nome de Usuário do Administrador**, o **Token Secreto** e o **Domínio** da conta do Zendesk. Exemplos desses valores são:

   * No campo **Nome de Usuário Administrador**, popule o nome de usuário da conta do administrador no locatário do Zendesk. Exemplo: admin@contoso.com.

   * No campo **Token Secreto**, preencha o token secreto conforme descrito na Etapa 6.

   * No campo **Domínio**, preencha o subdomínio do seu locatário do Zendesk.
     Exemplo: Para uma conta com uma URL de locatário de `https://my-tenant.zendesk.com`, o subdomínio seria **my-tenant**.

6. O **Token Secreto** da sua conta Zendesk está localizado em **Administrador > API > Configurações**.
   Certifique-se de que o **Token de acesso** está definido como **Habilitado**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Ao popular os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Microsoft Azure Active Directory pode se conectar ao Zendesk. Se a conexão falhar, verifique se sua conta do Zendesk tem permissões de Administrador e tente novamente.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Zendesk**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Examine os atributos de usuário que serão sincronizados do Microsoft Azure Active Directory para o Zendesk na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Zendesk para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Zendesk**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Examine os atributos de grupo que serão sincronizados do Microsoft Azure Active Directory para o Zendesk na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder os grupos do Zendesk em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Microsoft Azure Active Directory para o Zendesk, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os usuários e/ou grupos a serem provisionados para o Zendesk escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Microsoft Azure Active Directory no Zendesk.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Zendesk dá suporte ao uso de grupos para Usuários com funções somente de Agente. Para obter mais informações, consulte a [documentação da Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um usuário e/ou grupo, o serviço de provisionamento automático de usuários do Azure AD também atribui a função padrão **Agente**. Somente **Agentes** podem ser atribuídos a uma função personalizada. Para obter mais informações, consulte esta [documentação da API Zendesk ](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como examinar os logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
