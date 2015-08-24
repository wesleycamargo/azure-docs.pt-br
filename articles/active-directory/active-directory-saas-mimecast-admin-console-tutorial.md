<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o Mimecast Admin Console | Microsoft Azure" description="Saiba como usar o Mimecast Admin Console com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o Mimecast Admin Console
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529833).
  
O objetivo deste tutorial é mostrar a integração do Azure com o Mimecast Admin Console. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do Mimecast Admin Console com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao Mimecast Admin Console poderão fazer logon único no aplicativo em seu site de empresa Mimecast Admin Console (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o Mimecast Admin Console
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Cenário")
##Habilitando a integração de aplicativos com o Mimecast Admin Console
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Mimecast Admin Console.

###Para habilitar a integração de aplicativos com o Mimecast Admin Console, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Mimecast Admin Console**.

    ![Galeria de aplicativos](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Mimecast Admin Console** e clique em **Concluir** para adicionar o aplicativo.

    ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Mimecast Admin Console com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, consulte [Como converter certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **Mimecast Admin Console**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Mimecast Admin Console**, selecione **Logon único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Mimecast Admin Console**, digite a URL usada pelos usuários para fazer logon no seu aplicativo do Mimecast Admin Console (por exemplo, "https://webmail-uk.mimecast.com" ou "https://webmail-us.mimecast.com") e clique em **Avançar**.

    >[AZURE.NOTE]A URL de logon é específica para a região.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no Mimecast Admin Console**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu Mimecast Admin Console como um administrador.

6.  Vá para **Serviços > Aplicativo**.

    ![Serviços](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Serviços")

7.  Clique em **Perfis de Autenticação**.

    ![Perfis de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Perfis de autenticação")

8.  Clique em **Novo Perfil de Autenticação**.

    ![Nenhum perfil de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nenhum perfil de autenticação")

9.  Na seção **Perfil de Autenticação**, execute as seguintes etapas:

    ![Perfil de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Perfil de autenticação")

    1.  Na caixa de texto **Descrição**, digite um nome para sua configuração.
    2.  Selecione **Impor autenticação SAML para o Mimecast Admin Console**.
    3.  Como **Provedor**, selecione **Active Directory do Azure**.
    4.  No portal do Azure, na página de diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL do emissor** e, em seguida, cole-o na caixa de texto **URL do emissor**.
    5.  No portal do Azure, na página de diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL de logon remoto** e, em seguida, cole-o na caixa de texto **URL de logon**.
    6.  No portal do Azure, na página de diálogo **Configurar logon único no Mimecast Admin Console**, copie o valor da **URL de logon remoto** e, em seguida, cole-o na caixa de texto **URL de logoff**.  

        >[AZURE.NOTE]O valor da URL de logon e da URL de logoff para o Mimecast Admin Console são as mesmas.

    7.  Crie um arquivo **codificado em base 64** usando o certificado baixado.

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abra seu certificado codificado em base 64 no bloco de notas, remova a primeira linha (“*--*“) e a última linha (“*--*“), copie o conteúdo restante para a área de transferência e, então, cole-o na caixa de texto **Certificado de provedor de identidade (metadados)**.
    9.  Selecione **Permitir logon único**.
    10. Clique em **Salvar**.

10. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para permitir que os usuários do Azure AD façam logon no Mimecast Admin Console, eles devem ser provisionados no Mimecast Admin Console. No caso do Mimecast Admin Console, o provisionamento é uma tarefa manual.
  
Você precisa registrar um domínio antes de criar usuários.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon no **Mimecast Admin Console** como administrador.

2.  Vá para **Diretórios > Interno**.

    ![Diretórios](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Diretórios")

3.  Clique em **Registrar um novo domínio**.

    ![Registrar um novo domínio](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registrar um novo domínio")

4.  Após o novo domínio ter sido criado, clique em **Novo endereço**.

    ![Novo endereço](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Novo endereço")

5.  Na caixa de diálogo do novo endereço, execute as seguintes etapas:

    ![Salvar](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Salvar")

    1.  Digite os atributos de **Endereço de email**, **Nome global**, **Senha** e **Confirmar senha** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Mimecast Admin Console ou as APIs fornecidas pelo Mimecast Admin Console para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

###Para atribuir usuários ao Mimecast Admin Console, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Mimecast Admin Console**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->