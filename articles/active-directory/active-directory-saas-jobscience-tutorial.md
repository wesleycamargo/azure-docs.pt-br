<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com o Jobscience | Microsoft Azure" 
    description="Saiba como usar o Jobscience com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure com o Jobscience
  
O objetivo deste tutorial é mostrar a integração do Azure com o Jobscience. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Jobscience com logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Jobscience poderão fazer logon único no aplicativo em seu site de empresa do Jobscience (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Jobscience
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Cenário")
##Habilitando a integração de aplicativos para o Jobscience
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Jobscience.

###Para habilitar a integração de aplicativos com o Jobscience, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Jobscience**.

    ![Galeria de Aplicativos](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Jobscience** e clique em **Concluir** para adicionar o aplicativo.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Jobscience com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para Jobscience exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon no site da sua empresa Jobscience como um administrador.

2.  Vá para **Configuração**.

    ![Configuração](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuração")

3.  No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de domínio** para expandir a seção correspondente e, em seguida, clique em **Meu domínio** para abrir a página **Meu domínio**.

    ![Meu Domínio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu Domínio")

4.  Para verificar se seu domínio foi configurado corretamente, veja se ele está em "**Etapa 4 Implantado para Usuários**" e revise "**Minhas Configurações de Domínio**".

    ![Domínio implantado para o usuário](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domínio implantado para o usuário")

5.  Em uma janela de navegador da Web diferente, faça logon no portal clássico do Azure.

6.  Na página de integração de aplicativos do **Jobscience**, clique em **Configurar logon único**, para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurar o logon único")

7.  Na página **Como você deseja que os usuários façam logon no Jobscience**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurar o logon único")

8.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Jobscience**, digite a URL usando o padrão "*http://company.my.salesforce.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurar a URL do Aplicativo")

9.  Na página **Configurar logon único no Jobscience**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurar o logon único")

10. No site de empresa do Jobscience, clique em **Controles de Segurança** e clique em **Configurações de Logon Único**.

    ![Controles de Segurança](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Controles de segurança")

11. Na seção **Configurações de Logon Único**, realize as seguintes etapas:

    ![Configurações de logon único](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Configurações de logon único")

    1.  Selecione **SAML Habilitado**.
    2.  Clique em **Novo**.

12. No diálogo **Editar Configuração de Logon Único do SAML**, realize as seguintes etapas:

    ![Configuração de logon único do SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "Configuração de logon único do SAML")

    1.  Na caixa de texto **Nome**, digite um nome para a sua configuração.
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Jobscience**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor**.
    3.  Na caixa de texto **ID de Entidade**, digite **https://salesforce-jobscience.com**
    4.  Clique em **Procurar** para carregar o certificado do Azure AD.
    5.  Para o **Tipo de Identidade do SAML**, selecione **A declaração contém a ID de Federação do objeto User**.
    6.  Para **Local de Identidade do SAML**, selecione **A identidade está contida no elemento NameIdentifier da instrução Subject**.
    7.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Jobscience**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
    8.  No portal clássico do Azure, na página de diálogo **Configurar logon único no Jobscience**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do Provedor de Identidade**.
    9.  Clique em **Salvar**.

13. No painel de navegação à esquerda, na seção **Administrar**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**.

    ![Meu Domínio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu Domínio")

14. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.

    ![Identidade visual da página de logon](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Identidade visual da página de logon")

15. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.

    ![Identidade visual da página de logon](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Identidade visual da página de logon")

16. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurar o logon único")
  
Para obter a URL de Logon Único iniciada pelo SP, clique em **Configurações de Logon Único** na seção do menu **Controles de Segurança**.

![Controles de Segurança](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Controles de segurança")
  
Clique no perfil SSO que você criou na etapa anterior. Esta página mostra o Logon Único na URL para a sua empresa (por exemplo, *https://companyname.my.salesforce.com?so=companyid*).
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no Jobscience, eles devem ser provisionados no Jobscience. No caso do Jobscience, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Jobscience** como administrador.

2.  Vá para Configuração.

    ![Configuração](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuração")

3.  Vá para **Gerenciar Usuários > Usuários**.

    ![Usuários](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Usuários")

4.  Clique em **Novo Usuário**.

    ![Todos os Usuários](./media/active-directory-saas-jobscience-tutorial/IC784370.png "Todos os usuários")

5.  No diálogo **Editar Usuário**, realize as seguintes etapas:

    ![Editar usuário](./media/active-directory-saas-jobscience-tutorial/IC784371.png "Editar usuário")

    1.  Digite o nome, sobrenome, alias, email, propriedades de nome e apelido do usuário do AD do Azure que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O titular da conta do AD do Azure receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.

>[AZURE.NOTE] Você pode usar qualquer outra ferramenta de criação de conta de usuário do Jobscience ou APIs fornecidas pelo Jobscience para provisionar contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Jobscience, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos **Jobscience**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->