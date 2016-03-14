<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o DocuSign | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Active Directory do Azure com o DocuSign

O objetivo deste tutorial é mostrar a integração do Azure ao DocuSign. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

- Uma assinatura válida do Azure
- Um locatário no DocuSign



O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. [Habilitando a integração de aplicativos para o DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurando o logon único](#configuring-single-sign-on)


3. [Configurando o provisionamento de contas](#configuring-account-provisioning)


4. [Atribuindo usuários](#assigning-users)




<br><br>![Configurando o logon único][0]<br>


 

## Habilitando a integração de aplicativos para o DocuSign

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o DocuSign.

### Para habilitar a integração de aplicativos para o DocuSign, execute as seguintes etapas:

1. No portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Configurando o logon único][1]<br>

2. Na lista Diretório, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Configurando o logon único][2]<br>

4. Clique em **Adicionar** na parte inferior da página. <br><br>![Aplicativos][3]<br>

5. Na caixa de diálogo O que você deseja fazer, clique em **Adicionar um aplicativo da galeria**. <br><br>![Configurando o logon único][4]<br>


6. Na caixa de pesquisa, digite **Docusign**. <br><br>![Configurando o logon único][5]<br>

7. No painel de resultados, selecione **Docusign** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Configurando o logon único][6]<br>




## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no DocuSign com sua conta do Azure AD usando federação baseada em protocolo SAML.


### Para configurar o logon único, execute as seguintes etapas:

1. No portal clássico do Azure, na página de **integração de aplicativos do DocuSign**, clique em **Configurar logon único** para abrir o diálogo Configurar Logon Único. <br><br>![Configurando o logon único][7]<br>

2. Na página **Como você deseja que os usuários façam logon no DocuSign**, selecione **Logon Único do AD do Microsoft Azure** e clique em Próximo. <br><br>![Configurando o logon único][8]<br>

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon do DocuSign**, digite a URL do locatário do DocuSign e clique em **Próximo**: A URL tem o seguinte esquema: *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![Configurando o logon único][9]<br>


    > [AZURE.TIP] Se você não souber qual é a URL do aplicativo para o seu locatário, tente entrar em contato com a Docusign via SSOSetup@Docusign.com para obter a URL de SSO iniciada no SP para o seu locatário.
 

4. Na página **Configurar logon único no DocuSign**, clique em **Baixar certificado** e salve o arquivo de certificado em seu computador. <br><br>![Configurando o logon único][10]<br>


5. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **DocuSign** como administrador.


6. No menu na parte superior, expanda o menu do usuário, clique em **Preferências**; no painel de navegação à esquerda, expanda **Gerenciamento de Contas** e clique em **Recursos**. <br><br>![Configurando o logon único][11]<br>

7. Clique em **Configuração SAML** e clique no link **Configuração SAML**.



8. Na seção **Configuração do SAML2.0**, execute as seguintes etapas: <br><br>![Configurando o logon único][13]<br>


    a. No portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no DocuSign**, copie o valor de URL** do Emissor e cole-o na caixa de texto **URL de Ponto de Extremidade do Provedor de Identidade**.

    > [AZURE.IMPORTANT] Se essa opção de configuração não estiver disponível, contate seu gerente de conta Docusign ou entre em contato com a equipe de suporte de SSO por email ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Clique em **Pesquisar** para carregar o certificado que você baixou.


    c. Selecione **Habilitar nome, sobrenome e endereço de email**.


    d. Clique em **Salvar**.


9. No portal clássico do Azure, selecione a **confirmação de configuração de logon único** e clique em **Avançar**. <br><br>![Aplicativos][14]<br>

10. Na página **Confirmação de logon único**, clique em **Concluir**.<br><br>![Aplicativos][15]<br>


 

## Configurando o provisionamento de contas

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no DocuSign.

### Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. No **portal clássico do Azure**, na página de **integração de aplicativos do DocuSign**, clique em **Configurar provisionamento de contas** para abrir o diálogo Configurar Provisionamento de Usuários. <br><br>![Configurando o provisionamento de contas][30]<br>
 

2. Na página **Configurações e credenciais de administrador**, para habilitar o provisionamento automático de usuários, forneça as credenciais de uma conta DocuSign com direitos suficientes e clique em **Próximo**. <br><br>![Configurando o provisionamento de contas][31]<br>

3. Na caixa de diálogo **Testar conexão**, clique em **Iniciar teste** e, depois de um teste bem-sucedido, clique em **Próximo**. <br><br>![Configurando o provisionamento de contas][32]<br>

3. Na página **Confirmação**, clique em **Concluir**.

<br><br>![Configurando o provisionamento de contas][33]<br>
 

## Atribuindo usuários

Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

### Para atribuir usuários ao DocuSign, execute as etapas a seguir:

1. No **portal clássico do Azure**, crie uma conta de teste.

2. Na página de **Integração de aplicativos do DocuSign**, clique em **Atribuir usuários**. <br><br>![Atribuindo usuários][40]<br>
 

3. Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

<br><br>![Atribuindo usuários][41]<br>


Agora, você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o DocuSign.

Como uma primeira etapa de verificação, é possível verificar o status de provisionamento clicando em Painel no D na página de integração de aplicativos do DocuSign do portal clássico do Azure. <br><br>![Atribuindo usuários][42]<br>

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado: <br><br>![Atribuindo usuários][43]<br>


Se você quiser testar suas configurações de logon único, abra o Painel de Acesso.

Para obter mais detalhes sobre o Painel de Acesso, confira: Introdução ao Painel de Acesso.





## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=AcomDC_0302_2016-->