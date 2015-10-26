<properties pageTitle="Tutorial: integração do Active Directory do Azure ao Egnyte | Microsoft Azure" description="Saiba como usar o Egnyte com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integração do Active Directory do Azure ao Egnyte
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528188).
  
O objetivo deste tutorial é mostrar a integração do Azure ao Egnyte.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Egnyte
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu ao Egnyte poderão fazer logon único no aplicativo em seu site de empresa do Egnyte (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Egnyte
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Cenário")
##Habilitando a integração de aplicativos para o Egnyte
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Egnyte.

###Para habilitar a integração de aplicativos para o Egnyte, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **egnyte**.

    ![Galeria de Aplicativos](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Egnyte** e clique em **Concluir** para adicionar o aplicativo.

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Egnyte com a própria conta do Azure AD usando a federação baseada no protocolo SAML.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base-64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração de aplicativos do **Egnyte**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Egnyte**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configurar o logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon no Egnyte**, digite sua URL usando o seguinte padrão "**https://company.egnyte.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Egnyte**, clique em **Baixar certificado** e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa do Egnyte como administrador.

6.  Clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Configurações")

7.  No menu, clique em **Configurações**.

    ![Configurações](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Configurações")

8.  Clique na guia **Configuração** e clique em **Segurança**.

    ![Segurança](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Segurança")

9.  Na seção **Autenticação de Logon Único**, execute as seguintes etapas:

    ![Autenticação de Logon Único](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Autenticação de Logon Único")

    1.  Para **Autenticação de logon único**, selecione **SAML 2.0**.
    2.  Para **Provedor de Identidade**, selecione **AzureAD**.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Egnyte**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de logon do provedor de identidade**.
4.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Egnyte**, copie o valor de **ID de Entidade** e cole-o na caixa de texto **ID de entidade do provedor de identidade**.
    5.  Crie um arquivo **codificado em base-64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele em sua área de transferência e, então, cole-o na caixa de texto **Certificado de provedor de identidade**
    7.  Para **Mapeamento de usuário padrão**, selecione **Endereço de email**.
    8.  Para **Usar valor de emissor específico ao domínio**, selecione **desabilitado**.
    9.  Clique em **Salvar**.

10. No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no Egnyte, eles deverão ser provisionados no Egnyte. No caso do Egnyte, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa do **Egnyte** como administrador.

2.  Acesse **Configurações > Usuários e Grupos**.

3.  Clique em **Adicionar Novo Usuário** e selecione o tipo de usuário que você deseja adicionar.

    ![Usuários](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Usuários")

4.  Na seção **Novo Usuário Padrão**, execute as seguintes etapas:

    ![Novo Usuário Padrão](./media/active-directory-saas-egnyte-tutorial/IC787825.png "Novo Usuário Padrão")

    1.  Digite o **Email**, **Nome de usuário** e outros detalhes de uma conta válida do Active Directory do Azure que você deseja provisionar.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE]O titular da conta do Active Directory do Azure receberá um email de notificação.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Egnyte ou as APIs fornecidas pelo Egnyte para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Egnyte, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Egnyte**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->