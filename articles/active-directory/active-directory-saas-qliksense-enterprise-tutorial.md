<properties
	pageTitle="Tutorial: Integração do Azure Active Directory com o Qlik Sense Enterprise | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Qlik Sense Enterprise."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Azure Active Directory ao Qlik Sense Enterprise

Neste tutorial, você aprenderá como integrar o Qlik Sense Enterprise ao Azure Active Directory (Azure AD).

Integrar o Qlik Sense Enterprise ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD que tenha acesso ao Qlik Sense Enterprise
- Você pode habilitar seus usuários a fazerem logon automaticamente no Qlik Sense Enterprise (logon único) com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Qlik Sense Enterprise, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Qlik Sense Enterprise habilitada para logon único


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Qlik Sense Enterprise da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionar Qlik Sense Enterprise da galeria
Para configurar a integração do Qlik Sense Enterprise com o Azure AD, você precisará adicionar o Qlik Sense Enterprise à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Qlik Sense Enterprise por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Qlik Sense Enterprise**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. No painel de resultados, selecione **Qlik Sense Enterprise** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Qlik Sense Enterprise, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Qlik Sense Enterprise é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Qlik Sense Enterprise.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Qlik Sense Enterprise.

Para configurar e testar o logon único do Azure AD com o Qlik Sense Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Qlik Sense Enterprise](#creating-a-qliksense-enterprise-test-user)**: para ter um equivalente de Brenda Fernandes no Qlik Sense Enterprise que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Qlik Sense Enterprise.


**Para configurar o logon único do Azure AD com Qlik Sense Enterprise, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Qlik Sense Enterprise**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
	 
	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Qlik Sense Enterprise**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png)

    a. Na caixa de texto **URL de logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Qlik Sense Enterprise usando o seguinte padrão: **https://\<Qlik Sense Fully Qualifed Hostname>:443/<Virtual Proxy Prefix>/samlauthn/**.
	
	> [AZURE.NOTE] Observe a barra à direita no fim desse URI. Ela é necessária.

	b. Clique em **Avançar**
 
4. Na página **Configurar o logon único no Qlik Sense Enterprise** execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador. Esteja preparado para editar o arquivo de metadados antes de carregar o servidor Qlik Sense.

    b. Clique em **Avançar**.

5. Prepare o arquivo XML de Metadados de Federação para que você possa carregar no servidor Qlik Sense.

	> [AZURE.NOTE] Antes de carregar os metadados IdP para o servidor Qlik Sense, o arquivo precisa ser editado para remover as informações e garantir a operação correta entre o Azure AD e o servidor Qlik Sense.

	![QlikSense][qs24]

	a. Abra o arquivo FederationMetaData.xml baixado do Azure em um editor de texto.

	b. Procure o valor **RoleDescriptor**. Haverá quatro entradas (dois pares de marcas de elemento de abertura e fechamento).

	c. Exclua todas as informações e as marcas RoleDescriptor do arquivo.

	d. Salve o arquivo e mantenha-o à mão para uso posterior neste documento.

6. Navegue para o QMC (Qlik Sense Qlik Management Console) como um usuário que pode criar configurações de proxy virtual.

7. No QMC, clique no item de menu Virtual Proxy.

	![QlikSense][qs6]

8. Na parte inferior da tela, clique no botão Criar novo.

	![QlikSense][qs7]

9. A tela de Edição de proxy virtual é exibida. No lado direito da tela, há um menu para tornar as opções de configuração visíveis.

	![QlikSense][qs9]

10. Com a opção de menu identificação marcada, insira as informações de identificação para a configuração de proxy virtual do Azure.

	![QlikSense][qs8]
	
	a. O campo de descrição é um nome amigável para a configuração do proxy virtual. Insira um valor para uma descrição.
	
	b. O campo identifica o ponto de extremidade do proxy virtual para se conectar ao Qlik Sense com o Logon Único do Azure AD. Insira um nome de prefixo exclusivo para esse proxy virtual.

	c. O tempo limite de inatividade de sessão (minutos) é o tempo limite para conexões por meio desse proxy virtual.

	d. O nome de cabeçalho de cookie de sessão é o nome do cookie que armazena o identificador de sessão para a sessão do Qlik Sense que um usuário recebe após uma autenticação bem-sucedida. Esse nome deve ser exclusivo.

