<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o AWS (Amazon Web Service) | Microsoft Azure"
	description="Saiba como usar o AWS (Amazon Web Service) com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
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
	ms.date="09/30/2015"
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

1. No **Portal de Gerenciamento do Azure**, no painel de navegação esquerdo, clique em **Active Directory**. <br><br>![Active Directory][1]<br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Aplicativos][2]<br>

4. Clique em **Adicionar** na parte inferior da página. <br><br>![Aplicativos][3]<br>

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**. <br><br>![Aplicativos][4]<br>

6. Na caixa de pesquisa, digite **serviço Amazon Web (AWS)**. <br><br>![Aplicativos][5]<br>

7. No painel de resultados, selecione **Serviço Amazon Web (AWS)**, e, em seguida, clique em **Concluir** para adicionar o aplicativo. <br><br>![Aplicativos][6]<br>



##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o AWS (Amazon Web Service), com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do AWS (Amazon Web Service) é equivalente a um usuário do AD do Azure. Em outras palavras, uma relação de link entre um usuário do AD do Azure e o usuário relacionado no serviço AWS (Amazon Web) deve ser estabelecida.<br> Essa relação de vínculo é estabelecida, atribuindo-se o valor do **nome de usuário** no AD do Azure como o valor da **nome de usuário** no serviço AWS (Amazon Web).
 
Para configurar e testar o logon único do AD do Azure com o AWS (Amazon Web Service), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários para usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Simon Britta.
4. **[Criar um usuário de teste do serviço AWS (Amazon Web)](#creating-a-halogen-software-test-user)** - para ter um equivalente de Simon Britta no serviço AWS (Amazon Web) que está vinculado à representação do AD do Azure dela.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - habilitar Simon Britta a usar o logon único do AD do Azure.
5. **[Testes do Logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Service).<br> Seu aplicativo AWS (Amazon Web Service) espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado à sua configuração **atributos de token saml**. A captura de tela a seguir mostra um exemplo disso.


<br><br>![Configurar o logon único][27]<br>

**Para configurar o logon único do AD do Azure com o AWS (Amazon Web Service), execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **AWS (Amazon Web Service)**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**. <br><br>![Configurar o logon único][7]<br>

2. Na página **Como você gostaria que os usuários fizessem logon na página do AWS (Amazon Web Service)**, selecione **Logon único do AD do Azure**, e, em seguida, clique em **Próximo**. <br><br>![Configurar o logon único][8]<br>

3. Na caixa de diálogo **Definir configurações de aplicativo**, clique em Avançar. <br><br>![Definir configurações de aplicativo][9]<br>
 
4. Na página **Configurar logon único no serviço AWS (Amazon Web Service)** clique em **Baixar metadados**, e, em seguida, salve o arquivo de metadados localmente em seu computador. <br><br>![Configurar o logon único][10]<br>

5. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Service) como administrador.

6. Clique em **página inicial do Console**. <br><br>![Configurar o logon único][11]<br>

7. Clique em **Gerenciamento de identidades e acesso**. <br><br>![Configurar o logon único][12]<br>

8. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**. <br><br>![Configurar o logon único][13]<br>

9. Na página da caixa de diálogo **Configurar provedor**, execute as seguintes etapas: <br><br>![Configurar o logon único][14]<br>

     a. Como **Tipo de provedor**, selecione **SAML**.

     b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (p. ex.: *WAAD*).

     c. Para carregar o arquivo de metadados baixado, clique em **Escolher arquivo**.

     d. Clique em **Próxima etapa**.


10. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**. <br><br>![Configurar o logon único][15]<br>

11. Clique em **Funções**, e, em seguida, clique em **Criar nova função**. <br><br>![Configurar o logon único][16]<br>

12. Na caixa de diálogo **Definir nome de função**, execute as seguintes etapas: <br><br>![Configurar o logon único][17]<br>

     a. Na caixa de texto **Nome da função**, digite um nome de função (por exemplo: *TestUser*).

     b. Clique em **Próxima etapa**.

13. Na caixa de diálogo **Selecionar tipo de função**, execute as seguintes etapas: <br><br>![Configurar o logon único][18]<br>

     a. Selecione **Função de acesso do provedor de identidade**.

     b. Na seção **Conceder acesso de logon único da Web (WebSSO) a provedores SAML**, clique em **Selecionar**.


14. Na caixa de diálogo **Estabelecer confiança**, execute as seguintes etapas: <br><br>![Configurar o logon único][19]<br>
     
     a. Como provedor SAML, selecione o provedor SAML criado previamente (p. ex.: *WAAD*)

     b. Clique em **Próxima etapa**.


