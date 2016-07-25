<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Concur | Microsoft Azure" 
    description="Saiba como usar o Concur com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao Concur  


O objetivo deste tutorial é mostrar a integração do Azure ao Concur. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no Concur

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Concur
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-concur-tutorial/IC769766.png "Cenário")

>[AZURE.NOTE] A configuração de sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, e você deve entrar em contato com o Concur para executá-la.

##Habilitando a integração de aplicativos para o Concur

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Concur.

###Para habilitar a integração de aplicativos com o Concur, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-concur-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de Aplicativos**, clique em **Adicionar um Aplicativo** e em **Adicionar um aplicativo a ser utilizado pela minha organização**.

    ![O que você deseja fazer?](./media/active-directory-saas-concur-tutorial/IC700995.png "O que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Concur**.

    ![Galeria de Aplicativos](./media/active-directory-saas-concur-tutorial/IC721727.png "Galeria de Aplicativos")

6.  No painel de resultados, selecione **Concur** e clique em **Concluir** para adicionar o aplicativo.

    ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Concur com a própria conta no Azure AD usando federação baseada no protocolo SAML.

>[AZURE.NOTE] A configuração de sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, e você deve entrar em contato com o Concur para executá-la.

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração do aplicativo **Concur**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-concur-tutorial/IC769767.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Concur**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-concur-tutorial/IC769768.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, a caixa de texto **URL de Entrada do Concur**, digite a URL de entrada do locatário do Concur e clique em **Avançar**:

    ![Configurar a URL de entrada](./media/active-directory-saas-concur-tutorial/IC769769.png "Configurar a URL de entrada")

4.  Na página **Configurar logon único no Concur**, realize as etapas a seguir.

    ![Configurar a URL de entrada](./media/active-directory-saas-concur-tutorial/IC769770.png "Configurar a URL de entrada")

    1.  Clique em Baixar metadados e salve o arquivo de dados em seu computador.
    2.  Entre em contato com a equipe de suporte do Concur para configurar o SSO para seu locatário.
    3.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar o diálogo **Configurar Logon Único**.

	>[AZURE.NOTE] A configuração de sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, e você deve entrar em contato com o Concur para executá-la.

##Configurando o provisionamento de usuários

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Concur.

Para habilitar os aplicativos no serviço de Despesas, é necessário configurar e usar um perfil de Administrador de Serviço Web. Não adicione simplesmente a função de Administrador de Serviço Web ao seu perfil de administrador existente usado para as funções administrativas de T&E.

O consultor do Concur ou o administrador do cliente deve criar um perfil de Administrador de Serviço Web distinto, e o administrador do cliente deve usar esse perfil para as funções de Administrador de Serviço Web (por exemplo, habilitação de aplicativos). Esses perfis devem ser mantidos separados do perfil de administração diária de T&E do administrador do cliente (o perfil de administração de T&E não deve ter a função de Administrador de Serviço Web).

Ao criar o perfil que será usado para habilitar o aplicativo, insira o nome do administrador do cliente nos campos de perfil do usuário. Isso atribuirá propriedade ao perfil. Após a criação do(s) perfil(s), o cliente deverá fazer logon com esse perfil para clicar no botão “*Habilitar*” de um Aplicativo do Parceiro no menu dos Serviços Web.

Essa ação não deve ser feita com o perfil usado para a administração normal de T&E pelos seguintes motivos.

1.  É o cliente que deve clicar em “*Sim*” na janela do diálogo exibida após a habilitação de um aplicativo. Esse clique confirma que o cliente está disposto a permitir que o Aplicativo parceiro acesse seus dados, portanto você ou o Parceiro não pode clicar no botão Sim.
2.  Se um administrador de cliente que habilitou um aplicativo usando o perfil de administrador de T&E deixar a empresa (resultando na inativação do perfil), quaisquer aplicativos habilitados com esse perfil não funcionarão até que sejam habilitados com outro perfil ativo de Administrador de Serviços Web. É por isso que você deve criar perfis de Administrador de Serviços Web distintos.
3.  Se um administrador deixar a empresa, o nome associado ao perfil de Administrador de Serviço Web pode ser alterado para o administrador substituto, se isso for desejado, sem afetar o aplicativo habilitado, porque esse perfil não ficará inativado

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  Faça logon em seu locatário do **Concur**.

2.  No menu **Administração**, selecione **Serviços Web**.

    ![Locatário do Concur](./media/active-directory-saas-concur-tutorial/IC721729.png "Locatário do Concur")

3.  No lado esquerdo, no painel **Serviços Web**, selecione **Habilitar Aplicativo do Parceiro**.

    ![Habilitar Aplicativo parceiro](./media/active-directory-saas-concur-tutorial/IC721730.png "Habilitar Aplicativo parceiro")

4.  Na lista **Habilitar Aplicativo**, selecione **Active Directory do Azure** e clique em **Habilitar**.

    ![Active Directory do Microsoft Azure](./media/active-directory-saas-concur-tutorial/IC721731.png "Active Directory do Microsoft Azure")

5.  Clique em **Sim** para fechar o diálogo **Confirmar Ação**.

    ![Confirmar Ação](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmar Ação")

6.  No portal clássico do Azure, selecione **Concur** na lista de aplicativos para abrir a página do diálogo **Concur**.

7.  Para abrir a página **Configurar Provisionamento de Usuário**, clique em **Configurar provisionamento de usuário**.

8.  Insira o nome de usuário e a senha do administrador do Concur e clique em **Avançar**.

9.  Para concluir a configuração, na página **Confirmação**, clique no botão **Concluir**.

Agora, você pode criar uma conta de teste, aguardar 10 minutos e verificar se a conta foi sincronizada com o Concur.
##Atribuindo usuários

Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

###Para atribuir usuários ao Concur, execute as etapas a seguir:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração do aplicativo **Concur**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-concur-tutorial/IC769771.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-concur-tutorial/IC767830.png "Sim")

Agora, você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o Concur.

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->