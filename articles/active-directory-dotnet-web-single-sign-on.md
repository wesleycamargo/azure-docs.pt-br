<properties urlDisplayName="" pageTitle="Logon único da Web com o .NET e o Active Directory do Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Web Single Sign-On with .NET and Azure Active Directory" authors="" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="mbaldwin" />






# Logon único da Web com o .NET e o Active Directory do Azure

<h2><a name="introduction"></a>Introdução</h2>

Este tutorial mostra como utilizar o Active Directory do Azure para habilitar o logon único para usuários clientes do Office 365. Você saberá como:

* Provisionar o aplicativo Web em um locatário do cliente
* Proteger o aplicativo usando Web Services Federation

<h3>Pré-requisitos</h3>
Os seguintes pré-requisitos de ambiente de desenvolvimento são necessários para esta explicação passo a passo:

* Visual Studio 2010 SP1
* Microsoft .NET Framework 4.0
* [ASP.NET MVC 3]
* [Tempo de execução do Windows Identity Foundation 1.0]
* [SDK do Windows Identity Foundation 3.5]
* Serviços de Informações da Internet (IIS) 7.5 com SSL habilitado
* Windows PowerShell
* [Commandlets do Office 365 PowerShell]

<h3>Sumário</h3>
* [Introdução][]
* [Etapa 1: Criar um aplicativo ASP.NET MVC][]
* [Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa][]
* [Etapa 3: Proteger o aplicativo usando Web Services Federation para a entrada de funcionários][]
* [Resumo][]

<h2><a name="createapp"></a>Etapa 1: Criar um aplicativo ASP.NET MVC</h2>
Esta etapa descreve como criar um aplicativo ASP.NET MVC 3 simples que representará um recurso protegido. O acesso a esse recurso será concedido por meio de autenticação federada gerenciada pelo STS da empresa, que é descrito mais adiante no tutorial.

1. Abra o Visual Studio como administrador.
2. No menu **Arquivo**, clique em **Novo Projeto**. 
3. No menu Modelo à esquerda, selecione **Web** e clique em **Aplicativo Web ASP.NET MVC 3**.Nomeie o projeto *OrgIdFederationSample*.
4.  Na caixa de diálogo **Novo Projeto ASP.NET MVC 3**, selecione o modelo **Vazio** e clique em **OK**.
5. Após o Visual Studio gerar o novo projeto, clique com o botão direito do mouse na pasta **Controladores** no **Gerenciador de Soluções**, clique em **Adicionar** e, em seguida, em **Controlador**.
6. Na caixa de diálogo **Adicionar Controlador**, nomeie o novo controlador *HomeController.cs* e clique em **Adicionar**.
7. O arquivo **HomeController.cs** será criado e aberto. No arquivo de código, clique com o botão direito do mouse no método ***Index()*** e clique em **Adicionar Modo de Exibição...**. 
8. Na caixa de diálogo **Adicionar Modo de Exibição**, clique em **OK**. Um arquivo **Index.cshtml** será criado em uma nova pasta chamada **Home**. 
9. Clique com o botão direito do mouse em seu projeto **OrgIdFederationSample** no **Gerenciador de Soluções** e clique em **Propriedades**.
10. Na janela Propriedades, clique em **Web** no menu à esquerda e selecione **Usar servidor Web do IIS Local**. Pode surgir uma caixa de diálogo solicitando que você crie um diretório virtual para o projeto. Clique em **Sim** na caixa de diálogo.
11. Compile e execute o aplicativo. A página de Índice de seu controlador Home será exibida.

<h2><a name="provisionapp"></a>Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa</h2>
Esta etapa descreve como um administrador de um cliente do Active Directory do Azure provisiona o aplicativo MVC em seu locatário e configura o logon único. Após essa etapa ser realizada, os funcionários da empresa podem autenticar-se no aplicativo Web usando suas contas do Office 365.

O processo de provisionamento começa com a criação de uma nova Entidade de Serviço para o aplicativo. As Entidades de Serviço são usadas pelo Active Directory do Azure para registrar e autenticar aplicativos no diretório.

