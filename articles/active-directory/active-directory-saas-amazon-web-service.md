<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o AWS (Amazon Web Service) | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o AWS (Amazon Web Service)."
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
	ms.date="08/27/2015"
	ms.author="markvi"/>


# Tutorial: Integração do Active Directory do Azure com o AWS (Amazon Web Service)

O objetivo deste tutorial é mostrar a você como integrar o AWS (Amazon Web Service) ao AD do Azure (Active Directory do Azure).<br>A integração do AWS (Amazon Web Service) ao AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao AWS (Amazon Web Service) 
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Service) (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o AWS (Amazon Web Service), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada de logon único do AWS (Amazon Web Service)


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o AWS (Amazon Web Service) da galeria 
2. Configurar e testar o logon único do Azure AD


## Adicionando o AWS (Amazon Web Service) da galeria
Para configurar a integração do AWS (Amazon Web Service) com o AD do Azure, você precisa adicionar o AWS (Amazon Web Service), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

### Para adicionar o AWS (Amazon Web Service) por meio da galeria, execute as seguintes etapas:

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.<br><br> ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]
6. Na caixa de pesquisa, digite **AWS (Amazon Web Service)**.<br><br> ![Aplicativos][5]
7. No painel de resultados, selecione **AWS (Amazon Web Service)** e clique em **Concluir** para adicionar o aplicativo.<br><br> ![Aplicativos][6]



##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o AWS (Amazon Web Service), com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do AWS (Amazon Web Service) é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do AWS (Amazon Web Service).<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure ao valor de **nome de usuário** no AWS (Amazon Web Service).
 
Para configurar e testar o logon único do AD do Azure com o AWS (Amazon Web Service), você precisa concluir os seguintes blocos de construção:

1. **[Configurando o Logon Único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do AWS (Amazon Web Service)](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no AWS (Amazon Web Service) que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testando o logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do AWS (Amazon Web Service).<br> Seu aplicativo AWS (Amazon Web Service) espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado para a sua configuração de **atributos de token SAML**. A captura de tela a seguir mostra um exemplo disso. <br><br> ![Configurar o logon único][27]

**Para configurar o logon único do AD do Azure com o AWS (Amazon Web Service), execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **AWS (Amazon Web Service)**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.<br><br> ![Configurar o logon único][7]

2. Na página **Como você deseja que os usuários façam logon no AWS (Amazon Web Service)**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br>![Configurar o logon único][8]

3. Na página de diálogo **Definir Configurações de Aplicativo** clique em Avançar. <br><br>![Definir configurações de aplicativo][9]
 
4. Na página **Configurar logon único no AWS (Amazon Web Service)**, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.<br><br>![Configurar o logon único][10]

5. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Service) como administrador.

6. Clique em **Página inicial do Console**. <br><br> ![Configurar o logon único][11]

7. Clique em **Gerenciamento de identidade e acesso**. <br><br> ![Configurar o logon único][12]

8. Clique em **Provedores de Identidade** e, em seguida, clique em **Criar Provedor**. <br><br> ![Configurar o logon único][13]

9. Na página de diálogo **Configurar Provedor**, execute as seguintes etapas: <br><br> ![Configurar o logon único][14]

     9\.1. Como **Tipo de Provedor**, selecione **SAML**.

     9\.2. Na caixa de texto **Nome do Provedor**, digite um nome de provedor (por exemplo: *WAAD*).

     9\.3. Para carregar seu arquivo de metadados baixado, clique em **Escolher Arquivo**.

     9\.4. Clique em **Próxima Etapa**.


10. Na página de diálogo **Verificar Informações do Provedor**, clique em **Criar**. <br><br> ![Configurar o logon único][15]

11. Vá para **funções > Criar Nova Função**. <br><br> ![Configurar o logon único][16]

12. Na caixa de diálogo **Definir Nome de Função**, execute as seguintes etapas: <br><br> ![Configurar o logon único][17] 12.1. Na caixa de texto **Nome da Função**, digite um nome de função (por exemplo: *TestUser*).

     12\.2. Clique em **Próxima Etapa**.