15. Na caixa de diálogo **Verificar confiança na função**, clique em **Próxima etapa**. <br><br>![Configurar o logon único][32]<br>


16. na caixa de diálogo **Anexar política**, clique em **Próxima etapa**. <br><br>![Configurar o logon único][33]<br>


17. Na caixa de diálogo **Revisão**, execute as seguintes etapas: <br><br>![Configurar o logon único][34]<br>

     a. Cópia do valor **Função ARN**.

     b. Cópia do valor ARN de **Entidades confiáveis**.

     c. Clique em **Criar função**.

18. No portal do AD do Azure, selecione a confirmação de configuração de logon único e, em seguida, clique em **Próximo**. <br><br>![O que é o Azure AD Connect][20]<br>

19. Na página **Confirmação de logon único**, clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**. <br><br>![O que é o Azure AD Connect][22]<br>


20. No menu na parte superior, clique em **Atributos** para abrir a caixa de diálogo **Atributos de Token SAML**. <br><br>![Configurar o logon único][21]<br>

21. Clique em **Adicionar atributo de usuário**. <br><br>![Configurar o logon único][23]<br>

22. Na caixa de diálogo Adicionar Atributo de Usuário, execute as seguintes etapas. <br><br>![Configurar o logon único][24]<br>

     a. Na caixa de texto **Nome do Atributo**, digite ****https://aws.amazon.com/SAML/Attributes/Role**.

     b. Na caixa de texto **Valor do atributo**, digite **[o valor da função ARN], [o valor ARN de entidade confiável]**.

     >[AZURE.TIP]Estes são os valores que você copiou da caixa de diálogo Revisão quando criou sua função.

     c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo de usuário**.

23. Clique em **Adicionar atributo de usuário**. <br><br>![Configurar o logon único][23]<br>


24. Na caixa de diálogo Adicionar Atributo de Usuário, execute as seguintes etapas. <br><br>![Configurar o logon único][25]<br>


     a. Na caixa de texto **Nome do Atributo**, digite ****https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Na caixa de texto **Valor do atributo**, tipo **mail**.

     c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo de usuário**.


25. Clique em **Aplicar alterações**. <br><br>![Configurar o logon único][26]<br>





### Criação de um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Simon Britta.<br> Na lista de usuários, selecione **Britta Simon**.<br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)<br>

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação esquerdo, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Para abrir a caixa de diálogo **Adicionar usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. Na página de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>

  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do usuário, digite **BrittaSimon**.
  3. Clique em Avançar.

6.  Na caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>

  1. Na caixa de texto **Nome**, digite **Britta**.
  2. Na caixa de texto **Sobrenome**, digite **Simon**.
  3. Na caixa de texto **Nome de exibição**, digite **Britta Simon**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**. <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. Na página de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>

  1. Anote o valor da **Nova senha**.
  2. Clique em **Concluído**.   
  
 
### Criar um usuário de teste do AWS (Amazon Web Service)

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Service).

### Para criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Service), execute as seguintes etapas:

1. Faça logon no site da sua empresa **AWS (Amazon Web Service)** como administrador.

2. Clique no ícone **Página inicial do Console**. <br><br>![Configurar o logon único][11]<br>

3. Clique em Gerenciamento de identidades e acesso. <br><br>![Configurar o logon único][28]<br>

4. No painel, clique em usuários e, em seguida, clique em criar novos usuários. <br><br>![Configurar o logon único][29]<br>

5. Na caixa de diálogo Criar usuário, execute a seguintes etapas: <br><br>![Configurar o logon único][30]<br>

     a. Na caixa de texto **Inserir nomes de usuário**, digite o nome de usuário Simon Brita de Simon no AD do Azure.

     b. Clique em **Criar**.




### Atribuição do usuário de teste do Azure AD

O objetivo desta seção é habilitar Simon Britta a usar o logon único do Azure, concedendo o acesso ao serviço AWS (Amazon Web Service).

![Atribuir usuário][31]

**Para atribuir Brenda Fernandes ao CloudPassage, execute as seguintes etapas:**

1. No portal do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior. <br><br>![Atribuir usuário][26]<br>

2. Na lista de aplicativos, selecione **AWS (Amazon Web Service)**. <br><br>![Atribuir usuário][27]<br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][25]<br>

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][29]<br>

### Teste do logon único

O objetivo desta seção é testar a sua configuração de logon único do AD do Azure usando o painel de acesso.<br> Quando clica no bloco AWS (Amazon Web Service) no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo AWS (Amazon Web Service).


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
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png

<!---HONumber=Oct15_HO1-->