<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao TOPdesk - Secure | Microsoft Azure"
    description="Saiba como usar o TOPdesk - Secure com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Tutorial: Integração do Active Directory do Azure ao TOPdesk - Secure
  
O objetivo deste tutorial é mostrar a integração do Azure com o TOPdesk - Secure. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do TOPdesk - Secure
  
Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TOPdesk - Secure poderão fazer logon único no aplicativo em seu site de empresa do TOPdesk - Secure (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o TOPdesk - Secure
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Cenário")

##Habilitando a integração de aplicativos com o TOPdesk - Secure
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TOPdesk - Secure.

###Para habilitar a integração de aplicativos com o TOPdesk - Secure, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **TOPdesk - Secure**.

    ![Galeria de Aplicativos](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **TOPdesk - Secure** e clique em **Concluir** para adicionar o aplicativo.

    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TOPdesk - Secure com sua conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o TOPdesk - Secure requer que você carregue um arquivo de ícone de logotipo. Para obter o arquivo do ícone, entre em contato com a equipe de suporte do TOPdesk.

###Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.

2.  No menu do **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configurações")

3.  Clique em **Configurações de Logon**.

    ![Configurações de Logon](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configurações de Logon")

4.  Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

5.  Na seção **Seguro** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:

    ![Configurações Técnicas](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Configurações Técnicas")

    1.  Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.
    2.  Abra o arquivo de metadados e localize o nó **AssertionConsumerService**. ![Serviço de Declaração do Consumidor](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Serviço de Declaração do Consumidor")
    3.  Copie o valor de **AssertionConsumerService**.  

        >[AZURE.NOTE] Você precisará do valor na seção **Configurar URL do Aplicativo** mais adiante neste tutorial.

6.  Em outra janela do navegador da Web, faça logon em seu portal do **Active Directory do Azure** como administrador.

7.  Na página de integração de aplicativos do **TOPdesk - Secure**, clique em **Configurar logon único**, para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurar o logon único")

8.  Na página **Como você deseja que os usuários façam logon no TOPdesk - Secure**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurar o logon único")

9.  Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurar a URL do Aplicativo")

    1.  Na caixa de texto **URL de Logon do TOPdesk - Secure**, digite a URL usada pelos usuários para entrar no aplicativo TOPdesk - Secure (por exemplo: "**https://qssolutions.topdesk.net*")).
2.  Na caixa de texto **URL de Resposta do TOPdesk – Secure**, cole a **URL AssertionConsumerService do TOPdesk - Secure** (por exemplo: "**https://qssolutions.topdesk.net/tas/public/login/saml*")
3.  Clique em **Próximo**.

10. Na página **Configurar logon único no TOPdesk - Secure**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurar o logon único")

11. Execute as seguintes etapas para criar um arquivo de certificado:

    ![Certificado](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificado")

    1.  Abra o arquivo de metadados baixado.
    2.  Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.
    3.  Copie o valor do nó **X509Certificate**.
    4.  Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.

12. Em seu site de empresa do TOPdesk - Secure, no menu **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configurações")

13. Clique em **Configurações de Logon**.

    ![Configurações de Logon](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configurações de Logon")

14. Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

15. Na seção **Público**, clique em **Adicionar**.

    ![Adicionar](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Adicionar")

16. Na página do diálogo **Assistente de configuração do SAML**, realize as seguintes etapas:

    ![Assistente de Configuração de SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistente de Configuração de SAML")

    1.  Para carregar o arquivo de metadados baixado, em **Metadados de Federação**, clique em **Procurar**.
    2.  Para carregar o arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.
    3.  Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.
    4.  Na caixa de texto **Atributo do nome de usuário**, digite ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
5.  Na caixa de texto **Nome de exibição**, digite um nome para a sua configuração.
    6.  Clique em **Salvar**.

17. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurar o logon único")

##Configurando o provisionamento de usuários
  
Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Secure, eles deverão ser provisionados no TOPdesk - Secure. No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.

2.  No menu na parte superior, clique em **TOPdesk > Novo > Arquivos de Suporte > Operador**.

    ![Operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operador")

3.  No diálogo **Novo Operador**, realize as seguintes etapas:

    ![Novo Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Novo Operator")

    1.  Clique na guia Geral.
    2.  Na caixa de texto **Sobrenome** na seção **Geral**, digite o sobrenome referente a uma conta válida do Active Directory do Azure que você deseja provisionar.
    3.  Selecione um **Site** para a conta na seção **Local**.
    4.  Na caixa de texto **Nome de Logon** da seção **Logon no TOPdesk**, digite um nome de logon para o usuário.
    5.  Clique em **Salvar**.

>[AZURE.NOTE] É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk - Secure ou APIs fornecidas pelo TOPdesk - Secure para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao TOPdesk - Secure, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **TOPdesk - Secure**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0218_2016-->