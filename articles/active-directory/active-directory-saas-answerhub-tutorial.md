<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao AnswerHub | Microsoft Azure" description="Saiba como usar o AnswerHub com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao AnswerHub
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=528077).

O objetivo deste tutorial é mostrar a integração do Azure ao AnswerHub. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do AnswerHub

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao AnswerHub poderão fazer logon único no aplicativo em seu site de empresa AnswerHub (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o AnswerHub
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Cenário")
##Habilitando a integração de aplicativos para o AnswerHub

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AnswerHub.

###Para habilitar a integração de aplicativos para o AnswerHub, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **AnswerHub**.

    ![Galeria de Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galeria de Aplicativos")

7.  No painel de resultados, selecione **AnswerHub** e clique em **Concluir** para adicionar o aplicativo.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AnswerHub com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **AnswerHub**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no AnswerHub**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar logon único")

3.  Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do AnswerHub**, digite a URL usando o seguinte padrão "**https://company.answerhub.com*" e clique em **Avançar**.

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar a URL do Aplicativo
")

4.  Na página **Configurar logon único no AnswerHub**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.

    ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa AnswerHub como um administrador.

6.  Vá para **Administração**.

7.  Clique na guia **Usuário e Grupo**.

8.  No painel de navegação à esquerda, na seção **Configurações Sociais**, clique em **Configuração do SAML**.

9.  Clique na guia **Config. de IDP**.

10. Na guia **Config. de IDP**, execute as seguintes etapas:

    ![Configuração do SAML](./media/active-directory-saas-answerhub-tutorial/IC785172.png "Configuração do SAML")

    1.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IDP**.
    2.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor de **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do IDP**.
    3.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor de **Formato de Identificador de Nome** e cole-o na caixa de texto **Formato do Identificador de Nome do IDP**.
    4.  Clique em **Chaves e Certificado**.

11. Na guia Chaves e Certificados, execute as seguintes etapas:

    ![Chaves e Certificados](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chaves e Certificados")

    1.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

		>[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele na área de transferência e depois o cole na caixa de texto **Chave Pública do IDP (Formato x509)**.
    3.  Clique em **Salvar**.

12. Na guia **Config. de IDP**, clique em **Salvar**.

13. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no AnswerHub, eles devem ser provisionados no AnswerHub. No caso do AnswerHub, o provisionamento é uma tarefa manual.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **AnswerHub** como um administrador.

2.  Vá para **Administração**.

3.  Clique na guia **Usuários e Grupos**.

4.  No painel de navegação à esquerda, na seção **Gerenciar usuários**, clique em**Criar ou importar usuários**.

    ![Usuários e Grupos](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Usuários e Grupos")

5.  Digite **Endereço de email**, **Nome de usuário** e **Senha** de uma conta válida do Active Directory do Azure que deseja provisionar nas caixas de texto relacionadas e clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do AnswerHub ou as APIs fornecidas pelo AnswerHub para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao AnswerHub, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **AnswerHub**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->