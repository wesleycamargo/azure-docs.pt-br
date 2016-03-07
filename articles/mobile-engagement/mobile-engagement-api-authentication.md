<properties 
	pageTitle="Introdução às APIs de autenticação do Azure Mobile Engagement"
	description="Este é um guia de migração para ajudar os clientes que usavam autenticação com APIs do Capptain e agora precisam autenticar usando as novas APIs do Azure Mobile Enagagement." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/17/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement - usando as APIs para autenticação

## Visão geral

Este documento descreve detalhadamente como obter o mecanismo de autenticação trabalhando para as novas APIs.

Supõe-se que você tenha uma assinatura válida do Azure e criou um aplicativo do Mobile Engagement usando um dos nossos [Tutoriais de Desenvolvedor](mobile-engagement-windows-store-dotnet-get-started.md).

## Autenticação

Um Microsoft Azure Active Directory baseado no token OAuth deve ser usado para autenticação.

Para autenticar a solicitação de uma API, um cabeçalho de autorização deverá ser adicionado a cada solicitação. Ele deve ser da seguinte forma:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Tokens de Azure Active Directory expiram em 1 hora.

Há várias maneiras de obter um token. Como as APIs são geralmente chamadas de um serviço de nuvem, você provavelmente desejará usar uma chave de API. Uma chave de API na terminologia do Azure é chamada de senha de uma entidade de serviço. O procedimento a seguir descreve uma maneira de configurá-lo manualmente.

> [AZURE.WARNING] A chave exibida no Azure Active Directory NÃO é a chave de API do Mobile Engagement mostrada no Portal. O conceito de chave de API do Mobile Engagement foi preterido e substituído pela autenticação do AAD descrita neste documento.

#### Configuração única (manual)

Ao fazer isso, observe as seguintes informações pois você precisará delas mais tarde:
	
1. Criar um aplicativo do Azure Active Directory usando [este guia](../resource-group-create-service-principal-portal.md).

	- O nome do aplicativo que você escolheu conhecido como `{AD_APP_NAME}` neste documento.
	- O identificador do cliente exibido em Configurar menu, conhecido como `{CLIENT_ID}` neste documento.
	- A chave exibida apenas uma vez após o salvamento, conhecida como `{CLIENT_SECRET}` neste documento.
	- Clique no botão **EXIBIR PONTOS DE EXTREMIDADE** na barra inferior, copie a **URL DO PONTO DE EXTREMIDADE DE TOKEN OAUTH 2.0**, chamado de `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` neste documento. <br/>                                    
2. Atribuir uma função à entidade de serviço como Leitor ou Proprietário usando [CLI do Azure](../xplat-cli-install.md).

	Se você estiver no Windows, modifique sua variável de ambiente `PATH` para incluir `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` para poder usar os comandos do Azure.

	Execute os comandos a seguir para configurar a conta com a interface de linha de comando (CLI) do Azure:

		azure config mode arm 
		azure login

	Em seguida, use este comando para localizar a ID de objeto de seu aplicativo.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Observer o `Object Id` na saída.

	Em seguida, atribua a função `Owner` à entidade de serviço usando este comando:
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Configuração única (usando script)

Isso é uma maneira alternativa para realizar as etapas mencionadas anteriormente usando um script do PowerShell.

1. Obtenha a versão mais recente do Azure PowerShell.

	Atualmente na versão 1.2.1 disponível para download [aqui](https://github.com/Azure/azure-powershell/releases/tag/v1.2.1-February2016).

2. Abra o Windows PowerShell no modo de administrador e certifique-se de que você tenha instalado o [cmdlets do Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx).

		Install-Module AzureRM
		Install-AzureRM

3. Execute o comando a seguir:

		Import-Module AzureRM.profile

4. Execute o seguinte comando para iniciar um diálogo de logon. Depois de entrar, o comando exibirá a assinatura "ativa", o que significa que será afetado por qualquer comando executado.

		Add-AzureRmAccount

5. Execute o seguinte comando para listar todas as suas assinaturas. Copie o guid de um que deseja usar.

		Get-AzureRmSubscription

6. Execute o seguinte comando fornecendo o guid de assinatura para configurar a assinatura a ser usada. Isso é especialmente útil quando você tiver várias assinaturas.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Copie o texto para o script [AzureRmServicePrincipalOwner.ps1 novo](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) em seu computador local e execute-o.

	>[Azure.Note] A política de segurança padrão pode impedir a execução de scripts do PowerShell. Nesse caso, configure temporariamente a política de execução para permitir a execução de script usando o seguinte comando:

	>	Set-ExecutionPolicy RemoteSigned

	O script solicitará um "nome" para atribuir à sua ServicePrincipal. Você pode fornecer qualquer nome aqui.

	Depois que o script for concluído, ele exibirá quatro valores que precisam ser autenticados por meio de programação com o AD: **TenantId**, **SubscriptionId**, **ApplicationId** e **Secret**.

	Copie esses valores para referência. Para obter um token de acesso agora, você usará o TenantId como `{TENANT_ID}`, o ApplicationId como `{CLIENT_ID}` e o Secret como `{CLIENT_SECRET}`.

8. Verifique o Portal de Gerenciamento que um novo aplicativo do AD estava em **Mostrar aplicativos que a minha empresa possui**.

#### Etapas para obter um token válido

Para obter um novo token, chame essa API:

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

Segue um exemplo de solicitação:

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Aqui está um exemplo de resposta:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

Este exemplo incluiu os parâmetros de POST da codificação de URL, o valor `resource` é realmente `https://management.core.windows.net/`. Tenha cuidado para também codificar a URL `{CLIENT_SECRET}`, pois ela pode conter caracteres especiais.

Agora, em todas as chamadas à API, inclua o cabeçalho de solicitação de autorização:

	Authorization: Bearer {ACCESS_TOKEN}

Se você receber um código de status 401 retornado, verifique o corpo da resposta, ele pode informar se o token tiver expirado. Nesse caso, obtenha um novo token.

##Usando as APIs

Agora que você tem um token válido, você está pronto para fazer as chamadas à API.

1. Em cada solicitação de API, você precisará passar um token válido e não expirado que você obteve na seção anterior.

2. Você precisará do plug-in em alguns parâmetros na solicitação URI que identifica seu aplicativo. A URI de solicitação é semelhante ao seguinte

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Para obter os parâmetros, clique no nome do aplicativo e clique em Painel e você verá uma página semelhante à seguinte com todos os 3 parâmetros.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/> 1. Ignore o endereço raiz de API como era para as APIs anteriores.<br/> 2. Você precisa usar o nome de Recurso de Aplicativo que é diferente do nome do Aplicativo em si.

<!---HONumber=AcomDC_0224_2016-->