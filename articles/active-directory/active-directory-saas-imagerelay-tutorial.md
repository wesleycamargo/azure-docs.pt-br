<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o ImageRelay | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o ImageRelay."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o ImageRelay

O objetivo deste tutorial é mostrar como integrar o ImageRelay ao Active Directory do Azure (AD do Azure). <br>A integração do ImageRelay ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao ImageRelay
- Você pode permitir que seus usuários façam logon automaticamente no ImageRelay (logon único) com as contas do AD do Azure
- Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do AD do Azure ao ImageRelay, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ImageRelay


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ImageRelay da galeria

2. Configurar e testar o logon único do AD do Azure


## Adicionando o ImageRelay da galeria
Para configurar a integração do ImageRelay ao AD do Azure, você precisará adicionar o ImageRelay da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ImageRelay da galeria, execute as seguintes etapas:**

1. No portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br>![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **ImageRelay**.<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. No painel de resultados, selecione **ImageRelay** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)<br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o ImageRelay com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa de uma conta de usuário que represente o usuário relacionado no ImageRelay. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do ImageRelay.<br> Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no ImageRelay.

Para configurar e testar o logon único do AD do Azure com o ImageRelay, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do ImageRelay](#creating-a-userecho-test-user)**: para ter um equivalente de Brenda Fernandes no ImageRelay que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no Portal Clássico do AD do Azure e configurar o logon único em seu aplicativo ImageRelay.


**Para configurar o logon único do AD do Azure com o ImageRelay, execute as seguintes etapas:**

1. No Portal Clássico do AD do Azure, na página de integração do aplicativo **ImageRelay**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

     ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no ImageRelay**, selecione **Logon único do AD do Azure** e clique em **Próximo**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

     ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo ImageRelay (por exemplo: **https://fabrikam.ImageRelay.com/*).

    b. Clique em **Próximo**.

4. Na página **Configurar logon único no ImageRelay**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.

5. Em outra janela do navegador, entre em seu site de empresa do ImageRelay como administrador.

    a. Na barra de ferramentas na parte superior, clique na carga de trabalho **Usuários e Permissões**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) <br>

    b. Clique em **Criar Nova Permissão**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) <br>

    c. Na carga de trabalho **Configurações de Logon Único**, marque a caixa de seleção **Este Grupo pode apenas entrar via logon único** e clique em **Salvar**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. Vá para **Configurações da Conta**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png)<br>

    e. Vá para a carga de trabalho **Configurações de Logon Único**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. Preencha o formulário como indicado e clique em **Salvar**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **URL de Logon (SSO)**: é a URL do serviço de logon único do Active Directory do Azure.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **URL do Serviço de Logoff**: é o serviço de saída única do Active Directory do Azure.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - Em **Formato da Id de Nome**, selecione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - Em **Opções de Vinculação para Solicitações do Provedor de Serviço (Image Relay)**, selecione **Associação POST**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>

  	- Em **Certificado x.509**, clique em **Atualizar Certificado**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - No Bloco de Notas, abra o certificado baixado do Active Directory do Azure na etapa 4, copie e cole o conteúdo do certificado aqui.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - Em **Provisionamento do Usuário Just-In-Time**, marque a caixa de seleção **Habilitar o Provisionamento do Usuário Just-In-Time**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - Selecione o grupo de permissão (por exemplo, **SSO Básico**) que terá permissão para entrar somente por meio de logon único.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. No Portal clássico do AD do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**.

    ![Logon único do AD do Azure][11]


### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do AD do Azure chamado Brenda Fernandes.<br> Na lista Usuários, escolha **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal Clássico do AD do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png)<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de diálogo **Perfil do Usuário**, execute as seguintes etapas:<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. Na página de diálogo **Obter senha temporária**, execute as seguintes etapas:<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste ImageRelay

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ImageRelay.

**Para criar um usuário chamado Brenda Fernandes no ImageRelay, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do ImageRelay como administrador.

1. Vá para **Usuários e Permissões** e selecione **Criar Usuário SSO**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. Insira o **Email**, o **Nome**, o **Sobrenome** e a **Empresa** do usuário que você deseja configurar e selecione o grupo de permissão (por exemplo, SSO Básico), que é o grupo que pode se conectar somente por meio de logon único.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. Clique em **Criar**.

### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao ImageRelay. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao ImageRelay, execute as seguintes etapas:**

1. No Portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **ImageRelay**.<br><br>![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. No menu na parte superior, clique em **Usuários**.<br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.<br><br>![Atribuir usuário][205]


### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clicar no bloco ImageRelay no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo ImageRelay.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->