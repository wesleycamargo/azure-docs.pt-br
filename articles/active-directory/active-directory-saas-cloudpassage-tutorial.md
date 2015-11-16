<properties
	pageTitle="Tutorial: integração do Active Directory do Azure ao CloudPassage | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o CloudPassage."
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
	ms.author="markvi"/>


# Tutorial: integração do Active Directory do Azure ao CloudPassage

O objetivo deste tutorial é mostrar a você como integrar o CloudPassage com o Active Directory do Azure (AD do Azure).<br>A integração do CloudPassage com o AD do Azure oferece os seguintes benefícios:

- Você pode controlar, no AD do Azure, quem tem acesso ao CloudPassage 
- Você pode habilitar seus usuários a fazerem logon automaticamente no CloudPassage (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o CloudPassage, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do CloudPassage com logon único habilitado


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de teste do AD do Azure, você pode obter um mês de uma assinatura de avaliação gratuita do Azure [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando CloudPassage da Galeria 
2. Configurar e testar o logon único do Azure AD


## Adicionando CloudPassage da Galeria
Para configurar a integração do CloudPassage com o AD do Azure, você precisa adicionar o CloudPassage, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

### Para adicionar o CloudPassage por meio da galeria, execute as seguintes etapas:

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.<br><br> ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]
6. Na caixa de pesquisa, digite **CloudPassage**.<br><br> ![Aplicativos][5]
7. No painel de resultados, selecione **CloudPassage** e clique em **Concluir** para adicionar o aplicativo.<br><br> ![Aplicativos][6]



##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o CloudPassage, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do CloudPassage é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do CloudPassage.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure ao valor de **nome de usuário** no CloudPassage.
 
Para configurar e testar o logon único do AD do Azure com o CloudPassage, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o Logon Único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do CloudPassage](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no CloudPassage que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testando o logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do CloudPassage.<br> Seu aplicativo CloudPassage espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso.<br><br> ![Configurar o logon único][21]

**Para configurar o logon único do AD do Azure com o CloudPassage, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração de aplicativos do **CloudPassage**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.<br><br> ![Configurar o logon único][7]

2. Na página **Como você deseja que os usuários façam logon no CloudPassage**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br>![Configurar o logon único][8]

3. Na página de caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Definir configurações de aplicativo][9]
 
     3\.1. Na caixa de texto **URL de Logon**, digite o URL usada pelos usuários para entrar no aplicativo CloudPassage (por exemplo: **https://portal.cloudpassage.com/saml/init/accountid*).

     3\.2. Na caixa de texto URL de Resposta, digite a URL de AssertionConsumerService URL (por exemplo: **https://portal.cloudpassage.com/saml/consume/accountid*). <br> Você pode obter o valor para este atributo clicando **documentação de instalação do SSO** na seção **configurações de logon único** do seu portal CloudPassage. <br><br>![Configurar o logon único][10]

     3\.3. Clique em **Próximo**.



4. Na página **Configurar logon único no CloudPassage**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado em seu computador. <br><br>![Configurar o logon único][11]

5. Em outra janela do navegador, entre em seu site de empresa CloudPassage como administrador.

6. No menu, na parte superior, clique em **Configurações**, e, em seguida, clique em **Administração do Site**. <br><br> ![Configurar o logon único][12]

7. Clique na guia **Configurações de Autenticação**. <br><br> ![Configurar o logon único][13]


8. Na seção de **Configurações de Logon Único**, execute as seguintes etapas: <br><br> ![Configurar o logon único][14]


     8\.1. No portal do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor da **URL do emissor** e, em seguida, cole-a na caixa de texto **URL do emissor SAML**.

     8\.2. No portal do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor **Ponto de extremidade iniciado pelo Provedor de Serviço (SP)** e, em seguida, cole-o na caixa de texto **URL de ponto de extremidade de SAML**.

     8\.3. No portal do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor de **URL de Logout** e cole-o na caixa de texto da **página inicial de Logout**.

     8\.4. Crie um arquivo codificado em **base-64** usando o certificado baixado.
          
      >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

     8\.5. Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado x 509**.

     8\.6. Clique em **Salvar**.


9. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.<br><br> ![Configurar o logon único][15]


10. Na página **Confirmação de logon único**, clique em **Concluir**.<br><br> ![Configurar o logon único][16]



11. No menu na parte superior, clique em **Atributos** para abrir a caixa de diálogo **Atributos Token SAML**. <br><br> ![Configurar o logon único][17]

12. Para adicionar os atributos de usuário necessários, para cada linha na tabela a seguir, execute as seguintes etapas: <br>

| Nome do atributo | Valor do atributo |
| --- | --- |
| nome | user.givenname |
| sobrenome | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. Na barra de ferramentas na parte inferior, clique em **Aplicar Alterações**. <br><br> ![Configurar o logon único][20]



### Criação de um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br><br> Na lista Usuários, selecione **Brenda Fernandes**.<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**.<br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.
  3. Clique em Avançar.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária** execute as seguintes etapas:<br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   


  
 
### Criar um usuário de teste CloudPassage

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no CloudPassage.

#### Para criar um usuário chamado Brenda Fernandes no CloudPassage, execute as seguintes etapas:

1.	Faça logon em seu site de empresa do **CloudPassage** como administrador. 

2.	Na barra de ferramentas, na parte superior, clique em **Configurações** e clique em **Administração do Site**. <br>![Criar um usuário de teste CloudPassage][22]

3.	Clique na guia **Usuários** e, em seguida, clique em **Adicionar Novo Usuário**. <br>![Criar um usuário de teste CloudPassage][23]
	
4.	Na seção **Adicionar Novo Usuário**, execute as seguintes etapas: <br>![Criar um usuário de teste CloudPassage][24]

     4\.1. Na caixa de texto **Nome**, digite Brenda.

     4\.2. Na caixa de texto **Sobrenome**, digite Fernandes.

     4\.3. Nas caixas de texto **Nome de usuário**, **E-mail** e **Digite novamente o e-mail**, digite o nome de usuário da Brenda no AD do Azure.

     4\.4. Como **Tipo de acesso**, selecione **Habilitar acesso do Portal de Halo**.

     4\.5. Clique em **Adicionar**.










### Atribuição do usuário de teste do Azure AD

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao CloudPassage.<br><br>![Atribuir usuário][30]

**Para atribuir Brenda Fernandes ao CloudPassage, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.<br> <br><br>![Atribuir usuário][26]
2. Na lista de aplicativos, selecione **CloudPassage**. <br><br>![Atribuir usuário][27]
1. No menu na parte superior, clique em **Usuários**.<br> <br><br>![Atribuir usuário][25]
1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][29]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco CloudPassage no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo CloudPassage.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=Nov15_HO2-->