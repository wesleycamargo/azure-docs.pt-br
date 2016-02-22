<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o StatusPage | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o StatusPage."
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
	ms.date="02/02/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o StatusPage

O objetivo deste tutorial é mostrar a você como integrar o StatusPage ao Active Directory do Azure (AD do Azure).<br>A integração do StatusPage ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao StatusPage 
- Você pode habilitar o logon automático de seus usuários no StatusPage (Logon único) com as contas do AD do Azure
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o StatusPage, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do StatusPage com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o StatusPage da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o StatusPage da galeria
Para configurar a integração do StatusPage ao AD do Azure, você precisará adicionar o StatusPage da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o StatusPage da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **StatusPage**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)<br>
7. Na página de resultados, selecione **StatusPage** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)<br>


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o StatusPage, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do StatusPage é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no StatusPage.<br> Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no StatusPage.
 
Para configurar e testar o logon único do AD do Azure com o StatusPage, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do StatusPage](#creating-a-statuspage-test-user)** - para ter um equivalente de Brenda Fernandes no StatusPage que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo do StatusPage.



**Para configurar o logon único do AD do Azure com o StatusPage, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **StatusPage**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no StatusPage**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br>![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png)<br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) <br>

    > [AZURE.NOTE] Entre em contato com a equipe de suporte do StatusPage em [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)para solicitar os metadados necessários para a configuração do logon único.


    a. Nos metadados, copie o valor de Issuer e cole-o na caixa de texto **Identificador**.

    b. Nos metadados, copie o valor a URL de Resposta e cole-a na caixa de texto **URL de Resposta**.

    c. Clique em **Próximo**.
 
 
4. Na página **Configurar logon único no StatusPage**, execute as seguintes etapas:<br><br>![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


1. Em outra janela do navegador, entre em seu site de empresa do StatusPage como administrador.

1. Na barra de ferramentas principal, clique em **Gerenciar Conta**. <br><br> ![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) <br>


1. Clique na guia **Logon Único**. <br><br> ![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) <br>


1. Na página Instalação do SSO, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) <br>

    a. No portal clássico do Azure, na página do diálogo **Configurar logon único no StatusPage**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Destino de SSO**.

    b. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    c. Clique em **Salvar**.


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.<br> Na lista Usuários, escolha **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste de StatusPage

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no StatusPage. O StatusPage dá suporte ao provisionamento de usuário just-in-time. Você já habilitou isso em [Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).


**Para criar um usuário chamado Brenda Fernandes no StatusPage, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do StatusPage como administrador.

1. Na parte superior do menu, clique em **Gerenciar Conta**.

1. Clique na guia Membros da Equipe. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) <br>

1. Clique em **Adicionar Membro da Equipe**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) <br>

1. Digite o **Endereço de Email**, **Nome** e **Sobrenome** de um usuário válido que você deseja provisionar nas caixas de texto relacionadas.<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png)<br>

1. Como **Função**, escolha **Administrador do Cliente**.

1. Clique em **Criar Conta**.

### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao StatusPage. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao StatusPage, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **StatusPage**. <br><br>![Configurar o logon único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco StatusPage no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo StatusPage.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->