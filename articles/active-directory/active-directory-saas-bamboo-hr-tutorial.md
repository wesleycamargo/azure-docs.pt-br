<properties pageTitle="Tutorial: Integração do Active Directory do Azure ao BambooHR | Microsoft Azure" description="Saiba como usar o BambooHR com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure ao Bamboo HR
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=529837).

O objetivo deste tutorial é mostrar a integração do Azure ao BambooHR.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do BambooHR

Depois de concluir este tutorial, os usuários do AD do Azure que você atribuiu ao BambooHR poderão fazer logon único no aplicativo em seu site de empresa BambooHR (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o BambooHR
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")
##Habilitando a integração de aplicativos para o BambooHR

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o BambooHR.

###Para habilitar a integração de aplicativos para o BambooHR, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **BambooHR**.

    ![Galeria de aplicativos](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **BambooHR** e clique em **Concluir** para adicionar o aplicativo.

    ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no BambooHR com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **BambooHR**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Cenário](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")

2.  Na página **Como você deseja que os usuários façam logon no BambooHR**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do BambooHR**, digite a URL usada pelos usuários para fazer logon no seu aplicativo BambooHR (por exemplo: https://company.bamboohr.com) e clique em **Avançar**.

    ![Configurar a URL do aplicativo](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no BambooHR**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar o logon único")

5.  Em outra janela do navegador da Web, faça logon em seu site de empresa BambooHR como um administrador.

6.  Na página inicial, execute as seguintes etapas:

    ![Logon Único](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Logon Único")

    1.  Clique em **Aplicativos**.
    2.  No menu de aplicativos à esquerda, clique em **Logon Único**.
    3.  Clique em **Logon Único do SAML**.

7.  Na seção **Logon Único do SAML**, execute as seguintes etapas:

    ![Logon Único do SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Logon Único do SAML")

    1.  No portal do Azure, na página da caixa de diálogo **Configurar logon único no BambooHR**, copie o valor de **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon do SSO**.
2.  Crie um arquivo **codificado em base 64** usando o certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
    4.  Clique em **Salvar**.

8.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no BambooHR, eles deverão ser provisionados no BambooHR.  
No caso do BambooHR, o provisionamento é uma tarefa manual.

###Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa **BambooHR** como um administrador.

2.  Na barra de ferramentas, na parte superior, clique em **Configurações**.

    ![Configuração](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuração")

3.  Clique em **Visão geral**.

4.  No painel de navegação esquerdo, vá para**Segurança > Usuários**.

5.  Digite o nome de usuário, a senha e o endereço de email de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.

6.  Clique em **Salvar**.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do BambooHR ou as APIs fornecidas pelo BambooHR para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao BambooHR, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **BambooHR**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->