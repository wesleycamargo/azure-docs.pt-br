---
title: "Tutorial: integração do Azure Active Directory com o Box | Microsoft Docs"
description: "Saiba como usar o Box com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5f3517f8-30f2-4be7-9e47-43d702701797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 6e6dad2dba03bd3339d841c885ab4e60b2dafbd3
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Integração do Active Directory do Azure ao Box
O objetivo deste tutorial é mostrar a integração do Azure ao Box  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário de teste no Box

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Box poderão fazer logon único no aplicativo em seu site de empresa do Box (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Box
* Configuração do SSO (logon único)
* Configurando o provisionamento de grupo e usuário
* Atribuindo usuários

![Cenário](./media/active-directory-saas-box-tutorial/IC769537.png "Cenário")

## <a name="enable-the-application-integration-for-box"></a>Habilitar a integração de aplicativos para o Box
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Box.

**Para habilitar a integração de aplicativos para o Box, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-box-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-box-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-box-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Box**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-box-tutorial/IC701023.png "Galeria de aplicativos")
7. No painel de resultados, selecione **Box** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## <a name="configure-single-sign-on"></a>Configurar o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Box com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. 

Como parte desse procedimento, é necessário carregar metadados no Box.com.

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Box**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769538.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Box**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769539.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, a caixa de texto **URL de Locatário do Box**, digite a URL de locatário do Box (por exemplo: https://<mydomainname>.box.com) e clique em **Avançar**.
   
  ![Configurar URL do Aplicativo](./media/active-directory-saas-box-tutorial/IC669826.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Box**, para baixar os metadados, clique em **Baixar metadados** e no arquivo de dados localmente no computador.
   
  ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC669824.png "Configurar logon único")
5. Encaminhe esse arquivo de metadados à equipe de suporte do Box. A equipe de suporte precisa configurar o logon único para você.
6. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
  ![Configurar logon único](./media/active-directory-saas-box-tutorial/IC769540.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Box.

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Box**, clique em **Configurar provisionamento de usuários** para abrir a caixa de diálogo **Configurar Provisionamento de Usuários**. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar o provisionamento de usuário automático")
2. Na página de caixa de diálogo **Habilitar provisionamento de usuário no Bo**, clique em **Habilitar provisionamento de usuário**. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar o provisionamento de usuário automático")
3. Na página **Fazer logon para conceder acesso ao Box**, forneça as credenciais necessárias e clique em **Autorizar**. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar o provisionamento de usuário automático")
4. Clique em **Conceder acesso à Caixa** para autorizar essa operação e retornar ao Portal clássico do Azure. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar o provisionamento de usuário automático")
5. Na página **Opções de Provisionamento**, as caixas de seleção **Tipos de objeto a provisionar** permitem que você selecione se os objetos de grupo são ou não provisionados para o Box além dos objetos de usuário.  Consulte a seção "Atribuindo usuários e grupos" abaixo para obter mais informações.
6. Para concluir a configuração, clique no botão Concluir. 
   
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar o provisionamento de usuário automático")

## <a name="assign-a-test-user"></a>Atribuir um usuário de teste
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Box, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **Box**, clique em **Atribuir usuários**. 
   
    ![Atribuir usuários](./media/active-directory-saas-box-tutorial/IC769552.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição. 
   
   ![Sim](./media/active-directory-saas-box-tutorial/IC767830.png "Sim")

Agora, você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o Box.

Como uma primeira etapa de verificação, é possível verificar o status de provisionamento clicando em Painel no D na página de integração do aplicativo Box do portal clássico do Azure.

![Painel](./media/active-directory-saas-box-tutorial/IC769553.png "Painel")

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado:

![Status da integração](./media/active-directory-saas-box-tutorial/IC769555.png "Status da integração")

Em seu locatário do Box, os usuários sincronizados estão listados em **Usuários Gerenciados** no **Console do Administrador**.

![Status da integração](./media/active-directory-saas-box-tutorial/IC769556.png "Status da integração")

## <a name="assign-users-and-groups"></a>Atribuir usuários e grupos
A guia **Box > Usuários e Grupos** no portal clássico do Azure permite que você especifique quais usuários e grupos devem ter acesso ao Box. A atribuição de um usuário ou grupo faz com que as seguintes ações ocorram:

* O Azure AD permite que o usuário atribuído (seja por atribuição direta ou associação de grupo) realize a autenticação no Box. Se um usuário não for atribuído, o Azure AD não permitirá que eles entrem no Box e retornará um erro na página de entrada do Azure AD.
* Um bloco de aplicativo do Box é adicionado ao [iniciador do aplicativo](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)do usuário.
* Se o provisionamento automático estiver habilitado, os usuários e/ou grupos atribuídos serão adicionados à fila de provisionamento para serem provisionado automaticamente.
  
  * Se apenas os objetos de usuário foram configurados para serem provisionados, todos os usuários atribuídos diretamente serão colocados na fila de provisionamento e todos os usuários que são membros de grupos atribuídos serão colocados na fila de provisionamento. 
  * Se os objetos de grupo foram configurados para serem provisionados, todos os objetos de grupo atribuídos serão provisionados para o Box, bem como todos os usuários que são membros desses grupos. As associações de grupo e usuário são preservadas ao serem gravadas no Box.

Você pode usar a guia **Atributos > Logon único** para configurar quais atributos de usuário (ou declarações) são apresentadas ao Box durante a autenticação baseada em SAML e a guia **Atributos > Provisionamento** para configurar como os atributos de usuário e grupo fluem do Azure AD para o Box durante as operações de provisionamento. Consulte os recursos abaixo para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais
* [Personalizando as declarações emitidas no token SAML](active-directory-saml-claims-customization.md)
* [Provisioning: Customize Attribute Mappings (Provisionamento: personalizar mapeamentos de atributos)](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


