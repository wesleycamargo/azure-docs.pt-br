<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao AirWatch | Microsoft Azure" description="Saiba como usar o AirWatch com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao AirWatch
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529791).

O objetivo deste tutorial é mostrar a integração do Azure ao AirWatch. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do AirWatch

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao AirWatch poderão fazer logon único no aplicativo em seu site de empresa AirWatch (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração do aplicativo para o AirWatch
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Habilitando a integração do aplicativo para o AirWatch

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AirWatch.

###Para habilitar a integração de aplicativos para o AirWatch, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **AirWatch**.

    ![Galeria de Aplicativos](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **AirWatch** e clique em **Concluir** para adicionar o aplicativo.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AirWatch com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **AirWatch**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no AirWatch!**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurar o logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do AirWatch**, digite a URL usada pelos usuários para entrar no aplicativo AirWatch (por exemplo: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no AirWatch**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa AirWatch! como um administrador.

6.  No painel de navegação esquerdo, clique em **Configurações** e em **Administradores**.

    ![Administradores](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administradores")

7.  Expanda o menu **Configurações** e clique em **Serviços de Diretório**.

    ![Configurações](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Configurações")

8.  Clique na guia **Usuário**, no campo de texto **DN Base**, digite seu nome de domínio e clique em **Salvar**.

    ![Usuário](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Usuário")

9.  Clique na guia **Servidor**.

    ![Servidor](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Servidor")

10. Execute as seguintes etapas:

    ![Carregar](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Carregar")

    1.  Como **Tipo de Diretório**, selecione **Nenhum**.
    2.  Selecione **Usar SAML para Autenticação**.
    3.  Para carregar o certificado baixado, clique em **Carregar**.

11. Na seção **Solicitar**, execute as seguintes etapas:

    ![Solicitação](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Solicitação")

    1.  Como **Tipo de Associação de Solicitação**, selecione **POST**.
    2.  No portal do Azure, na página de diálogo **Configurar logon único no AirWatch**, copie o valor de **URL de Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon Único do Provedor de Identidade**.
    3.  Como **Formato de NameID**, selecione **Endereço de Email**.
    4.  Clique em **Salvar**.

12. Clique na guia **Usuário** novamente.

    ![Usuário](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Usuário")

13. Na seção **Atributo**, execute as seguintes etapas:

    ![Atributo](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Atributo")

    1.  Na caixa de texto **Identificador de Objeto**, digite **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Na caixa de texto **Nome de usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Na caixa de texto **Nome de Exibição**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Na caixa de texto **Nome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Na caixa de texto **Sobrenome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Na caixa de texto **Email**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Clique em **Salvar**.

14. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no AirWatch, eles devem ser provisionados no AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **AirWatch** como um administrador.

2.  No painel de navegação esquerdo, clique em **Contas** e em **Usuários**.

    ![Usuários](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Usuários")

3.  No menu **Usuários**, clique em **Exibição de Lista** e em **Adicionar > Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Adicionar usuário")

4.  Na caixa de diálogo **Adicionar/Editar Usuário**, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Adicionar usuário")

    1.  Digite **Nome**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome** e **Endereço de Email** de uma conta válida do Active Directory do Azure que deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do AirWatch ou as APIs fornecidas pelo AirWatch para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao AirWatch, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **AirWatch**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->