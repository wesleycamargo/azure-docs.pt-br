---
title: 'Tutorial: Configurar o Cisco Spark para o provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Cisco Spark.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77dab6ad0480bc1565c219766d17211995dcfc20
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278405"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cisco Spark para provisionamento automático de usuários

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Cisco Spark e no Azure AD (Azure AD) para configurar o Azure AD para provisionar e desprovisionar usuários automaticamente para o Cisco Spark.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um inquilino da Cisco Spark
* Uma conta de usuário no Cisco Spark com permissões de administrador

> [!NOTE]
> A integração do provisionamento do Azure AD conta com o [ Cisco Spark Webservice ](https://developer.webex.com/getting-started.html), disponível para as equipes do Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Adicionando o Cisco Spark da galeria

Antes de configurar o Cisco Spark para o provisionamento automático de usuários com o Azure AD, você precisa adicionar o Cisco Spark da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Spark da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco Spark**, selecione **Cisco Spark** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Cisco Spark na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cisco-spark"></a>Atribuindo usuários ao Cisco Spark

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e ativar o provisionamento automático de usuários, você deve decidir quais usuários do Azure AD precisam acessar o Cisco Spark. Uma vez decidido, você pode atribuir esses usuários ao Cisco Spark seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Dicas importantes para atribuir usuários ao Cisco Spark

* Recomenda-se que um único usuário do AD do Azure seja atribuído ao Cisco Spark para testar a configuração automática de provisionamento de usuários. Outros usuários podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Cisco Spark, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Configurando o Fornecimento Automático de Usuários para o Cisco Spark

Esta seção o guia pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários no Cisco Spark com base nas atribuições de usuários no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Para configurar o provisionamento automático de usuários para o Cisco Spark no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Cisco Spark**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Cisco Spark**.

    ![O link do Cisco Spark na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de administrador**, insira o **URL do locatário** e **Token secreto** da conta do seu Cisco Spark.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/secrettoken1.png)

    * No campo **URL do locatário**, preencha o URL da API do Cisco Spark SCIM para seu locatário, que assume a forma de `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, em que `[Tenant ID]` é uma sequência alfanumérica, conforme descrito na etapa 6.

    * No campo **Secret Token**, preencha o token secreto, conforme descrito na etapa 6.

6. O **ID do Locatário** e o **Token Secreto** para a sua conta do Cisco Spark podem ser encontrados fazendo login no [site do desenvolvedor do Cisco Spark](https://developer.webex.com/) com sua conta de Administrador. Uma vez logado -

   * Vá até a página [Introdução](https://developer.webex.com/getting-started.html)

   * Role para baixo até a [seção de autenticação](https://developer.webex.com/getting-started.html#authentication)
  
    ![Cisco Spark Authentication Token](./media/cisco-spark-provisioning-tutorial/SecretToken.png)

   * A string alfanumérica na caixa é seu **Token Secreto**. Copie este token para a área de transferência

   * Vá para a página [Obter meus próprios detalhes](https://developer.webex.com/endpoint-people-me-get.html)
       * Certifique-se de que o modo de teste esteja ativado
       * Digite a palavra "Bearer" seguida de um espaço e cole o Token secreto no campo Authorization ![Token de autenticação do Cisco Spark](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Clique em Executar

   * No texto da resposta para a direita, o **ID de locatário** aparece como "orgId":

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

7. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao Cisco Spark. Se a conexão falhar, certifique-se de que sua conta do Cisco Spark tenha permissões de administrador e tente novamente.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Active Directory do Azure para o Cisco Spark**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Revise os atributos do usuário sincronizados do Azure AD para o Cisco Spark na seção **Mapeamento de Atributos**. Os atributos selecionados como **Matching** são usados para corresponder às contas de usuário no Cisco Spark para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Cisco Spark, altere o **Status de provisionamento** para **ativar** na seção **Configurações**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Defina os usuários e / ou grupos que você deseja provisionar para o Cisco Spark escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Cisco Spark](./media/cisco-spark-provisioning-tutorial/Save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes da Sincronização** para monitorar o andamento e seguir os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Cisco Spark.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Cisco Spark está atualmente na fase EFT (Early Field Testing, teste de campo antecipado) da Cisco. Para obter mais informações, entre em contato com [equipe de suporte do Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximos passos

* [Saiba como examinar os logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
