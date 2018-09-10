---
title: 'Tutorial: Configurar Cisco para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e descontinuar automaticamente as contas de usuário para o Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: 76a83ef4f647dcf7d79218cb281f1f976b292870
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42139773"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cisco Webex para provisionamento automático de usuários


O objetivo deste tutorial é demonstrar as etapas a serem executadas no Cisco Webex e no Microsoft Azure Active Directory (Azure AD) para configurar o Microsoft Azure Active Directory para provisionar e desprovisionar usuários automaticamente para o Cisco Webex.


> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

*   Um locatário do Azure AD
*   Um locatário do Cisco Webex
*   Uma conta de usuário no Cisco Webex com permissões de administrador


> [!NOTE]
> A integração do provisionamento do Microsoft Azure Active Directory conta com o [ Cisco Webex Webservice ](https://developer.webex.com/getting-started.html), disponível para as equipes do Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando o Cisco Webex da galeria
Antes de configurar o Cisco Webex para o provisionamento automático de usuários com o Microsoft Azure Active Directory, você precisa adicionar o Cisco Webex da galeria de aplicativos do Microsoft Azure Active Directory à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Webex da galeria de aplicativos do Microsoft Azure Active Directory, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]

3. Para adicionar o Cisco Webex, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Cisco Webex**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. No painel de resultados, selecione **Cisco Webex** e, em seguida, clique no botão **Adicionar** para adicionar o Cisco Webex à sua lista de aplicativos SaaS.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Atribuindo usuários ao Cisco Webex

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e ativar o provisionamento automático de usuários, você deve decidir quais usuários do Microsoft Azure Active Directory precisam acessar o Cisco Webex. Uma vez decidido, você pode atribuir esses usuários ao Cisco Webex seguindo as instruções aqui:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Dicas importantes para atribuir usuários ao Cisco Webex

*   Recomenda-se que um único usuário do Microsoft Azure Active Directory seja atribuído ao Cisco Webex para testar a configuração automática de provisionamento de usuários. Outros usuários podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Cisco Webex, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configurando o Fornecimento Automático de Usuários para o Cisco Webex

Esta seção o guia pelas etapas para configurar o serviço de provisionamento do Microsoft Azure Active Directory para criar, atualizar e desabilitar usuários no Cisco Webex com base nas atribuições de usuários no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Para configurar o provisionamento automático de usuários para o Cisco Webex no Microsoft Azure Active Directory:


1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione Cisco Webex na sua lista de aplicativos SaaS.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **Credenciais de administrador**, insira o **URL do locatário** e **Token secreto** da conta do seu Cisco Webex.

    *   No campo **URL do locatário**, preencha o URL da API do Cisco Webex SCIM para seu locatário, que assume a forma de `https://api.webex.com/v1/scim/[Tenant ID]/`, em que `[Tenant ID]` é uma sequência alfanumérica, conforme descrito na etapa 6.

    *   No campo **Token Secreto**, preencha o token secreto conforme descrito na etapa 6.

1. O **ID do Locatário** e o **Token Secreto** para a sua conta do Cisco Webex podem ser encontrados fazendo login no [site do desenvolvedor do Cisco Webex](https://developer.webex.com/) com sua conta de Administrador. Uma vez logado -
    * Vá até a página [Introdução](https://developer.webex.com/getting-started.html)
    * Role para baixo até a [Seção de Autenticação](https://developer.webex.com/getting-started.html#authentication)
    ![Token de Autenticação do Cisco Webex](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * A string alfanumérica na caixa é seu **Token Secreto**. Copie este token para a área de transferência
    * Vá para a página [Obter meus próprios detalhes](https://developer.webex.com/endpoint-people-me-get.html)
        * Certifique-se de que o modo de teste esteja ativado
        * Digite a palavra "Bearer" seguida de um espaço e cole o Token secreto no campo Authorization ![Token de autenticação do Cisco Webex](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
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

1. Ao preencher os campos mostrados na Etapa 5, clique em **Testar Conexão** para garantir que o Microsoft Azure Active Directory possa se conectar ao Cisco Webex. Se a conexão falhar, certifique-se de que sua conta do Cisco Webex tenha permissões de administrador e tente novamente.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Microsoft Azure Active Directory para o Cisco Webex**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Revise os atributos do usuário sincronizados do Microsoft Azure Active Directory para o Cisco Webex na seção **Mapeamento de Atributos**. Os atributos selecionados como **Matching** são usados para corresponder às contas de usuário no Cisco Webex para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../active-directory-saas-scoping-filters.md).

13. Para habilitar o serviço de provisionamento do Microsoft Azure Active Directory para o Cisco Webex, altere o **Status de provisionamento** para **ativar** na seção **Configurações**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Defina os usuários e / ou grupos que você deseja provisionar para o Cisco Webex escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Cisco Webex](./media/cisco-webex-provisioning-tutorial/Save.png)


Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **Detalhes da Sincronização** para monitorar o andamento e seguir os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Microsoft Azure Active Directory no Cisco Webex.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Cisco Webex está atualmente na fase Early Field Testing (EFT) da Cisco. Para obter mais informações, entre em contato com [equipe de suporte do Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png
