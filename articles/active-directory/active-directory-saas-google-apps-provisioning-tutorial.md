---
title: "Tutorial: Configurando o Google Apps para o provisionamento automático de usuário no Azure | Microsoft Docs"
description: "Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: b061f0ddad70be4a5ca48d48d1a737d6af8afa8d
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="tutorial-configuring-google-apps-for-automatic-user-provisioning"></a>Tutorial: Configurando o Google Apps para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no Google Apps e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Google Apps.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   É necessário ter um locatário válido do Google Apps for Work ou do Google Apps for Education. Você pode usar uma conta de avaliação gratuita para qualquer serviço.
*   Uma conta de usuário no Google Apps com permissões de Administrador de Equipe.

## <a name="assigning-users-to-google-apps"></a>Atribuindo usuários ao Google Apps

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Google Apps. Depois de decidir, atribua esses usuários ao aplicativo Google Apps seguindo estas instruções: [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

> [!IMPORTANT]
>*   Recomendamos a atribuição de um único usuário do Azure AD ao Google Apps para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.
>*   Ao atribuir um usuário ao Google Apps, é necessário selecionar a função Usuário ou “Grupo” na caixa de diálogo de atribuição. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do Google Apps e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Google Apps, com base na atribuição de usuário e de grupo do Azure AD.

>[!Tip]
>Você também pode optar por habilitar o Logon Único baseado em SAML no Google Apps, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

> [!NOTE]
> Outra opção viável para automatizar o provisionamento de usuários para o Google Apps é usar a [Ferramenta de Sincronização do Google Apps (GADS)](https://support.google.com/a/answer/106368?hl=en) , que provisiona suas identidades do Active Directory local para o Google Apps. Por outro lado, a solução neste tutorial provisiona seus usuários do Active Directory do Azure (nuvem) e grupos habilitados para email para o Google Apps. 

1. Entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador e clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.
   
    ![Clique em Segurança.][10]

2. Na página **Segurança**, clique em **Referência de API**.
   
    ![Clique em Referência de API.][15]

3. Selecione **Habilitar o acesso à API**.
   
    ![Clique em Referência de API.][16]

    > [!IMPORTANT]
    > Para cada usuário que você pretende provisionar para o Google Apps, seu nome de usuário no Active Directory do Azure *deve* estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários parecidos com bob@contoso.onmicrosoft.com não são aceitos pelo Google Apps, enquanto bob@contoso.com é aceito. É possível alterar o domínio de um usuário existente editando as propriedades dele no AD do Azure. Instruções sobre como definir um domínio personalizado para o Azure Active Directory e o Google Apps estão incluídas nas etapas a seguir.
      
4. Se você ainda não adicionou um nome de domínio personalizado ao Azure Active Directory, siga as seguintes etapas:
  
    a. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, clique em **Active Directory**. Na lista de diretórios, selecione o diretório. 

    b. Clique em **Nome de domínios** no painel de navegação à esquerda e, depois, clique em **Adicionar**.
     
     ![domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![adição de domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Digite seu nome de domínio no campo **Nome de domínio** . Esse nome de domínio deve ser o mesmo nome de domínio que você pretende usar para o Google Apps. Quando estiver pronto, clique no botão **Adicionar Domínio**.
     
     ![nome de domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Clique em **Avançar** para ir até a página de verificação. Para verificar se você possui esse domínio, é preciso editar os registros DNS do domínio de acordo com os valores fornecidos nesta página. É possível optar por verificar usando **Registros MX** ou **Registros TXT**, dependendo do que você selecionar na opção **Tipo de Registro**. Para obter instruções mais abrangentes sobre como confirmar o nome de domínio com o Azure AD, consulte [Adicionar seu próprio nome de domínio ao Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Repita as etapas anteriores para todos os domínios que você pretende adicionar ao diretório.

5. Agora que você confirmou todos os domínios com o Azure AD, agora deve confirmá-los novamente com o Google Apps. Para cada domínio que já não estiver registrado com o Google Apps, realize as etapas a seguir:
   
    a. No [Console de Administrador do Google Apps](http://admin.google.com/), clique em **Domínios**.
     
     ![Clique em Domínios][20]

    b. Clique em **Adicionar um domínio ou um alias de domínio**.
     
     ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio**e digite o nome do domínio que você deseja adicionar.
     
     ![Digite o nome de domínio][22]

    d. Clique em **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como confirmar seu domínio com o Google Apps, consulte. [Confirmar a propriedade do site com o Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para os domínios adicionais que você pretende adicionar ao Google Apps.
     
     > [!WARNING]
     > Se você alterar o domínio primário do locatário do Google Apps e já tiver configurado o logon único com o Azure AD, precisará repetir a etapa 3 em [Etapa 2: Habilitar Logon Único](#step-two-enable-single-sign-on).
       
6. No [Console de Administrador do Google Apps](http://admin.google.com/), clique em **Funções de Administrador**.
   
     ![Clique em Google Apps][26]

7. Determine a conta de administrador que você gostaria de usar para gerenciar o provisionamento de usuários. Para a **função de administrador** dessa conta, edite os **Privilégios** para essa função. Verifique se ela tem todos os **Privilégios de API de Administrador** habilitados, para que essa conta possa ser usada para o provisionamento.
   
     ![Clique em Google Apps][27]
   
    > [!NOTE]
    > Se estiver configurando um ambiente de produção, a melhor prática será criar uma conta do administrador no Google Apps especificamente para esta etapa. Essas contas devem ter uma função de administrador associada a elas que tem os privilégios de API necessários.
     
8. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

9. Se você já tiver configurado o Google Apps para logon único, pesquise a instância do Google Apps usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Google Apps** na galeria de aplicativos. Selecione o Google Apps nos resultados da pesquisa e adicione-o à lista de aplicativos.

10. Selecione a instância do Google Apps e, depois, a guia **Provisionamento**.

11. Defina o **Modo de Provisionamento** como **Automático**. 

     ![provisionamento](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Isso abre uma caixa de diálogo de autorização do Google Apps em uma nova janela do navegador.

13. Confirme que você deseja dar permissão ao Active Directory do Azure para fazer alterações em seu locatário do Google Apps. Clique em **Aceitar**.
    
     ![Confirme permissões.][28]

14. No portal do Azure, clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao aplicativo Google Apps. Se a conexão falhar, verifique se sua conta do Google Apps tem permissões de Administrador de Equipe e repita a etapa **“Autorizar”**.

15. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

16. Clique em **Salvar.**

17. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Google Apps.**

18. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Google Apps. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Google Apps em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

19. Para habilitar o serviço de provisionamento do Azure AD no Google Apps, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

20. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Google Apps na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Google Apps.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
