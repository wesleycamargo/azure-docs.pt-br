---
title: "Tutorial: Configurar Pingboard para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário automaticamente para o Pingboard."
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
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configurar Pingboard para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar para habilitar o provisionamento e desprovisionamento automáticos e de contas de usuário do Azure Active Directory para o Pingboard.

## <a name="prerequisites"></a>pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory
*   Uma [Conta Pro](https://pingboard.com/pricing) do locatário do Pingboard 
*   Uma conta de usuário no Pingboard com permissões de administrador 

> [!NOTE] 
> A integração do provisionamento Azure Active Directory depende da API Pingboard (`https://your_domain.pingboard.com/scim/v2`) que está disponível para a sua conta.

## <a name="assigning-users-to-pingboard"></a>Atribuir usuários ao Pingboard

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto de provisionamento automático da conta do usuário, apenas os usuários que foram "atribuídos" a um aplicativo no Azure Active Directory são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários no Azure Active Directory representam os usuários que precisam de acesso ao seu aplicativo Pingboard. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo Pingboard seguindo as instruções aqui:

[Atribuir um usuário a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir usuários ao Pingboard

*   É recomendável que você atribua um único usuário do Azure Active Directory ao Pingboard para testar a configuração de provisionamento. Outros usuários podem ser atribuídos mais tarde.

## <a name="configuring-user-provisioning-to-pingboard"></a>Configurar o provisionamento de usuário para Pingboard 

Esta seção mostra como conectar seu Azure Active Directory à API de provisionamento de conta de usuário do Pingboard e configurar o serviço de provisionamento para criar, atualizar e desativar as contas de usuário atribuídas no Pingboard com base na atribuição do usuário no Azure Active Directory.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Pingboard, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Para configurar o provisionamento automático de conta de usuário para Pingboard no Azure Active Directory:

1)  No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2) Se você já tiver configurado o Pingboard para logon único, procure sua instância do Pingboard usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Pingboard** na galeria de aplicativos. Selecione **Pingboard** nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

3)  Selecione sua instância do Pingboard e selecione a guia **Provisionamento**.

4)  Defina o **Modo de Provisionamento** como **Automático**.

![Provisionamento de Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) Na seção “Credenciais de Administrador”, realize as seguintes etapas:

* Na caixa de texto **URL do locatário**, digite `https://your_domain.pingboard.com/scim/v2` e substitua seu_domínio pelo seu domínio real
* Faça logon no [Pingboard](https://pingboard.com/) usando a conta de administrador.
* Clique em Complementos > Integrações > Azure Active Directory.
* Clique na guia Configurar e selecione **Habilitar provisionamento de usuário do Azure**.
* Copiar o campo **Token de portador OAuth** e inserir na caixa de texto **Token secreto**.

6) No portal do Azure, clique em **Testar conexão** para garantir que o Azure Active Directory consiga se conectar ao seu aplicativo Pingboard. Se a conexão falhar, certifique-se de que sua conta Pingboard possui permissões de administrador e tente executar novamente a etapa **"Testar conexão"**.

7) Insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento no campo **Email de notificação** e marque a caixa de seleção a seguir.

8) Clique em **Salvar**. 

9) Na seção Mapeamentos, selecione **Sincronizar usuários do Azure Active Directory com o Pingboard**.

10) Na seção **Mapeamentos de atributo**, verifique os atributos de usuário a serem sincronizados entre o Azure Active Directory e o Pingboard. Os atributos selecionados como propriedades de **Correspondência** são usados para associar as contas de usuário no Pingboard para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações. Para obter mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md).

11) Para habilitar o serviço de provisionamento do Azure Active Directory para Pingboard, altere o **Status de provisionamento** para **Ligado** na seção **Configurações**

12) Clique em **Salvar** para iniciar a sincronização inicial de usuários atribuídos ao Pingboard.

As sincronizações iniciais demoram mais do que as sincronizações posteriores, que ocorrem aproximadamente a cada 20 minutos desde que o serviço esteja em execução. Você pode usar a seção **Detalhes da sincronização** para monitorar o andamento e seguir os links para provisionar relatórios de atividade, que descrevem todas as ações executadas pelo serviço de provisionamento no seu aplicativo do Pingboard.

Para obter mais informações sobre como ler os logs de provisionamento do Azure Active Directory, consulte [Relatórios sobre o provisionamento automático de conta de usuário](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-pingboard-tutorial.md)
