<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Jive | Microsoft Azure" description="Saiba como usar o Jive com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Jive
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=330042). Para obter mais informações sobre esse tópico, consulte [Práticas recomendadas para gerenciar os aprimoramentos de acesso do aplicativo do Active Directory do Azure](http://go.microsoft.com/fwlink/?LinkId=329963).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Jive. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no Jive
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Jive
2.  Configurando o provisionamento de usuários

##Habilitando a integração de aplicativos para o Jive
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Jive.

###Para habilitar a integração de aplicativos com o Jive, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-jive-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de aplicativos**, clique em **Adicionar um aplicativo** e, em seguida, clique em **Adicionar um aplicativo para minha organização usar**.

    ![O que você deseja fazer?](./media/active-directory-saas-jive-tutorial/IC700995.png "O que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  No painel de resultados, selecione **Jive** e clique em **Concluir** para adicionar o aplicativo.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Configurando o provisionamento de usuários
  
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Jive. Como parte desse procedimento, é necessário fornecer um token de segurança do usuário que será necessário solicitar em Jive.com.
  
A captura de tela a seguir mostra um exemplo de caixa de diálogo correspondente no AD do Azure:

![Configure o provisionamento do usuário](./media/active-directory-saas-jive-tutorial/IC698794.png "Configure o provisionamento do usuário")

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Jive**, clique em **Configurar provisionamento do usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário**.

2.  Na página **Inserir suas credenciais Jive para habilitar o provisionamento automático de usuários**, forneça as seguintes configurações:

    1.  Na caixa de texto **Nome de usuário administrador do Jive**, digite o nome de conta do Jive que tem o perfil do **administrador de sistema** atribuído no Jive.com.

    2.  Na caixa de texto **Senha de administrador do Jive**, digite a senha para essa conta.

    3.  Na caixa de texto **URL de locatário do Jive**, digite a URL de locatário do Jive.

        >[AZURE.NOTE]A URL de locatário do Jive é usada pela sua organização para efetuar logon no Jive. Normalmente, a URL tem o seguinte formato: **www.<organization>.jive.com**.

    4.  Clique em **Validar** para verificar sua configuração.

    5.  Clique no botão **Próximo** para abrir a página **Confirmação**.

3.  Em seguida, na página **Confirmação**, clique na marca de seleção para salvar sua configuração.
  
Agora, você pode criar uma conta de teste, aguardar 10 minutos e verificar se a conta foi sincronizada no Jive.com.

<!---HONumber=August15_HO7-->