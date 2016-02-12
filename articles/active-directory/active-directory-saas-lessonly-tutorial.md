<properties
	pageTitle="Tutorial: integração do Active Directory do Azure ao Lesson.ly | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Lesson.ly."
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


# Tutorial: integração do Active Directory do Azure com o Lesson.ly

O objetivo deste tutorial é mostrar como integrar o Lesson.ly ao Active Directory do Azure (AD do Azure). <br>A integração do Lesson.ly ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao Lesson.ly
- Você pode permitir que seus usuários façam logon automaticamente em Lesson.ly (Logon único) com as contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do AD do Azure ao Lesson.ly, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Lesson.ly habilitada para logon único


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Lesson.ly da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Lesson.ly da galeria
Para configurar a integração do Lesson.ly ao AD do Azure, você precisa adicionar o Lesson.ly da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Lesson.ly da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Lesson.ly**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)<br>
7. No painel de resultados, selecione **Lesson.ly** e clique em **Concluir** para adicionar o aplicativo. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)<br>
##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Lesson.ly, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Lesson.ly é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do AD do Azure e o usuário relacionado do Lesson.ly.<br> Essa relação de vinculação é estabelecida por meio da atribuição do valor de **nome de usuário** no AD do Azure como o valor de **nome de usuário** no Lesson.ly.

Para configurar e testar o logon único do AD do Azure com o Lesson.ly, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do Lesson.ly](#creating-a-Lessonly-test-user)** - para ter um equivalente de Brenda Fernandes no Lesson.ly que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Bonus.ly com a respectiva conta do AD do Azure usando a federação baseada no protocolo SAML.

Seu aplicativo Lesson.ly espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML**. A captura de tela a seguir mostra um exemplo disso. <br><br> ![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) <br>

**Para configurar o logon único do AD do Azure com o Lesson.ly, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo Lesson.ly, no menu superior, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**. <br><br> ![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) <br>

2. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas: <br><br> ![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) <br> a. Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.<br> b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo mostrado para a linha.<br> c. Na lista **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.<br> d. Clique em **Concluído**

3. Clique em **Aplicar alterações**.

4. Em seu navegador, clique em **Voltar** para abrir o diálogo Início Rápido novamente

5. Clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

6. Na página **Como você deseja que os usuários façam logon no Lesson.ly**, selecione **Logon Único do AD do Azure** e clique em **Avançar**. <br><br> ![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) <br>

7. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) <br>


    a. Na caixa de texto da URL de Entrada, digite a URL usada pelos usuários para fazer logon no seu aplicativo do Lesson.ly usando o seguinte padrão: **“https://nomedaempresa.Lesson.ly/signin”**. Ao referenciar um nome genérico, **nomedaempresa** precisará ser substituído por um nome real.


8. Na página **Configurar logon único no Lesson.ly**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


9. Para configurar o SSO para seu aplicativo, entre em contato com sua equipe de suporte do Lesson.ly via dev@lesson.ly. Anexe o arquivo de certificado baixado para seu email e compartilhe as URLs de metadados (ID da Entidade, URL de Entrada de Logon Único e URL de Saída) com a equipe do Lesson.ly para configurar o logon único em seu lado.


10. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

11. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.<br> Na lista Usuários, escolha **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação esquerdo, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) <br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) <br>

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) <br>

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) <br>

8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) <br>

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criar um usuário de teste Lesson.ly

O objetivo desta seção é criar um usuário chamado Brenda Fernandes em Lesson.ly. O Lesson.ly dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Lesson.ly, caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Lesson.ly.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Lesson.ly. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Lesson.ly, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione ** Lesson.ly.**. <br><br>![Configurar o logon único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.<br> Quando você clicar no bloco Lesson.ly no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Lesson.ly.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0204_2016-->