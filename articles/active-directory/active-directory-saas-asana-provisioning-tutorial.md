---
title: "Tutorial: Configurar o Asana para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Asana."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configurar o Asana para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa realizar no Asana e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para Asana.

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário de diretório do Azure Active Directory
*   Um locatário do Asana com o plano [Enterprise](https://www.asana.com/pricing) ou melhor habilitado 
*   Uma conta de usuário no Asana com permissões de administrador 

> [!NOTE] 
> A integração do provisionamento do Azure AD depende da [API da Asana](https://app.asana.com/api/1.0/scim/Users) que está disponível para Asana.

## <a name="assigning-users-to-asana"></a>Atribuir usuários ao Asana

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto de provisionamento automático de conta de usuário, apenas os usuários que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais os usuários no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Asana. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo Asana seguindo as instruções aqui:

[Atribuir um usuário a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir usuários ao Asana

*   É recomendável que um único usuário do Azure AD seja atribuído ao Asana para testar a configuração de provisionamento. Outros usuários podem ser atribuídos mais tarde.

## <a name="configuring-user-provisioning-to-asana"></a>Configurar o provisionamento de usuário para Asana 

Esta seção mostra como conectar o Azure AD para a API de provisionamento de conta de usuário do Asana e configurar o serviço de provisionamento para criar, atualizar e desativar contas de usuário atribuídas no Asana com base na atribuição do usuário no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para Asana, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o provisionamento automático da conta do usuário para Asana no Azure AD:

1)  No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2) Se você já tiver configurado o Asana para logon único, procure sua instância do Asana usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Asana** na galeria de aplicativos. Selecione **Asana** nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

3)  Selecione sua instância do Asana e a guia **Provisionamento**.

4)  Defina o **Modo de Provisionamento** como **Automático**.

![Provisionamento do Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) Na seção de Credenciais de administrador, siga as instruções abaixo para gerar o token e insira-a na caixa de texto **Token secreto**.

* Faça logon no [Asana](https://app.asana.com) usando a conta de administrador
* Clique na foto do perfil da barra superior e selecione Configurações atuais do nome da organização
* Navegue até a guia Contas de serviço
* Clique em Adicionar conta de serviço
* Atualize o Nome, Sobre e a foto de perfil, conforme necessário, copie o **Token** e clique em Salvar alterações

6) No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao seu aplicativo Asana. Se a conexão falhar, certifique-se de que sua conta do Asana possui permissões de administrador e tente a etapa **Testar conectividade** novamente.

7) Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

8) Clique em **Salvar**. 

9) Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory para o Asana**.

10) Na seção **Mapeamentos de atributo**, verifique os atributos do usuário que serão sincronizados entre o Azure AD e o Asana. Observe que os atributos selecionados como propriedades de **Correspondência** serão usados para associação com as contas de usuário no Asana para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações. Consulte [Personalizando mapeamentos de atributo de provisionamento de usuário](active-directory-saas-customizing-attribute-mappings.md) para saber mais detalhes

11) Para habilitar o serviço de provisionamento do Azure AD para Asana, altere o **Status de provisionamento** para **Ativado** na seção **Configurações**

12) Clique em **Salvar**. 

Isso inicia a sincronização inicial de todos os usuários atribuídos ao Asana na seção usuários. As sincronizações iniciais demoram mais do que as sincronizações posteriores, que ocorrem aproximadamente a cada 20 minutos desde que o serviço esteja em execução. Você pode usar a seção **Detalhes de sincronização** para monitorar o andamento e seguir os links para relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no seu aplicativo Asana.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-asana-tutorial.md)
