---
title: 'Tutorial: Configurar ThousandEyes para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no ThousandEyes.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f008e981abb11a4927ec045c33342bbac9a05bd8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436788"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configurar ThousandEyes para provisionamento automático de usuário


O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no ThousandEyes e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ThousandEyes. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory
*   Um ativo [conta do ThousandEyes](https://www.thousandeyes.com/pricing)
*   Uma conta de usuário do ThousandEyes que foi atribuída uma função que inclui as seguintes permissões de 3:
    * Exibir todos os usuários
    * Editar usuário
    * Permissões de acesso de API

> [!NOTE]
> Integração de provisionamento do Azure AD depende de [API de SCIM do ThousandEyes](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK_ThousandEyes-support-for-SCIM). 

## <a name="assigning-users-to-thousandeyes"></a>Atribuição de usuários ao ThousandEyes

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, é necessário decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo ThousandEyes. Depois de decidir, atribua esses usuários ao seu aplicativo ThousandEyes seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir usuários ao ThousandEyes

*   Recomendamos a atribuição de um único usuário do Azure AD ao ThousandEyes para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao ThousandEyes, você deve selecionar o **usuário** função ou outra válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários são ignorados.

## <a name="configure-auto-provisioned-user-roles-in-thousandeyes"></a>Configurar funções de usuário provisionado automaticamente no ThousandEyes

Para cada grupo de conta, são o provisionamento automático usuários em que você podem configurar um conjunto de funções a serem aplicadas quando a nova conta de usuário é criada. Por padrão, o provisionamento automático de usuários são atribuídos a _usuário Regular_ função para todas as contas de grupos, a menos que configurado de outra forma.

1. Para especificar um novo conjunto de funções para usuários provisionados em log ThousandEyes e navegue até a seção de configurações de SCIM **> seu ícone no canto superior direito de usuário > configurações da conta > organização > segurança e autenticação.** 

   ![Navegue até configurações de API de SCIM](https://monosnap.com/file/kqY8Il7eysGFAiCLCQWFizzM27PiBG)

2. Adicione uma entrada para cada grupo de contas, atribua um conjunto de funções, em seguida, *salvar* suas alterações.

   ![Definir funções padrão e os grupos de conta para os usuários criados por meio da API de SCIM](https://monosnap.com/file/16siam6U8xDQH1RTnaxnmIxvsZuNZG)


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configuração do provisionamento de usuários no ThousandEyes 

Esta seção explica como conectar seu Azure AD à API de provisionamento de conta de usuário do ThousandEyes e como configurar o serviço de provisionamento a fim de criar, atualizar e desabilitar contas de usuário atribuídas no ThousandEyes com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML Single Sign-On (SSO) para o ThousandEyes, seguindo a [instruções fornecidas na base de conhecimento do Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-tutorial) para concluir o SSO. O SSO pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o ThousandEyes no Azure AD


1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o ThousandEyes para logon único, pesquise sua instância do ThousandEyes usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **ThousandEyes** na galeria de aplicativos. Selecione o ThousandEyes nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do ThousandEyes e selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Sob o **credenciais de administrador** seção, de entrada a **Token de portador OAuth** gerado pela conta dos ThousandEyes (você pode encontrar e ou gerar um token em sua conta do ThousandEyes  **Criar o perfil** seção).

    ![Provisionamento do ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No Portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao seu aplicativo ThousandEyes. Se a conexão falhar, verifique se sua conta do ThousandEyes tem permissões de Administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção “Enviar uma notificação por email quando ocorrer uma falha”.

8. Clique em **Salvar**. 

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o ThousandEyes**.

10. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para o ThousandEyes. Os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no ThousandEyes para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD para o ThousandEyes, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

12. Clique em **Salvar**. 

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao ThousandEyes na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