13. Na caixa de diálogo **Selecionar Tipo de Função**, execute as seguintes etapas: <br><br> ![Configurar o logon único][18]

     13\.1. Selecione **Função para acesso ao Provedor de Identidade**.

     13\.2. Na seção **Conceder acesso WebSSO (Logon Único da Web) aos provedores SAML**, clique em **Selecionar**.


14. Na caixa de diálogo Estabelecer Confiança, execute as seguintes etapas: <br><br> ![Configurar o logon único][19] 14,1. Como provedor SAML, selecione o provedor SAML que você criou anteriormente (por exemplo: *WAAD*)



15. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.<br><br>![O que é o Azure AD Connect][20]

16. Na página **Confirmação de logon único** clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.<br><br>![O que é o Azure AD Connect][22]


17. No menu na parte superior, clique em **Atributos** para abrir a caixa de diálogo **Atributos de Token SAML**. <br><br> ![Configurar o logon único][21]

18. Clique em **adicionar atributo de usuário**. <br><br> ![Configurar o logon único][23]

19. Na caixa de diálogo Adicionar Atributo de Usuário, execute as etapas a seguir. <br><br> ![Configurar o logon único][24]

     19\.1. Na caixa de texto **Nome do Atributo**, digite ****https://aws.amazon.com/SAML/Attributes/Role**.

     19\.2. Na caixa de texto **Valor do Atributo**, digite **arn:aws:iam::214510765665:role/Admin,arn:aws:iam::214510765665:saml-provider/WAAD**.

     19\.3. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.

20. Clique em **adicionar atributo de usuário**. <br><br> ![Configurar o logon único][23]


21. Na caixa de diálogo Adicionar Atributo de Usuário, execute as etapas a seguir. <br><br> ![Configurar o logon único][25]

     21\.1. Na caixa de texto **Nome do Atributo**, digite ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     21\.2. Na lista **Valor do Atributo**, selecione **usuário:email**.

     21\.3. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.


22. Clique em **Aplicar Alterações**. <br><br> ![Configurar o logon único][26]





### Criação de um usuário de teste do Azure AD

O objetivo desta seção é criar uma usuária de teste no portal do Azure chamada Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**.<br> ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.
  3. Clique em Avançar.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária** execute as seguintes etapas:<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   
  
 
### Criar um usuário de teste do AWS (Amazon Web Service)

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Service).

### Para criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Service), execute as seguintes etapas:

1. Faça logon no seu site de empresa do **AWS (Amazon Web Service)** como administrador.

2. Clique no ícone de **Página Inicial do Console**. <br><br> ![Configurar o logon único][11]

3. Clique em Gerenciamento de Identidade e Acesso. <br><br> ![Configurar o logon único][28]

4. No Painel, clique em Usuários e, em seguida, clique em Criar Novos Usuários. <br><br> ![Configurar o logon único][29]

5. Na caixa de diálogo Criar Usuário, execute as seguintes etapas: <br><br> ![Configurar o logon único][30]

     5\.1. Nas caixas de texto **Inserir Nomes de Usuário**, digite o nome de usuário de Brenda Fernandes no AD do Azure.

     5\.2. Clique em **Criar**.




### Atribuição do usuário de teste do Azure AD

O objetivo desta seção é habilitar que Britta Simon use o logon único do Azure, concedendo a ela acesso ao AWS (Amazon Web Service).<br><br>![Atribuir usuário][31]

**Para atribuir Brenda Fernandes ao CloudPassage, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.<br> <br><br>![Atribuir usuário][26]
2. Na lista de aplicativos, selecione **AWS (Amazon Web Service)**. <br><br>![Atribuir usuário][27]
1. No menu na parte superior, clique em **Usuários**.<br> <br><br>![Atribuir usuário][25]
1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][29]

### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando clica no bloco AWS (Amazon Web Service) no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo AWS (Amazon Web Service).


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png

<!---HONumber=August15_HO9-->