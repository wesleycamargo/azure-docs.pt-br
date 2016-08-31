<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Samanage | Microsoft Azure" 
    description="Saiba como usar o Samanage com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="08/15/2016" 
    ms.author="jeedes" />

# Tutorial: Integração do Azure Active Directory ao Samanage
  
O objetivo desse tutorial é mostrar como integrar o Samanage ao Azure AD (Azure Active Directory).

A integração do Samanage ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Samanage
- Você pode permitir que seus usuários façam logon automaticamente no Samanage (logon único) com as contas do Azure AD deles
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Samanage, você precisará dos seguintes itens:

- Uma assinatura válida do Azure
- Um locatário no Samanage


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Samanage da galeria
2. Configurar e testar o logon único do AD do Azure

## Adição do Samanage da galeria
Para configurar a integração do Samanage ao Azure AD, você precisa adicionar o Samanage na galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Samanage da galeria, execute as seguintes etapas:**

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Samanage**.

    ![Galeria de aplicativos](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Samanage** e clique em **Concluir** para adicionar o aplicativo.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Samanage, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Samanage é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Samanage.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Samanage.

Para configurar e testar o logon único do Azure AD com o Samanage, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Samanage](#creating-a-Samanage-test-user)** - para ter um equivalente de Brenda Fernandes no Samanage que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD
  
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo do Samanage.

**Para configurar o logon único do Azure AD com o Samanage, execute as seguintes etapas:**

1.  No portal clássico do Azure, na página de integração de aplicativos do **Samanage**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no Samanage**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Logon Único do AD do Microsoft Azure")

3.  Na página de diálogo Definir Configurações de Aplicativo, execute as seguintes etapas:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configurar a URL do Aplicativo")

    a. Na caixa de texto **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.
	
	b. Clique em **Avançar**

	> [AZURE.NOTE] Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada real. Para obter esses valores, consulte etapa 8.c para obter mais detalhes ou entre em contato com o Samanage.

4.  Na página **Configurar logon único no Samanage**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Samanage como administrador.

6.  Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.

    ![Painel](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Painel")

7.  Clique em **Logon Único**.

    ![Logon Único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Logon Único")

8.  Navegue até a seção **Logon usando SAML**, execute as seguintes etapas:
    
    ![Fazer logon usando SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Fazer logon usando SAML")

    a. Clique em **Habilitar o Logon Único com SAML**.

    b. Na caixa de texto **URL do Provedor de Identidade**, insira o valor de **ID do Provedor de Identidade** do assistente de configuração de aplicativo do Azure AD.

    c. Confirme o **URL de Logon** corresponde a **URL de Entrada** na etapa 3.

	d. Na caixa de texto **URL de Logoff do Provedor de Identidade**, insira o valor de **URL de Logoff Remoto** do assistente de configuração de aplicativo do Azure AD.

    e. No tipo de caixa de texto **Emissor SAML**, digite o URI da id do aplicativo definido no seu provedor de identidade.

	f. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Cole seu Certificado x.509 do Provedor de Identidade abaixo**.
    
	g. Clique em **Criar usuários se eles não existirem no Samanage**.
    
	h. Clique em **Atualizar**.

9.  No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configurar o logon único")

10. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configurar o logon único")


### Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal Clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
    
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
    
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
    
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
    
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

### Criação de um usuário de teste de Samanage
  
Para permitir que os usuários do Azure AD façam logon no Samanage, eles deverão ser provisionados no Samanage. No caso do Samanage, o provisionamento é uma tarefa manual.

####Para provisionar uma conta de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa Samanage como um administrador.

2.  Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.

    ![Configuração](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Configuração")

3.  Clique na guia **Usuários**

    ![Usuários](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Usuários")

4.  Clique em **Novo Usuário**.

    ![Novo Usuário](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "Novo Usuário")

5.  Digite o **Nome** e o **Endereço de Email** de uma conta do Azure AD que você deseja provisionar e clique em **Criar usuário**.

    ![Criar Usuário](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Criar Usuário")

	>[AZURE.NOTE]O titular da conta do AAD receberá um email e um link para confirmar sua conta antes de se tornar ativo. É possível usar qualquer outra ferramenta de criação da conta de usuário do Samanage ou as APIs fornecidas pelo Samanage para provisionar as contas de usuário do AAD.


###Atribuição do usuário de teste do AD do Azure
  
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Samanage.
	
![Atribuir usuários](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Atribuir usuários")

**Para atribuir Brenda Fernandes ao Samanage, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
    
	![Atribuir usuários](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Atribuir usuários")

2. Na lista de aplicativos, selecione **Samanage**.
    
	![Configurar o logon único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)

3. No menu na parte superior, clique em **Usuários**.
    
	![Atribuir usuários](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Atribuir usuários")

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
    
	![Atribuir usuários](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Atribuir usuários")


### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.
 
Ao clicar no bloco do Samanage no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Samanage.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0817_2016-->