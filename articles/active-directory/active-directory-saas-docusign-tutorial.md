---
title: "Tutorial: integração do Azure Active Directory com o DocuSign | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6d2ab680afdd60732b8cf0a39a31ad6f35a70d09


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: integração do Active Directory do Azure com o DocuSign
O objetivo deste tutorial é mostrar a integração do Azure ao DocuSign.
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário no DocuSign

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. [Habilitando a integração de aplicativos para o DocuSign](#enabling-the-application-integration-for-docusign) 
2. [Configurando o logon único](#configuring-single-sign-on) 
3. [Configurando o provisionamento de contas](#configuring-account-provisioning) 
4. [Atribuindo usuários](#assigning-users) 
   
![Configurando o logon único][0]

## <a name="enabling-the-application-integration-for-docusign"></a>Habilitando a integração de aplicativos para o DocuSign
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o DocuSign, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Configurando o logon único][1]
2. Na lista Diretório, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Configurando o logon único][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo O que você deseja fazer, clique em **Adicionar um aplicativo da galeria**.
   
    ![Configurando o logon único][4]
6. Na caixa de pesquisa, digite **Docusign**.
   
    ![Configurando o logon único][5]
7. No painel de resultados, selecione **DocuSign** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Configurando o logon único][6]

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no DocuSign com sua conta do Azure AD usando federação baseada em protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de **integração do aplicativo DocuSign**, clique em **Configurar logon único** para abrir o diálogo Configurar Logon Único.
   
    ![Configurando o logon único][7]
2. Na página **Como você deseja que os usuários façam logon no DocuSign**, selecione **Logon Único do Microsoft Azure AD** e clique em Avançar.
   
    ![Configurando o logon único][8]
3. Na página **Definir Configurações do Aplicativo** , execute as seguintes etapas:
   
    ![Configurando o logon único][61]
   
    a. Na caixa de texto **URL de Entrada**, digite `https://account.docusign.com/*`.  
   
    b. Na caixa de texto **Identificador**, digite: `https://account.docusign.com/*`.  
   
    c. Clique em **Avançar**. 

    > [!TIP] 
    > A URL de Logon e os valores de identificador são apenas espaços reservados. Instruções sobre como recuperar os valores reais para seu ambiente são abordadas mais adiante neste tópico.


1. Na página **Configurar logon único no DocuSign**, clique em **Baixar certificado** e salve o arquivo de certificado localmente em seu computador.
   
    ![Configurando o logon único][10]
2. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **portal de administração do DocuSign** como administrador.
3. No menu de navegação à esquerda, clique em **Domínios**.
   
    ![Configurando o logon único][51]
4. No painel direito, clique em **Solicitar Domínio**.
   
    ![Configurando o logon único][52]
5. Na caixa de diálogo **Solicitar um domínio**, na caixa de texto **Nome de Domínio**, digite o domínio de sua empresa e clique em **Solicitar**. Verifique o domínio e confira se o status está ativo.
   
    ![Configurando o logon único][53]
6. No menu à esquerda, clique em **Provedores de Identidade**  
   
    ![Configurando o logon único][54]
7. No painel direito, clique em **Adicionar Provedor de Identidade**. 
   
    ![Configurando o logon único][55]
8. Na página **Configurações do Provedor de Identidade** , execute as seguintes etapas:
   
    ![Configurando o logon único][56]

    a. Na caixa de texto **Nome** , digite um nome exclusivo para sua configuração. Não use espaços.

    b. No portal clássico do Azure, copie a URL do Emissor e cole-a na caixa de texto **URL do Provedor de Identidade** .

    c. No portal clássico do Azure, copie a **URL de Logon Remoto** e cole-a na caixa de texto **URL de Logon do Provedor de Identidade**.

    d. No portal clássico do Azure, copie a **URL de Logoff Remoto** e cole-a na caixa de texto **URL de Logoff de Provedor de Identidade**.

    e. Selecione **Assinar Solicitação AuthN**.

    f. Para **Enviar solicitação AuthN por**, selecione **POST**.

    g. Para **Enviar solicitação de logoff por**, selecione **POST**. 


1. Na seção **Mapeamento de Atributo Personalizado** , escolha o campo que você deseja mapear com a Declaração do Azure AD. Neste exemplo, a declaração **emailaddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Esse é o nome de declaração padrão do Azure AD para a declaração de email. 
   
    > [!NOTE]
    > Use o **identificador de Usuário** apropriado para mapear o usuário do Azure AD para o mapeamento de usuário do Docusign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.
    > 
    > 
   
    ![Configurando o logon único][57]
2. Na seção **Certificado do Provedor de Identidade**, clique em **Adicionar Certificado** e carregue o certificado que você baixou do portal clássico do Azure AD.   
   
    ![Configurando o logon único][58]
3. Clique em **Salvar**.
4. Na seção **Provedores de Identidade**, clique em **Ações** e em **Pontos de Extremidade**.   
   
    ![Configurando o logon único][59]
5. No portal clássico do Azure, volte para a página **Definir Configurações de Aplicativo** . 
6. Em **Portal de administração do DocuSign**, na seção **Exibir Pontos de Extremidade do SAML 2.0**, execute as seguintes etapas:
   
    ![Configurando o logon único][60]
   
    a. Copie a **URL do Emissor de Provedor de Serviço** e cole-a na caixa de texto **Identificador** no portal clássico do Azure.
   
    b. Copie a **URL de Logon do Provedor de Serviço** e cole-a na caixa de texto **URL de logon** no portal clássico do Azure.
   
    c.  Clique em **Fechar**  
7. No portal clássico do Azure, clique em **Avançar**. 
8. No Portal Clássico do Azure, selecione a **confirmação de configuração de logon único** e clique em **Avançar**.
   
    ![Aplicativos][14]
9. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Aplicativos][15]

## <a name="configuring-account-provisioning"></a>Configurando o provisionamento de contas
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. No **portal clássico do Azure**, na página de **integração de aplicativos do DocuSign**, clique em **Configurar provisionamento de contas** para abrir a caixa de diálogo Configurar Provisionamento de Usuários.
   
    ![Configurando o provisionamento de contas][30]
2. Na página **Configurações e credenciais de administrador**, para habilitar o provisionamento automático de usuários, forneça as credenciais de uma conta do DocuSign com direitos suficientes e clique em **Avançar**. 
   
    ![Configurando o provisionamento de contas][31]
3. Na caixa de diálogo **Testar conexão**, clique em **Iniciar teste** e, depois de um teste bem-sucedido, clique em **Avançar**.
   
    ![Configurando o provisionamento de contas][32]
4. Na página **Confirmação**, clique em **Concluir**.
   
    ![Configurando o provisionamento de contas][33]

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Para atribuir usuários ao DocuSign, execute as etapas a seguir:
1. No **portal clássico do Azure**, crie uma conta de teste.
2. Na página de integração de aplicativos **DocuSign**, clique em **Atribuir usuários**.
   
    ![Atribuindo usuários][40]
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Atribuindo usuários][41]

Agora, você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o DocuSign.

Como uma primeira etapa de verificação, é possível verificar o status de provisionamento clicando em Painel no D na página de integração do aplicativo Docusign do portal clássico do Azure.

![Atribuindo usuários][42]

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado:

![Atribuindo usuários][43]

Se quiser testar suas configurações de logon único, abra o Painel de Acesso.

Para obter mais detalhes sobre o Painel de Acesso, confira: Introdução ao Painel de Acesso.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png



<!--HONumber=Nov16_HO3-->