11. Clique na opção de menu de autenticação para torná-la visível. Aparece a tela de autenticação.

	![QlikSense][qs10]

	a. O menu suspenso **Modo de acesso anônimo** determina se os usuários anônimos podem acessar o Qlik Sense por meio do proxy virtual. A opção padrão é Nenhum usuário anônimo.

	b. O menu suspenso **Método de autenticação** determina o esquema de autenticação que o proxy virtual usará. Selecione SAML na lista suspensa. Mais opções serão exibidas como resultado.

	c. No **campo URI de host SAML**, insira os usuários de nome de host para acessar o Qlik Sense por meio desse proxy virtual SAML. O nome do host é o uri do servidor Qlik Sense.

	d. Na **ID da entidade SAML**, insira o mesmo valor inserido para o campo URI de host SAML.

	e. **Metadados IdP SAML** é o arquivo editado anteriormente na seção **Editar metadados de federação de configuração do Azure AD**. **Antes de carregar os metadados IdP, o arquivo precisa ser editado** para remover as informações e garantir a operação correta entre o Azure AD e servidor Qlik Sense. **Veja as instruções acima se o arquivo ainda precisar ser editado.** Se o arquivo foi editado, clique no botão Procurar e selecione o arquivo de metadados editado para carregá-lo para a configuração do proxy virtual.

	f. Insira a referência de nome ou o esquema de atributo para o atributo SAML que representa o **UserID** que o Azure AD enviará ao servidor Qlik Sense. Informações de referência de esquema estão disponíveis na configuração de postagem de telas do aplicativo do Azure. Para usar o atributo de nome, **insira http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

	g. Insira o valor para o **diretório de usuário** que será anexado aos usuários quando eles se autenticarem no servidor Qlik Sense por meio do Azure AD. Valores embutidos em código devem estar entre **colchetes**. Para usar um atributo enviado na asserção SAML do Azure AD, digite o nome do atributo na caixa de texto **sem** colchetes.

	h. O **algoritmo de assinatura SAML** define o certificado de provedor (nesse caso, o servidor Qlik Sense) do serviço de assinatura para a configuração de proxy virtual. Se o servidor Qlik Sense usar um certificado confiável gerado usando o Microsoft Enhanced RSA e o Provedor de Criptografia AES, altere o algoritmo de assinatura de SAML para **SHA-256**.

	i. A seção de mapeamento de atributo SAML permite que atributos adicionais como grupos sejam enviados ao Qlik Sense para uso em regras de segurança.

12. Clique na opção de menu Balanceamento de Carga para torná-la visível. Aparece a tela Balanceamento de Carga.

	![QlikSense][qs11]

13. Clique no botão Adicionar novo nó de servidor, selecione o(s) nó(s) de mecanismo aos quais o Qlik Sense enviará sessões para fins de balanceamento de carga e clique no botão Adicionar.

	![QlikSense][qs12]

14. Clique na opção de menu Avançado para torná-la visível. Aparece a tela Avançado.

	![QlikSense][qs13]

	a. A lista de permissões de Host identifica os nomes de host que são aceitos durante a conexão com o servidor Qlik Sense. **Insira o nome do host que os usuários especificarão ao se conectar ao servidor Qlik Sense.** O nome do host é o mesmo valor que o uri de host SAML, sem https://.

15. Clique no botão Aplicar.

	![QlikSense][qs14]

16. Clique em OK para aceitar a mensagem de aviso informando que proxies vinculados ao proxy virtual serão reiniciados.

	![QlikSense][qs15]

17. No lado direito da tela, o menu Itens Associados é exibido. Clique na opção de menu Proxies.

	![QlikSense][qs16]

18. Aparece a tela de proxy. Clique no botão Vincular na parte inferior para vincular um proxy ao proxy virtual.

	![QlikSense][qs17]

19. Selecione o nó de proxy que dará suporte a essa conexão de proxy virtual e clique no botão Vincular. Depois da vinculação, o proxy será listado nos proxies associados.

	![QlikSense][qs18] ![QlikSense][qs19]

20. Após cerca de cinco a dez segundos, será exibida a mensagem Atualizar QMC. Clique no botão Atualizar QMC.

	![QlikSense][qs20]

21. Quando o QMC for atualizado, clique no item de menu Virtual proxies. A nova entrada de proxy virtual SAML é listada na tabela na tela. Clique na entrada de proxy virtual.

	![QlikSense][qs51]

22. Na parte inferior da tela, o botão Baixar metadados SP será ativado. Clique no botão Baixar metadados SP para salvar os metadados em um arquivo.

	![QlikSense][qs52]

23. Abra o arquivo de metadados sp. Observe a entrada **entityID** e a entrada **AssertionConsumerService**. Esses valores são equivalentes ao **Identificador** e à **URL de logon** na configuração do aplicativo do Azure AD. Se eles não forem correspondentes, você deverá substituí-los no assistente de configuração de aplicativo do Azure AD.

	![QlikSense][qs53]

24. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
	
	![Logon único do AD do Azure][10]

25. Na página **Confirmação de logon único**, clique em **Concluir**.
 
	![Logon único do AD do Azure][11]


### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página de diálogo **Perfil do Usuário**, execute as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.


### Criação de um usuário de teste do Qlik Sense Enterprise

Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no Qlik Sense Enterprise. Trabalhe com a equipe de suporte do Qlik Sense Enterprise para adicionar os usuários na plataforma Qlik Sense Enterprise.


### Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Qlik Sense Enterprise.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Qlik Sense Enterprise, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Qlik Sense Enterprise**.

	![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png)

3. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]


## Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Qlik Sense Enterprise no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Qlik Sense Enterprise.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

<!---HONumber=AcomDC_0907_2016-->