1. Se ainda não tiver feito isso, baixe e instale os Commandlets do Office 365 PowerShell.
2. No menu **Iniciar**, execute o console do **Módulo do Microsoft Online Services para Windows PowerShell**.Esse console fornece um ambiente de linha de comando para configurar atributos sobre locatários do Office 365, como criar e modificar Entidades de Serviço.
3. Para importar o módulo **MSOnlineExtended** necessário, digite o seguinte comando e pressione Enter:

		Import-Module MSOnlineExtended -Force
4. Para conectar-se a seu diretório do Office 365, você precisará fornecer as credenciais de administrador da empresa. Digite o seguinte comando e pressione Enter e, em seguida, digite suas credenciais no prompt:

		Connect-MsolService
5. Agora você criará uma nova Entidade de Serviço para o aplicativo. Digite o comando a seguir e pressione Enter:

		New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
Essa etapa retornará informações semelhantes às seguintes:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample Website
		ServicePrincipalNames : {OrgIdFederationSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
	<div class="dev-callout"><strong>Observação</strong><p>Você deve salvar essa saída, principalmente a chave simétrica gerada. Essa chave só é revelada a você durante a criação da Entidade de Serviço, e você não poderá recuperá-la no futuro. Os outros valores são necessários para usar a Graph API para ler e gravar informações no diretório.</p></div>

6. A etapa final define a URL de resposta para o seu aplicativo. A URL de resposta é aonde as respostas são enviadas após tentativas de autenticação. Digite os comandos a seguir e pressione Enter:

		$replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

		Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 
	
O aplicativo Web agora foi provisionado no diretório e pode ser usado para o logon único da Web pelos funcionários da empresa.

<h2><a name="protectapp"></a>Etapa 3: Proteger o aplicativo usando Web Services Federation para a entrada de funcionários</h2>
Esta etapa mostra como adicionar suporte para logon federado usando Windows Identity Foundation (WIF). Você também adicionará uma página de logon e configurará a relação de confiança entre o aplicativo e o locatário do diretório.

1. No Visual Studio, clique com o botão direito do mouse no projeto **OrgIdFederationSample** e selecione **"Adicionar referência STS..."**.Essa opção do menu de contexto foi adicionada quando você instalou o SDK do WIF.
2. Na caixa de diálogo **Utilitário de Federação** em **URI do Aplicativo**, você precisa fornecer o URI no formato a seguir:

		spn:<Your AppPrincipalId>@<Your Directory Domain>

	**spn** especifica que o URI é um nome de Entidade de Serviço, **Seu AppPrincpalId** é o valor de GUID do **AppPrincipalId** gerado quando você criou uma Entidade de Serviço e **Seu Domínio de Diretório** é o domínio *.onmicrosoft.com do locatário do diretório.Para este aplicativo de exemplo, um valor do URI do aplicativo completo é especificado, conforme mostrado abaixo:

		spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

	Depois que tiver inserido o URI do aplicativo, clique em **Avançar**.

3. Uma caixa de diálogo de aviso será exibida informando que "O aplicativo não está hospedado em uma conexão https segura". Isso é causado porque o Utilitário de Federação não reconhece o formato **spn:**, mas o aplicativo ainda usará uma conexão https segura de qualquer maneira. Clique em **Sim** para continuar.

4. Na página seguinte do Utilitário de Federação, selecione **Usar um STS existente** e, em seguida, em **Local do documento de metadados do WS-Federation do STS**, digite a URL para o documento de metadados do Web Services Federation.Essa URL é especificada no seguinte formato:

		https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

	Para este aplicativo, o local dos metadados do Web Services Federation é especificado conforme mostrado abaixo:

		https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

	Após ter inserido o local dos metadados, clique em **Avançar**.

5. Na página seguinte do Utilitário de Federação, selecione **Desabilitar validação de cadeia de certificados** e clique em **Avançar**.

6. Na página seguinte do Utilitário de Federação, selecione **Sem criptografia** e clique em **Avançar**.

7. A próxima página do Utilitário de Federação exibe as declarações fornecidas pelo STS. Examine as declarações e clique em **Avançar**.

8. Em seguida, você configurará os Serviços de Informações da Internet (IIS) para dar suporte a SSL para seu ambiente de desenvolvimento. Para abrir o Gerenciador do IIS, você pode digitar *inetmgr* no prompt **Executar**. 

9. No Gerenciador do IIS, expanda a pasta **Sites** no menu à esquerda e, em seguida, clique na **Página Inicial do Site Padrão**. No menu **Ações** à direita, clique em **Associações...**.

10. Na caixa de diálogo **Associações do Site**, clique em **Adicionar**. Na caixa de diálogo **Adicionar Associação do Site**, altere o menu suspenso **Tipo** para ***https*** e, em seguida, em **Certificado SSL**, selecione **Certificado de Desenvolvimento do IIS Express**. Clique em **OK**.

11. No Gerenciador do IIS, clique em **Pools de Aplicativos** no menu à esquerda, selecione a entrada **ASP.NET v4.0** na lista e clique em **Configurações Avançadas** no menu **Ações** à direita.

12. Na caixa de diálogo **Configurações Avançadas**, defina a propriedade **Carregar Perfil do Usuário** como **true**. Clique em **OK**.

13. No Visual Studio, abra o arquivo **Web. config** no **Gerenciador de Soluções** na raiz do seu projeto. 

14. No arquivo **Web.config**, localize a seção **wsFederation** e adicione um atributo **reply** com o mesmo valor que a variável **$replyUrl** especificada ao criar a Entidade de Serviço.Por exemplo:

		<wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Adicione um nó **httpRuntime** na seção **system.web** com o atributo **requestValidationMode** definido como **2.0**.Por exemplo:

		<system.web>
			<httpRuntime requestValidationMode="2.0" /> 
			...

	Salve o arquivo **Web.config**.

16. Agora que você habilitou a autenticação para o aplicativo Web, a página **Índice** deve ser modificada para apresentar as declarações de um usuário autenticado. Abra o arquivo **Index.cshtml** localizado na pasta **Home** da pasta **Exibições**.

17. Adicione o seguinte trecho de código ao arquivo **Index.cshtml** e salve-o:

		<p>
			@if (User.Identity.IsAuthenticated)
			{
			<ul>
				@foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
				{
					<li>@claim</li>
				}
			</ul>
			}
		</p> 

18. Depois de salvar as alterações no arquivo **Index.cshtml**, pressione **F5** para executar o aplicativo. Você será redirecionado para a página do Provedor de Identidade do Office 365, onde pode fazer logon usando suas credenciais de locatário do diretório.Por exemplo, *john.doe@awesomecomputers.onmicrosoft.com*.

19. Depois de entrar usando suas credenciais, você será redirecionado para a página de Índice do seu controlador Inicial, onde as declarações de sua conta serão exibidas. Isso demonstra uma autenticação bem-sucedida do usuário no aplicativo usando o logon único fornecido pelo Active Directory do Azure.

<h2><a name="summary"></a>Resumo</h2>
Este tutorial mostrou como criar e configurar um aplicativo de locatário único que usa os recursos de logon único do Active Directory do Azure. Também é possível criar aplicativos multilocatários para o Active Directory do Azure lendo o seguinte manual: [Desenvolvendo aplicativos multilocatários em nuvem com o Active Directory do Azure].

[Introdução]: #introduction
[Etapa 1: Criar um aplicativo ASP.NET MVC]: #createapp
[Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa]: #provisionapp
[Etapa 3: Proteger o aplicativo usando Web Services Federation para a entrada de funcionários]: #protectapp
[Resumo]: #summary
[Desenvolvendo aplicativos multilocatários em nuvem com o Active Directory do Azure]: http://g.microsoftonline.com/0AX00en/121
[SDK do Windows Identity Foundation 3.5]: http://www.microsoft.com/pt-br/download/details.aspx?id=4451
[Tempo de execução do Windows Identity Foundation 1.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=17331
[Commandlets do Office 365 PowerShell]: http://onlinehelp.microsoft.com/pt-br/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/pt-br/download/details.aspx?id=4211


<!--HONumber=35_1-->
