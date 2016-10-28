<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Kudos | Microsoft Azure" 
    description="Saiba como usar o Kudos com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Kudos
  
O objetivo deste tutorial é mostrar a integração do Azure com o Kudos.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do Kudos
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Kudos poderão fazer logon único no aplicativo em seu site de empresa do Kudos (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Kudos
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-kudos-tutorial/IC787799.png "Cenário")
##Habilitando a integração de aplicativos para o Kudos
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Kudos.

###Para habilitar a integração de aplicativos com o Kudos, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-kudos-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-kudos-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Kudos**.

    ![Galeria de Aplicativos](./media/active-directory-saas-kudos-tutorial/IC787800.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Kudos** e clique em **Concluir** para adicionar o aplicativo.

    ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Kudos com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração de aplicativos do **Kudos**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Kudos**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Kudos**, digite a URL usando o padrão "*https://company.kudosnow.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar logon único no Kudos**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Kudos como administrador.

6.  No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-kudos-tutorial/IC787806.png "Configurações")

7.  Clique em **Integrações > SSO**.

8.  Na seção **SSO**, realize as seguintes etapas:

    ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Kudos**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon**.
	2.  Crie um arquivo **codificado em Base 64** por meio do certificado baixado.  

        >[AZURE.TIP]
        Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
    4.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Kudos**, copie o valor da **URL do Serviço de Logoff Único** e cole-o na caixa de texto **URL de Logoff**.
	5.  Na caixa de texto **Sua URL do Kudos**, digite o nome de sua empresa.
    6.  Clique em **Salvar**.

9.  No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Kudos, eles devem ser provisionados no Kudos. No caso do Kudos, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Kudos** como administrador.

2.  No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-kudos-tutorial/IC787806.png "Configurações")

3.  Clique em **Usuário Administrador**.

4.  Clique na guia **Usuários** e, em seguida, clique em **Adicionar um usuário**.

    ![Usuário administrador](./media/active-directory-saas-kudos-tutorial/IC787809.png "Usuário administrador")

5.  Na seção **Adicionar um Usuário**, execute as seguintes etapas:

    ![Adicionar um usuário](./media/active-directory-saas-kudos-tutorial/IC787810.png "Adicionar um Usuário")

    1.  Digite o **Nome**, **Sobrenome** e **Email** e outros detalhes de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Criar Usuário**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Kudos ou APIs fornecidas pelo Kudos para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Kudos, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Kudos**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-kudos-tutorial/IC787811.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-kudos-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->