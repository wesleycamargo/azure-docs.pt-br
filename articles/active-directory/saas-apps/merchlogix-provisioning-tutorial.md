---
title: 'Tutorial: Configurar o MerchLogix para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário ao MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe36969661ae1b729601681c02f79e777b2f8cab
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344194"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configurar o MerchLogix para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no MerchLogix e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e/ou grupos ao MerchLogix. 

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

*   Um locatário do Azure AD
*   Um locatário do MerchLogix
*   Um contato técnico no MerchLogix que possa fornecer a URL de ponto de extremidade SCIM e o token de segredo necessário para o provisionamento de usuário

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionando MerchLogix a partir da Galeria
Antes de configurar o MerchLogix para provisionamento com o Azure AD automático de usuário, você precisará adicionar o MerchLogix da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MerchLogix da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Seção de Aplicativos empresariais][2]
    
3. Para adicionar o MerchLogix, clique o **novo aplicativo** botão na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix**e, em seguida, clique no **Add** botão para adicionar o MerchLogix à sua lista de aplicativos SaaS.

    ![Provisionamento do MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuir usuários ao MerchLogix

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Microsoft Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao MerchLogix. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao MerchLogix, seguindo as instruções aqui:

*   [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir usuários ao MerchLogix

*    É recomendável que um único usuário do Azure AD seja atribuído ao MerchLogix para testar sua configuração de provisionamento de usuário automático inicial. Outros usuários e/ou grupos podem ser atribuídos posteriormente, após os testes serem bem-sucedidos.

*   Ao atribuir um usuário ao MerchLogix, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurando o provisionamento automático de usuário ao MerchLogix 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no MerchLogix com base nas atribuições de usuário e/ou grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML single sign-on para MerchLogix, seguindo as instruções fornecidas na [tutorial do logon único MerchLogix](merchlogix-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o MerchLogix no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory > Aplicativos empresariais > Todos os aplicativos**.

2. Selecione o MerchLogix na lista de aplicativos SaaS.

3. Selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Sob o **credenciais de administrador** seção:

    *   No **URL do locatário** , insira a URL de ponto de extremidade do SCIM fornecida pelo seu contato técnico do MerchLogix.

    *   No **segredo do Token** , insira o segredo token fornecido pelo seu contato técnico do MerchLogix.

6. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao MerchLogix. Se a conexão falhar, verifique se que sua conta do MerchLogix tem permissões de administrador e tente novamente.

    
7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

8. Clique em **Salvar**.

9. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao MerchLogix**.

10. Examine os atributos de usuário que são sincronizados do Azure AD ao MerchLogix na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

11. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao MerchLogix**.

12. Examine os atributos de grupo que são sincronizados do Azure AD ao MerchLogix na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no MerchLogix para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

13. Para habilitar o Azure AD MerchLogix serviço de provisionamento, altere o **Status de provisionamento** para **na** no **configurações** seção.

14. Quando estiver pronto para provisionar, clique em **Salvar**.


Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no MerchLogix de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
