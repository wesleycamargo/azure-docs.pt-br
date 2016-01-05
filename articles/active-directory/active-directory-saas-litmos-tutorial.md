<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure ao Litmos | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Litmos."
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


# Tutorial: Integração do Active Directory do Azure ao Litmos

O objetivo deste tutorial é mostrar como integrar o Litmos ao AD do Azure (Active Directory do Azure).<br>A integração do Litmos ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao Litmos 
- Você pode permitir que usuários façam logon automaticamente no Litmos (logon único) com as respectivas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao Litmos, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Litmos


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do AD do Azure em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o Litmos da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Litmos da galeria
Para configurar a integração do Litmos ao AD do Azure, você precisará adicionar o Litmos da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Litmos da galeria, execute as seguintes etapas:**

1. No **portal do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Litmos**.<br><br> ![Aplicativos][5]<br>
7. No painel de resultados, selecione **Litmos** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Aplicativos][500]<br>


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Litmos, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Litmos é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Litmos.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor de **nome de usuário** no Litmos.
 
Para configurar e testar o logon único do AD do Azure com o Litmos, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste do Litmos](#creating-a-halogen-software-test-user)**: para ter um equivalente de Brenda Fernandes no Litmos que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo Litmos.<br> Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Como parte da configuração, você precisa personalizar os **atributos Token SAML** para seu aplicativo Litmos. <br><br> ![Logon único do AD do Azure][17] <br>

**Para configurar o logon único do AD do Azure com o Litmos, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **Litmos**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Litmos**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br> ![Logon único do AD do Azure][7] <br>


1. Faça logon no site da sua empresa no Litmos (por exemplo: **https://azureapptest.litmos.com/account/Login*) como administrador. <br><br> ![Logon único do AD do Azure][21] <br>


1. Na barra de navegação à esquerda, clique em **Contas**. <br><br> ![Logon único do AD do Azure][22] <br>


1. Clique na guia **Integrações**. <br><br> ![Logon único do AD do Azure][23] <br>


1. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**. <br><br> ![Logon único do AD do Azure][24] <br>

1. Copie o valor em **O ponto de extremidade SAML para litmos é:**. <br><br> ![Logon único do AD do Azure][26] <br>


3. No portal do Azure, na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Logon único do AD do Azure][8] <br>
 
    a. Na caixa de texto **Identificador**, digite a URL usada pelos usuários para fazer logon no aplicativo do Litmos (por ex.: **https://azureapptest.litmos.com/account/Login*)).
     
    b. Na caixa de texto **URL de resposta**, cole o valor que você copiou do aplicativo Litmos na etapa anterior.

    c. Clique em **Próximo**.
 
4. Na página **Configurar logon único no Litmos**, execute as seguintes etapas: <br><br>![Logon único do AD do Azure][2] <br>

    a. Clique em Baixar certificado e salve o certificado localmente no computador.


1. No seu aplicativo **Litmos**, execute as seguintes etapas: <br><br>![Logon único do AD do Azure][25] <br>

    a. Clique em **Habilitar SAML**.

    b. Crie um arquivo **codificado em base 64** usando o certificado baixado.

    >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    c. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado SAML X.509**.

    d. Clique em **Salvar Alterações**.


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]


20. Na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**. <br><br>![Configurar o logon único][12]<br>


24. Na caixa de diálogo **Adicionar Atributo de Usuário**, execute as seguintes etapas. <br><br>![Configurar o logon único][14]<br>

    | Nome do atributo | Valor do atributo |
    | ---            | ---             |
    | Email | user.mail |
    | Nome | user.givenname |
    | Sobrenome | user.surname |

    Para cada linha de dados na tabela acima, execute as seguintes etapas:
   
    a. Clique em **adicionar atributo de usuário**. <br><br>![Configurar o logon único][15]<br>


    a. Na caixa de texto **Nome do Atributo**, digite o **Nome do Atributo** mostrado para a linha.

    b. Selecione o **Valor do Atributo** mostrado para a linha.

    c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.


25. Clique em **Aplicar alterações**. <br><br>![Configurar o logon único][16]<br>




### Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar no portal do Azure um usuário de teste chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. Na página da caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br>

    a. Como **Tipo de Usuário**, selecione **Novo usuário na organização**.

    b. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do Litmos

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Litmos.<br> O aplicativo Litmos oferece suporte ao provisionamento Just-in-Time. Isso significa que, se for necessário, uma conta de usuário será criada automaticamente durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.

**Para criar um usuário chamado Brenda Fernandes no Litmos, execute as seguintes etapas:**


1. Faça logon no site da sua empresa no Litmos (por exemplo: **https://azureapptest.litmos.com/account/Login*) como administrador. <br><br> ![Logon único do AD do Azure][21] <br>


1. Na barra de navegação à esquerda, clique em **Contas**. <br><br> ![Logon único do AD do Azure][22] <br>


1. Clique na guia **Integrações**. <br><br> ![Logon único do AD do Azure][23] <br>


1. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**. <br><br> ![Logon único do AD do Azure][24] <br>

1. Selecione **Gerar Usuários Automaticamente:**. <br><br> ![Logon único do AD do Azure][27] <br>


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Litmos. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Litmos, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição dos aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>
2. Na lista de aplicativos, selecione **Litmos**. <br><br>![Atribuir usuário][202] <br>
1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>
1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Ao clicar no bloco Litmos no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Litmos.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png

<!---HONumber=AcomDC_1217_2015-->