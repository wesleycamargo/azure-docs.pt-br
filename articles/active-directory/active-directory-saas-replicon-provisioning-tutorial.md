---
title: 'Tutorial: Configurar o Replicon para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Replicon.
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
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 0ff0bcffae506e593f8ddc95886142df30164e65
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293272"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Tutorial: Configurar o Replicon para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Replicon e no Azure AD (Azure Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Replicon.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure AD
*   Um locatário do Replicon com o plano [Plus](https://www.replicon.com/time-bill-pricing/) ou outro com mais recursos, habilitado
*   Uma conta de usuário no Replicon com permissões de Administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do Replicon](https://www.replicon.com/help/developers), disponível para as equipes do Replicon no plano Plus ou outro com mais recursos.

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon a partir da galeria
Antes de configurar o Replicon para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Replicon por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Replicon por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]
    
3. Para adicionar o Replicon, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Replicon**.

    ![Pesquisa de aplicativos do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. No painel de resultados, selecione **Replicon** e, em seguida, clique no botão **Adicionar** para adicionar o Replicon à lista de aplicativos SaaS.

    ![Resultados da pesquisa do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Caixa de diálogo Criar Aplicativo do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Atribuindo usuários ao Replicon

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, é necessário decidir quais usuários e/ou grupos no Azure AD precisam acessar o Replicon. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Replicon seguindo estas instruções:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Dicas importantes para atribuir usuários ao Replicon

*   É recomendável que um único usuário do Azure AD seja atribuído ao Replicon para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Replicon, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Configurando o provisionamento automático de usuário no Replicon 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Replicon com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Replicon, seguindo as instruções fornecidas no [tutorial do logon único do Replicon](active-directory-saas-replicon-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Replicon no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione o Replicon na lista de aplicativos SaaS.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. Na seção **Credenciais de Administrador**, insira o **Nome de Usuário Administrador**, **Senha de Administrador** **CompanyId** e **Domínio** da conta do Replicon. Exemplos desses valores são:

    *   No campo **Nome de Usuário Administrador**, popule o nome de usuário da conta do administrador no locatário do Replicon. Exemplo: contosoadmin.

    *   No campo **Senha de Administrador**, preencha a senha correspondente ao nome de usuário do administrador.

    *   No campo **CompanyId**, popule a CompanyId de seu locatário do Replicon. Exemplo: a CompanyID baseada no logon abaixo é Contoso.

    ![Logon do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   No campo **Domínio**, popule o Domínio conforme descrito na Etapa 6.
    
6. Obtenha a **serviceEndpointRootURL** da conta de locatário do Replicon de acordo com as etapas mencionadas em [Ajuda do serviço do Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Depois de obter a URL, o **domínio** será o subdomínio de **serviceEndpointRootURL**, conforme realçado. 

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Ao popular os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao Replicon. Se a conexão falhar, verifique se a conta do Replicon tem permissões de Administrador e tente novamente.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Clique em **Salvar**.

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Replicon**.
    
    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Replicon na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados fazer a correspondência entre as contas de usuário no Replicon para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](./active-directory-saas-scoping-filters.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Replicon, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Defina os usuários e/ou grupos a serem provisionados para o Replicon escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Replicon.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
