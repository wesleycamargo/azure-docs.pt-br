<properties 
	pageTitle="Logon Único com o Active Directory do Azure (PHP)" 
	description="Saiba como criar um aplicativo Web PHP que usa logon único com o Active Directory do Azure." 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="tomfitz"/>

# Logon único da web com o PHP e o Active Directory do Azure

##<a name="introduction"></a>Introdução

Este tutorial mostra aos desenvolvedores de PHP como utilizar o Active Directory do Azure para habilitar o logon único para usuários de clientes do Office 365. Você saberá como:

* Provisionar o aplicativo web em um locatário do cliente
* Proteger o aplicativo usando o WS-Federation

###Pré-requisitos
Os seguintes pré-requisitos de ambiente de desenvolvimento são necessários para esta explicação passo a passo:

* [Código de exemplo de PHP para o Active Directory do Azure]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 (via Web Platform Installer)
* Serviços de Informações da Internet (IIS) 7.5 com SSL habilitado
* Windows PowerShell
* [Cmdlets do PowerShell do Office 365]

## Etapa 1: Criar um aplicativo PHP
Esta etapa descreve como criar um aplicativo PHP simples que representará um recurso protegido. O acesso a esse recurso será concedido por meio de autenticação federada gerenciada pelo STS da empresa, que é descrito mais adiante no tutorial.

1. Abra uma nova instância do Eclipse.
2. No menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Novo Projeto PHP...**. 
3. Na caixa de diálogo **Novo Projeto PHP**, nomeie o projeto *phpSample* e, em seguida, clique em **Concluir**.
4. No menu **Gerenciador de PHP** à esquerda, clique com o botão direito em *phpProject*, clique em **Novo** e, em seguida, clique em **Arquivo PHP**.
5. Na caixa de diálogo **Novo Arquivo PHP**, nomeie o arquivo **index.php** e, em seguida, clique em **Concluir**.
6. Substitua a marcação gerada pela seguinte e salve o **index.php**:

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. Abra o **Gerenciador dos Serviços de Informações da Internet (IIS)** digitando *inetmgr* no prompt Executar e pressione Enter.

8. No Gerenciador do IIS, expanda a pasta **Sites** no menu à esquerda, clique com o botão direito em **Site Padrão** e clique em **Adicionar Aplicativo...**.

9. Na caixa de diálogo **Adicionar Aplicativo**, defina o valor **Alias** como *phpSample* e o **Caminho físico** para o caminho do arquivo onde você criou o projeto PHP.

10. No Eclipse, no menu **Executar**, clique em **Executar**.

11. No menu **Executar Aplicativo Web PHP**, clique em **OK**.

12. A página **index.php** será aberta em uma nova guia. A página deve simplesmente exibir o texto: *Página de Índice*.

## Etapa 2: Provisionar o aplicativo em um inquilino do diretório da empresa
Esta etapa descreve como um administrador de um cliente do Azure Active Directory provisiona o aplicativo PHP em seu locatário e configura o logon único. Após essa etapa ser realizada, os funcionários da empresa podem autenticar-se no aplicativo web usando suas contas do Office 365.

O processo de provisionamento começa com a criação de uma nova Entidade de Serviço para o aplicativo. As Entidades de Serviço são usadas pelo Active Directory do Azure para registrar e autenticar aplicativos no diretório.

1. Se ainda não tiver feito isso, baixe e instale os Commandlets do Office 365 PowerShell.
2. No menu **Iniciar**, execute o console **Módulo do Active Directory do Azure para Windows PowerShell**. Esse console fornece um ambiente de linha de comando para configurar atributos sobre locatários do Office 365, como criar e modificar Entidades de Serviço.
3. Para importar o módulo **MSOnlineExtended** requerido, digite o seguinte comando e pressione Enter:

		Import-Module MSOnlineExtended -Force
4. Para conectar-se a seu diretório do Office 365, você precisa fornecer as credenciais de administrador da empresa. Digite o seguinte comando e pressione Enter e, em seguida, digite suas credenciais no prompt:

		Connect-MsolService
5. Agora você criará uma nova Entidade de Serviço para o aplicativo. Digite o comando a seguir e pressione Enter:

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
Essa etapa retornará informações semelhantes às seguintes:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]Você deve salvar essa saída, principalmente a chave simétrica gerada. Essa chave só é revelada a você durante a criação da Entidade de Serviço, e você não pode recuperá-la no futuro. Os outros valores são necessários para usar a Graph API para ler e gravar informações no diretório.

6. A etapa final define a URL de resposta para o seu aplicativo. A URL de resposta é onde as respostas são enviadas após tentativas de autenticação. Digite os comandos a seguir e pressione enter:

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
O aplicativo Web agora foi provisionado no diretório e pode ser usado para o logon único da Web pelos funcionários da empresa.

## Etapa 3: Proteger o aplicativo usando o Web Services Federation para entrada de funcionários
Esta etapa mostra como adicionar suporte para logon federado usando o Windows Identity Foundation (WIF) e as bibliotecas simpleSAML.php baixadas com o código de exemplo nos pré-requisitos. Você também adicionará uma página de logon e configurará a relação de confiança entre o aplicativo e o locatário do diretório.

1. No Eclipse, clique com o botão direito no projeto **phpSample**, clique em **Novo** e, em seguida, clique em **Arquivo**. 

2. Na caixa de diálogo **Novo Arquivo PHP**, nomeie o arquivo **federation.ini** e, em seguida, clique em **Concluir**.

3. No novo arquivo **federation.ini**, insira as informações a seguir fornecendo os valores com as informações que você salvou na Etapa 2 ao criar sua Entidade de Serviço:

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


	> [AZURE.NOTE]Os valores **audienceuris** e **realm** devem ser precedidos por "spn:".

4. No Eclipse, clique com o botão direito no projeto **phpSample**, clique em **Novo** e, em seguida, clique em **Arquivo PHP**.

5. Na caixa de diálogo **Novo Arquivo PHP**, nomeie o arquivo **secureResource.php** e, em seguida, clique em **Concluir**.

6. No novo arquivo **secureResource.php**, digite o seguinte código, substituindo o caminho **c:\\phpLibraries** pelo local da raiz onde você baixou o código de exemplo. O local da raiz deve incluir o arquivo **simpleSAML.php** e a pasta **federation**:

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. Abra a página **index.php** e atualize seu conteúdo para proteger a página e a salve:

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. No menu **Executar**, clique em **Executar**. Você deverá ser redirecionado automaticamente para a página do Provedor de Identidade do Office 365, onde você pode fazer logon usando suas credenciais de locatário do diretório. Por exemplo, **john.doe@fabrikam.onmicrosoft.com*.

## Resumo
Este tutorial mostrou como criar e configurar um aplicativo PHP de locatário único que usa os recursos de logon único do Azure Active Directory.

Um exemplo que mostra como usar o Active Directory do Azure e o logon único para PHP está disponível em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.

## Próximas etapas

Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).

[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Cmdlets do PowerShell do Office 365]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[Código de exemplo de PHP para o Active Directory do Azure]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!---HONumber=Oct15_HO3-->