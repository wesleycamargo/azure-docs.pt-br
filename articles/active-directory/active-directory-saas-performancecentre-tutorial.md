<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o PerformanceCentre | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o PerformanceCentre."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="markusvi"/>


# Tutorial: Integração do Active Directory do Azure com o PerformanceCentre

O objetivo deste tutorial é mostrar a você como integrar o PerformanceCentre ao Active Directory do Azure (AD do Azure).<br>A integração do PerformanceCentre ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao PerformanceCentre 
- Você pode permitir que seus usuários façam logon automaticamente no PerformanceCentre (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o PerformanceCentre, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do PerformanceCentre com logon único habilitado


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do AD do Azure em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o PerformanceCentre da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o PerformanceCentre da galeria
Para configurar a integração do PerformanceCentre ao AD do Azure, você precisará adicionar o PerformanceCentre da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PerformanceCentre da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **PerformanceCentre**.<br><br> ![Aplicativos][5]<br>
7. No painel de resultados, selecione **PerformanceCentre** e clique em **Concluir** para adicionar o aplicativo.<br><br>![Aplicativos][500]<br>


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o PerformanceCentre, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do PerformanceCentre é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do PerformanceCentre.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure ao valor de **Nome de Usuário** no PerformanceCentre.
 
Para configurar e testar o logon único do AD do Azure com o PerformanceCentre, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do PerformanceCentre](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no PerformanceCentre que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do PerformanceCentre.<br>

**Para configurar o logon único do AD do Azure com o PerformanceCentre, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **PerformanceCentre**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.<br><br>![Configurar o logon único][6]<br>

2. Na página **Como você deseja que os usuários façam logon no PerformanceCentre**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br>![Logon único do AD do Azure][7]<br>

3. Na caixa de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas: <br><br>![Logon único do AD do Azure][8] <br>
 
     a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no site do PerformanceCentre (por exemplo: **http://companyname.performancecentre.com/saml/SSO*).
 
     b. Clique em **Próximo**.
 
4. Na página **Configurar logon único no PerformanceCentre**, execute as seguintes etapas:<br><br>![Logon único do AD do Azure][9]<br>

    a. Clique em **Baixar metadados** e salve o arquivo em seu computador.



1. Faça logon no site da empresa **PerformanceCentre** como administrador.

2. Na guia à esquerda, clique em **Configurar**. <br><br>![Logon único do AD do Azure][10]

2. Na guia à esquerda, clique em **Diversos** e clique em **Logon Único**. <br><br>![Logon único do AD do Azure][11]

2. Para o **Protocolo**, selecione **SAML**. <br><br>![Logon único do AD do Azure][12]

2. Abra o arquivo de metadados baixado no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade** e clique em **Salvar**. <br><br>![Logon único do AD do Azure][13]

2. Verifique se os valores para **URL Base da Entidade** e **URL da ID da Entidade** estão corretos. <br><br>![Logon único do AD do Azure][14]


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][15]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][16]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar no portal do Azure um usuário de teste chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. Na página da caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário de teste do PerformanceCentre

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no PerformanceCentre.

**Para criar um usuário chamado Brenda Fernandes no PerformanceCentre, execute as seguintes etapas:**

1. Faça logon no site da empresa PerformanceCentre como administrador.

2. No menu à esquerda, clique em **Inter-relacionado**, e clique em **Criar Participante**. <br><br>![Criar Usuário][400]<br>

4. Na caixa de diálogo **Inter-relacionado - Criar Participante**, execute as seguintes etapas: <br><br>![Criar Usuário][401]<br>

    a. Digite os atributos necessários para Brenda Fernandes nas caixas de texto relacionadas.
    
    > [AZURE.IMPORTANT]O atributo Nome de Usuário de Brenda no PerformanceCentre deve ser igual ao Nome de Usuário no AD do Azure.


    b. Selecione **Administrador Cliente** em **Escolher Função**.

    c. Clique em **Salvar**.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao PerformanceCentre. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao PerformanceCentre, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição dos aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>
2. Na lista de aplicativos, selecione **PerformanceCentre**. <br><br>![Atribuir usuário][202]<br>
1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>
1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando clica no bloco PerformanceCentre no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo do PerformanceCentre.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=Nov15_HO2-->