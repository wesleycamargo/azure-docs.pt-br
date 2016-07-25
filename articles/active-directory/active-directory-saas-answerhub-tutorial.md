<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao AnswerHub | Microsoft Azure" 
    description="Saiba como usar o AnswerHub com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure ao AnswerHub

O objetivo deste tutorial é mostrar a integração do Azure ao AnswerHub. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do AnswerHub

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao AnswerHub poderão fazer logon único no aplicativo em seu site de empresa do AnswerHub (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o AnswerHub
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Cenário")
##Habilitando a integração de aplicativos para o AnswerHub

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AnswerHub.

###Para habilitar a integração de aplicativos para o AnswerHub, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **AnswerHub**.

    ![Galeria de Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **AnswerHub** e clique em **Concluir** para adicionar o aplicativo.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AnswerHub com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração do aplicativo **AnswerHub**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no AnswerHub**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do AnswerHub**, digite a URL usando o padrão "*https://company.answerhub.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar logon único no AnswerHub**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa AnswerHub como um administrador.

6.  Vá para **Administração**.

7.  Clique na guia **Usuário e Grupo**.

8.  No painel de navegação à esquerda, na seção **Configurações Sociais**, clique em **Configuração do SAML**.

9.  Clique na guia **Config. de IDP**.

10. Na guia **Config. de IDP**, realize as seguintes etapas:

    ![Configuração do SAML](./media/active-directory-saas-answerhub-tutorial/IC785172.png "Configuração do SAML")

    1.  No portal clássico do Azure, na página do diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IDP**.
    2.  No portal clássico do Azure, na página do diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logout Remoto** e cole-o na caixa de texto **URL de Logout do IDP**.
    3.  No portal clássico do Azure, na página do diálogo **Configurar logon único no AnswerHub**, copie o valor da **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato do Identificador de Nome do IDP**.
    4.  Clique em **Chaves e Certificados**.

11. Na guia Chaves e Certificados, execute as seguintes etapas:

    ![Chaves e Certificados](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chaves e Certificados")

    1.  Crie um arquivo **codificado em base 64** usando o certificado baixado.

		>[AZURE.TIP] Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Chave Pública do IDP (Formato x509)**.
    3.  Clique em **Salvar**.

12. Na guia **Config. de IDP**, clique em **Salvar**.

13. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no AnswerHub, eles devem ser provisionados no AnswerHub. No caso do AnswerHub, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **AnswerHub** como administrador.

2.  Vá para **Administração**.

3.  Clique na guia **Usuários e Grupos**.

4.  No painel de navegação à esquerda, na seção **Gerenciar Usuários**, clique em **Criar ou importar usuários**.

    ![Usuários e Grupos](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Usuários e Grupos")

5.  Digite **Endereço de email**, **Nome de usuário** e **Senha** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas e clique em **Salvar**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do AnswerHub ou as APIs fornecidas pelo AnswerHub para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao AnswerHub, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração do aplicativo **AnswerHub**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->