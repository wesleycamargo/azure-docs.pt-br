<properties
	pageTitle="Tutorial: integração do Azure Active Directory ao Weekdone | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Weekdone."
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
	ms.date="04/22/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory ao Weekdone

O objetivo desse tutorial é mostrar como integrar o Weekdone ao Azure AD (Azure Active Directory).

A integração do Weekdone ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Weekdone
- Você pode habilitar seus usuários a fazerem logon automaticamente no Weekdone (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Weekdone, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Weekdone com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Weekdone da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando Weekdone da galeria
Para configurar a integração do Weekdone ao Azure AD, você precisa adicionar o Weekdone na galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Weekdone da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Weekdone**.
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_01.png)

7. No painel de resultados, selecione **Weekdone** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_02.png)


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Weekdone, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Weekdone é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Weekdone.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Weekdone.

Para configurar e testar o logon único do Azure AD com o Weekdone, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Weekdone](#creating-a-weekdone-test-user)**: para ter um equivalente de Brenda Fernandes no Weekdone que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Weekdone.



**Para configurar o logon único do Azure AD com o Weekdone, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Weekdone**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Weekdone**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 
	![Configurar o logon único](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_03.png)

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo em **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:

	![Configurar o logon único](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_04.png)


    a. Na caixa de texto **URL de Resposta**, digite a URL usando o seguinte padrão: **"https://weekdone.com/a/azure"**

	b. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: **"https://weekdone.com/a/azure/metadata"**.

	c. Clique em **Próximo**.

4. Se quiser configurar o aplicativo em **modo iniciado pelo SP**, na página da caixa de diálogo **Definir Configurações de Aplicativo**, selecione **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon** e o **Identificador**. Em seguida, clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_06.png)

	a. Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Weekdone usando o seguinte padrão: **“https://weekdone.com/a/azure”**.

	b. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: **"https://weekdone.com/a/azure/metadata"**.

	c. Clique em **Próximo**.

5. Na página **Configurar logon único no Weekdone**, execute as seguintes etapas e clique em **Avançar**:

	![Configurar o logon único](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_05.png)

    a. Clique em **Baixar certificado** e salve o certificado no computador.

    b. Clique em **Próximo**.


5. Para ter o logon único configurado para seu aplicativo, entre em contato com a equipe de suporte do Weekdone via hello@weekdone.com. Anexe o arquivo de certificado baixado ao seu email e compartilhe as URLs de metadados (URL do emissor, URL SSO SAML e URL de serviço de logoff único) com a equipe de Weekdone para configurar o SSO no seu lado.

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
  
	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_04.png)

5. Na página da caixa de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_07.png)

8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-weekdone-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criando um usuário de teste Weekdone

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Weekdone. O Weekdone dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Weekdone, caso ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Weekdone pelo endereço hello@weekdone.com.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Weekdone.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Weekdone, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Weekdone**.

	![Configurar o logon único](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Weekdone no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Weekdone.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0427_2016-->