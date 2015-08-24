<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao Boomi | Microsoft Azure" description="Saiba como usar o Boomi com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao Boomi
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529589).

O objetivo deste tutorial é mostrar a integração do Azure ao Boomi. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Boomi

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Boomi poderão fazer logon único no aplicativo em seu site de empresa Boomi (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Boomi
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-boomi-tutorial/IC791134.png "Cenário")
##Habilitando a integração de aplicativos para o Boomi

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Boomi.

###Para habilitar a integração de aplicativos para o Boomi, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-boomi-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-boomi-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-boomi-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Boomi**.

    ![Galeria de Aplicativos](./media/active-directory-saas-boomi-tutorial/IC790822.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **Boomi** e clique em **Concluir** para adicionar o aplicativo.

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Boomi com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Boomi**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Boomi**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Resposta do Boomi**, digite a **URL de Logon do Boomi AtomSphere** (por exemplo: "*https://platform.boomi.com/sso/AccountName/saml*”) e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Boomi**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa Boomi como um administrador.

6.  Na barra de ferramentas, na parte superior, clique no nome da sua empresa e em **Configuração**.

    ![Configuração](./media/active-directory-saas-boomi-tutorial/IC790828.png "Configuração")

7.  Clique em **Opções de SSO**.

    ![Opções de SSO](./media/active-directory-saas-boomi-tutorial/IC790829.png "Opções de SSO")

8.  Na seção **Opções de Logon Único**, execute as seguintes etapas:

    ![Opções de Logon Único](./media/active-directory-saas-boomi-tutorial/IC790830.png "Opções de Logon Único")

    1.  Selecione **Habilitar Logon Único do SAML**.
    2.  Clique em **Importar** para carregar o certificado baixado.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no Boomi**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
    4.  Como **Local de ID da Federação**, selecione **ID da Federação está no elemento NameID do Assunto**.
    5.  Clique em **Salvar**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Boomi, eles deverão ser provisionados no Boomi. No caso do Boomi, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **Boomi** como um administrador.

2.  Vá para **Gerenciamento de Usuários > Usuários**.

    ![Usuários](./media/active-directory-saas-boomi-tutorial/IC790831.png "Usuários")

3.  Clique em **Adicionar Usuário**.

    ![Adicionar usuário](./media/active-directory-saas-boomi-tutorial/IC790832.png "Adicionar usuário")

4.  Na página da caixa de diálogo **Adicionar Funções de Usuário**, execute as seguintes etapas:

    ![Adicionar usuário](./media/active-directory-saas-boomi-tutorial/IC790833.png "Adicionar usuário")

    1.  Digite **Nome**, **Sobrenome** e **Email** de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em OK.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Boomi ou as APIs fornecidas pelo Boomi para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Boomi, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Boomi**, clique em **Atribuir usuários**.

    ![Atribuir Usuários](./media/active-directory-saas-boomi-tutorial/IC790834.png "Atribuir Usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-boomi-tutorial/IC767830.png "Sim")

Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->