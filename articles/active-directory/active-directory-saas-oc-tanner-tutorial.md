<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure ao O.C. Tanner - AppreciateHub | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o O.C. Tanner - AppreciateHub."
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
	ms.date="10/09/2015"
	ms.author="markusvi"/>


# Tutorial: Integração do Active Directory do Azure ao O.C. Tanner - AppreciateHub

O objetivo deste tutorial é mostrar como integrar o O.C. Tanner - AppreciateHub ao Active Directory do Azure (AD do Azure).<br>Integração do O.C. O Tanner - AppreciateHub ao AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem terá acesso ao O.C. Tanner - AppreciateHub 
- Você pode permitir que seus usuários façam logon automaticamente no O.C. Tanner - AppreciateHub (Logon Único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao O.C. Tanner - AppreciateHub, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um Logon único do Tanner - AppreciateHub em uma assinatura habilitada


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do AD do Azure em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionar o O.C. Tanner - AppreciateHub a partir da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionar o O.C. Tanner - AppreciateHub a partir da galeria
Para configurar a integração do O.C. Tanner - AppreciateHub no AD do Azure, você precisa adicionar O.C. Tanner - AppreciateHub a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o O.C. Tanner - AppreciateHub da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory.** <br><br> ![Active Directory][1] <br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior. <br><br> ![Aplicativos][2] <br>

4. Clique em **Adicionar** na parte inferior da página. <br><br> ![Aplicativos][3] <br>

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**. <br><br> ![Aplicativos][4] <br>

6. Na caixa de pesquisa, digite **O.C. Tanner - AppreciateHub**. <br><br> ![Aplicativos][5] <br>

7. No painel de resultados, selecione **O.C. Tanner - AppreciateHub** e clique em **Concluir** para adicionar o aplicativo. <br><br> ![Aplicativos][25] <br>




##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o O.C. Tanner - AppreciateHub com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual o usuário do O.C. Tanner - AppreciateHub é equivalente a um usuário no AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no O.C. Tanner - AppreciateHub precisa ser estabelecido.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como sendo o valor de **nome de usuário** no O.C. Tanner - AppreciateHub.
 
Para configurar e testar o logon único do AD do Azure com O.C. Tanner - AppreciateHub, é necessário concluir os seguintes blocos de criação:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário teste do O.C. Tanner - AppreciateHub](#creating-a-halogen-software-test-user)** - ter um equivalente de Brenda Fernandes no O.C. Tanner - AppreciateHub que é vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo O.C. Tanner - AppreciateHub.<br>

**Para configurar o logon único do AD do Azure com O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **O.C. Tanner - AppreciateHub** clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.<br><br> ![Configurar o logon único][6]

2. Em **Como você gostaria que os usuários fizessem logon n página O.C. Tanner - AppreciateHub**, selecione **Logon Único do AD do Azure** e clique em **Avançar**.<br><br> ![Logon único do AD do Azure][7]

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Definir configurações de aplicativo][8]
 
     a. Abra o arquivo de metadados usando o seguinte link: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Localize o nó **md:AssertionConsumerService**.

     c. Copie o valor do atributo **Local**. <br><br>![Definir configurações de aplicativo][12]
     
     d. Na caixa de texto **URL do Logon**, antes do valor que você obteve na etapa anterior.

     >[AZURE.NOTE]Se você estiver tendo problemas para obter a URL de resposta do arquivo de metadados, entre em contato com a equipe de suporte do O.C. Tanner - AppreciateHub por meio do [sso@octanner.com](mailto:sso@octanner.com).

     e. Clique em **Avançar**.
 
4. Na página **Configurar logon único no O.C. Tanner - AppreciateHub**, clique em **Baixar metadados** e então salve o arquivo de metadados localmente em seu computador.<br><br>![O que é o Azure AD Connect][9]

5. Entre em contato com a equipe de suporte do O.C. Tanner - AppreciateHub por meio do xyz, forneça o arquivo de metadados e avise que eles devem habilitar o SSO para você.


6. No portal do AD do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. <br><br>![O que é o Azure AD Connect][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![O que é o Azure AD Connect][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.<br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página de caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária** execute as seguintes etapas:<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário teste do O.C. Usuário de teste do Tanner - AppreciateHub

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub.

**Para criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

1. Peça à sua equipe de suporte do OC Tanner para criar um usuário que tenha o atributo nameID com o mesmo valor que o nome de usuário Brenda Fernandes no AD do Azure.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes para usar o logon único do Azure, concedendo a ela acesso ao O.C. Tanner - AppreciateHub. <br><br>![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.<br> <br><br>![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **O.C. Tanner - AppreciateHub**. <br><br>![Atribuir usuário][202]
1. No menu na parte superior, clique em **Usuários**.<br> <br><br>![Atribuir usuário][203]
1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco O.C. Tanner - AppreciateHub no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo O.C. Aplicativo Tanner - AppreciateHub.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=Oct15_HO3-->