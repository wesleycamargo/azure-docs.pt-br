<properties 
    pageTitle="Tutorial: integração do Azure Active Directory ao TOPdesk - Público | Microsoft Azure" 
    description="Saiba como usar o TOPdesk - Público com o Azure Active Directory para habilitar logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Diretório do Azure ao TOPdesk - Público

O objetivo deste tutorial é mostrar a integração do Azure com o TOPdesk - Público.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do TOPdesk - Public
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TOPdesk - Public poderão fazer logon único no aplicativo em seu site de empresa do TOPdesk - Public (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o TOPdesk - Público
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Cenário")

##Habilitando a integração de aplicativos com o TOPdesk - Público
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TOPdesk - Público.

###Para habilitar a integração de aplicativos com o TOPdesk - Público, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **TOPdesk - Public**.

    ![Galeria de aplicativos](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **TOPdesk - Public** e clique em **Concluir** para adicionar o aplicativo.

    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TOPdesk - Público com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para o TOPdesk - Público requer que você carregue um arquivo de ícone de logotipo. Para obter o arquivo do ícone, entre em contato com a equipe de suporte do TOPdesk.

###Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2.  No menu do **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configurações")

3.  Clique em **Configurações de Logon**.

    ![Configurações de Logon](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configurações de Logon")

4.  Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Geral")

5.  Na seção **Público** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:

    ![Configurações Técnicas](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Configurações Técnicas")

    1.  Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.
    2.  Abra o arquivo de metadados e localize o nó **AssertionConsumerService**.
        ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copie o valor de **AssertionConsumerService**.  

        >[AZURE.NOTE]Você precisará do valor na seção **Configurar URL do Aplicativo** mais adiante neste tutorial.

6.  Em outra janela do navegador da Web, faça logon no **portal clássico do Azure** como administrador.

7.  Na página de integração de aplicativos do **TOPdesk - Public**, clique em **Configurar logon único**, para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurar o logon único")

8.  Na página **Como você deseja que os usuários façam logon no TOPdesk - Público**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurar o logon único")

9.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Logon do TOPdesk - Public**, digite a URL usada pelos usuários para entrar no aplicativo TOPdesk - Public (por exemplo: "*https://qssolutions.topdesk.net*").
    2.  Na caixa de texto **URL de Resposta do TOPdesk – Public**, cole a **URL de AssertionConsumerService do TOPdesk - Public** (por exemplo: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Clique em **Avançar**.

10. Na página **Configurar logon único no TOPdesk - Public**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurar o logon único")

11. Execute as seguintes etapas para criar um arquivo de certificado:

    ![Certificado](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificado")

    1.  Abra o arquivo de metadados baixado.
    2.  Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.
    3.  Copie o valor do nó **X509Certificate**.
    4.  Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.

12. Em seu site de empresa do TOPdesk - Public, no menu **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configurações")

13. Clique em **Configurações de Logon**.

    ![Configurações de Logon](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configurações de Logon")

14. Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Geral")

15. Na seção **Público**, clique em **Adicionar**.

    ![Logon no SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "Logon no SAML")

16. Na página do diálogo **Assistente de configuração do SAML**, realize as seguintes etapas:

    ![Assistente de Configuração de SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "Assistente de Configuração de SAML")

    1.  Para carregar o arquivo de metadados baixado, em **Metadados de Federação**, clique em **Procurar**.
    2.  Para carregar o arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.
    3.  Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.
    4.  Na caixa de texto **Atributo do nome de usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Na caixa de texto **Nome de exibição**, digite um nome para a sua configuração.
    6.  Clique em **Salvar**.

17. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Público, eles deverão ser provisionados no TOPdesk - Público.  
No caso do TOPdesk - Público, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **TOPdesk - Public** como administrador.

2.  No menu na parte superior, clique em **TOPdesk > Novo > Arquivos de Suporte > Pessoa**.

    ![Pessoa](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Pessoa")

3.  Na caixa de diálogo Nova Pessoa, execute as seguintes etapas:

    ![Nova Pessoa](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Nova Pessoa")

    1.  Clique na guia Geral.
    2.  Na caixa de texto Sobrenome, digite o sobrenome referente a uma conta válida do Active Directory do Azure que você deseja provisionar.
    3.  Selecione um **Site** para a conta.
    4.  Clique em **Salvar**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk - Público ou APIs fornecidas pelo TOPdesk - Público para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao TOPdesk - Público, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **TOPdesk - Public**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->