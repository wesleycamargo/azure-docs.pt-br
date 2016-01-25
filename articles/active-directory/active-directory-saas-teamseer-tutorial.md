<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o TeamSeer | Microsoft Azure" 
    description="Saiba como usar o TeamSeer com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure ao TeamSeer
  
O objetivo deste tutorial é mostrar a integração do Azure ao TeamSeer. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário do TeamSeer
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TeamSeer poderão fazer logon único no aplicativo em seu site de empresa do TeamSeer (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o TeamSeer
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Cenário")

##Habilitando a integração de aplicativos para o TeamSeer
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o TeamSeer.

###Para habilitar a integração de aplicativos com o TeamSeer, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **TeamSeer**.

    ![Galeria de Aplicativos](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **TeamSeer** e clique em **Concluir** para adicionar o aplicativo.

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TeamSeer com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **TeamSeer**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no TeamSeer**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do TeamSeer**, digite a URL usando o padrão "**http://www.teamseer.com/companyid*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no TeamSeer**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa TeamSeer como um administrador.

6.  Vá para **Administrador de RH**.

    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")

7.  Clique em **Instalação**.

    ![Configuração](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Configuração")

8.  Clique em **Configurar detalhes do provedor de SAML**.

    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/IC789636.png "Configurações do SAML")

9.  Na seção de detalhes do provedor de SAML, execute as seguintes etapas:

    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/IC789637.png "Configurações do SAML")

    1.  No portal do Azure, na página do diálogo **Configurar logon único no TeamSeer**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL**.
    2.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público do IdP**.

10. Para concluir a configuração do provedor de SAML, execute as seguintes etapas:

    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/IC789638.png "Configurações do SAML")

    1.  Nos **Endereços de Email de Teste**, digite o endereço de email do usuário de teste.
    2.  Na caixa de texto **Emissor**, digite a URL do Emissor do provedor de serviços.
    3.  Clique em **Salvar**.

11. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no TeamSeer, eles deverão ser provisionados no ShiftPlanning. No caso do TeamSeer, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **TeamSeer** como administrador.

2.  Execute as seguintes etapas:

    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")

    1.  Vá para **Administrador de RH > Usuários**.
    2.  Clique em **Executar o assistente de Novo Usuário**.

3.  Na seção **Detalhes do Usuário**, realize as seguintes etapas:

    ![Detalhes do Usuário](./media/active-directory-saas-teamseer-tutorial/IC789641.png "Detalhes do Usuário")

    1.  Digite o **Nome**, **Sobrenome** e **Nome de usuário (Endereço de email)** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Próximo**.

4.  Siga as instruções na tela para adicionar um novo usuário e clique em **Concluir**.

>[AZURE.NOTE]Você pode usar qualquer outra ferramenta de criação da conta de usuário do TeamSeer ou APIs fornecidas pelo TeamSeer para provisionar as contas de usuário do AD do Azure.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao TeamSeer, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **TeamSeer**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->