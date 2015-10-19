<properties 
	pageTitle="Chamar uma API personalizada em Aplicativos Lógicos" 
	description="Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags ms.service="app-service-logic" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na"
	
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="stepsic"/>
	
# Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos

Embora os Aplicativos Lógicos tenham um conjunto avançado de mais de 40 conectores para uma gama de serviços, convém chamar a sua própria API personalizada que pode executar seu próprio código. Uma das maneiras mais fáceis e mais escalonáveis de hospedar sua própria API Web personalizada é usar o Serviço de Aplicativo. Este artigo aborda como chamar a API Web hospedada em um aplicativo Web de Serviço de Aplicativo.

## Implante seu aplicativo Web.

Primeiro, você precisa implantar sua API como um aplicativo Web no Serviço de Aplicativo. Estas instruções abordam a implantação básica: [Criar um aplicativo Web ASP.NET](web-sites-dotnet-get-started.md).

Obtenha a **URL** do seu aplicativo Web; ela aparece em **Essentials** na parte superior do aplicativo Web.

## Chamando a API

Comece criando um novo Aplicativo Lógico em branco. Uma vez criado o Aplicativo Lógico em branco, clique em **Editar** ou em **Gatilhos e ações** e selecione **Criar do Início**.

Primeiro, é preferível usar um disparador de recorrência ou clicar em **Executar essa lógica manualmente**. Em seguida, convém fazer a chamada efetivamente para a API. Para isso, clique na ação verde **HTTP** no lado direito.

1. Escolha o **Método**: ele será definido no código da API
2. Na seção **URL**, cole na **URL** do seu aplicativo Web implantado
3. Se precisar de **Cabeçalhos**, inclua-os no formato JSON da seguinte forma: `{"Content-type" : "application/json", "Accept" : "application/json" }`
4. Se sua API é pública, você pode deixar **Autenticação** em branco. Se você deseja proteger as chamadas à API, consulte as seções a seguir.
5. Finalmente, inclua o **Corpo** da pergunta que você definiu na sua API.

Clique em **Salvar** na barra de comandos. Se você clicar em **Executar agora**, deve ver a chamada para a API e a resposta na lista de execução.

Isso funciona muito bem se você tem uma API pública, mas se deseja proteger sua API, existem duas maneiras diferentes de fazer isso:

1. *Nenhuma alteração de código necessária*: o Active Directory do Azure pode ser usado para proteger sua API sem exigir alterações ou reimplantação de código.
2. Impor as autenticações básica, AAD ou de certificado no código de sua API. 

## Protegendo chamadas à API sem alteração de código 

Nesta seção, você vai criar dois aplicativos do Active Directory do Azure, um para o seu Aplicativo Lógico e outro para seu aplicativo Web. Você vai autenticar chamadas para seu aplicativo Web usando a entidade de serviço (id e segredo do cliente) associada ao aplicativo AAD para seu Aplicativo Lógico. Finalmente, você irá incluir os IDs de aplicativo em sua definição do Aplicativo Lógico.

### Parte 1: configurando uma identidade de aplicativo para seu Aplicativo Lógico

Isso é o que o Aplicativo Lógico usará para autenticar no Active Directory. Você *precisa* fazer isso somente uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus Aplicativos Lógicos, embora também possa, se quiser, criar identidades exclusivas para cada Aplicativo Lógico. Você pode fazer isso na interface do usuário ou usar o PowerShell.

#### Criar a identidade do aplicativo usando o portal do Azure

1. Navegue até o [Active Directory no portal do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione o diretório que você usa para seu aplicativo Web
2. Clique na guia **Aplicativos**.
3. Na parte inferior da página na barra de comandos, clique em **Adicionar**
4. Dê um nome à sua identidade e clique na seta para avançar
5. Coloque uma cadeia de caracteres exclusiva formatada como um domínio nas duas caixas de texto e clique na marca de seleção
6. Clique na guia **Configurar** para esse aplicativo
7. Copie a **ID de cliente**; ela será usada em seu Aplicativo Lógico
8. Na seção **Chaves**, clique em **Selecionar duração** e escolha 1 ou 2 anos
9. Clique no botão **Salvar** na parte inferior da tela (talvez seja necessário aguardar alguns segundos)
10. Não deixe de copiar a chave na caixa. Isso também será usado em seu Aplicativo Lógico

#### Criar a identidade do aplicativo usando o PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Não deixe de copiar a **ID do locatário**, a **ID do aplicativo** e a senha usada

### Parte 2: proteger seu aplicativo Web com uma identidade de aplicativo AAD

Se seu aplicativo Web já está implantado, você pode habilitá-lo no portal. Caso contrário, você pode tornar a habilitação de autorização parte da sua implantação do Gerenciador de Recursos do Azure.

#### Habilitar autorização no Portal do Azure

1. Navegue até o aplicativo Web e clique em **Configurações** na barra de comandos.
2. Clique em **Autenticação/Autorização**. 
3. Clique em **Ativado**.

Em seguida, um aplicativo será automaticamente criado para você. Você precisará da ID do cliente do aplicativo para a Parte 3; assim:

1. Vá para o [Active Directory no portal do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione seu diretório. 
2. Procure o aplicativo na caixa de pesquisa
3. Clique nele na lista
4. Clique na guia **Configurar**
5. Você deve ver a **ID do cliente**

#### Implantando seu aplicativo Web usando um modelo ARM

Primeiro, você precisa criar um aplicativo para seu aplicativo Web. Ele deve ser diferente do aplicativo que é usado para o seu Aplicativo Lógico. Comece seguindo as etapas acima na Parte 1, mas, para **HomePage** e **IdentifierUris**, use a https://**URL** real do seu aplicativo Web.

>[AZURE.NOTE]Quando você cria o aplicativo para seu aplicativo Web, deve usar a [abordagem do portal Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), já que o commandlet do PowerShell não configura as permissões necessárias para conectar os usuários a um site.

Quando tiver as IDs de cliente e de locatário, inclua o seguinte como um sub-recurso do aplicativo Web em seu modelo de implantação:

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

Para executar uma implantação automaticamente e implantar um aplicativo Web em branco e um Aplicativo Lógico ao mesmo tempo usando AAD, clique no seguinte botão: [![Implantar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para o modelo completo, consulte [Aplicativo Lógico chama uma API personalizada hospedada no Serviço de Aplicativo e protegida por AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### Parte 3: popular a seção de autorização no Aplicativo Lógico

Na seção **Autorização** da ação **HTTP**: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrição |
|---------|-------------|
| type | Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é ActiveDirectoryOAuth. |
| locatário | O identificador do locatário usado para identificar o locatário do AD. |
| audiência | Obrigatório. O recurso a que você está se conectando. |
| clientID | O identificador de cliente para o aplicativo do AD do Azure. |
| segredo | Obrigatório. Segredo do cliente que está solicitando o token. | 

O modelo acima já tem isso definido, mas se você estiver criando o Aplicativo Lógico diretamente, precisará incluir a seção de autorização completa.

## Protegendo sua API em código

### Autenticação de certificado

Você pode usar certificados de cliente para validar solicitações de entrada para seu aplicativo Web. Consulte [Como configurar autenticação mútua TLS para o aplicativo Web](app-service-web-configure-tls-mutual-auth.md) para configurar seu código.

Na seção *Autorização*, você deve fornecer: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrição |
|---------|-------------|
| type | Obrigatório. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser ClientCertificate. |
| pfx | Obrigatório. Conteúdo codificado na Base64 do arquivo PFX. |
| Senha | Obrigatório. Senha para acessar o arquivo PFX. |

### Autenticação básica

Você pode usar a autenticação básica (por exemplo, nome de usuário e senha) para validar solicitações de entrada. A autenticação básica é um padrão comum e você pode fazê-la em qualquer idioma no qual você compile o seu aplicativo.

Na seção *Autorização*, você deve fornecer: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrição |
|---------|-------------|
| type | Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser Basic. |
| Nome de Usuário | Obrigatório. Nome de usuário para autenticação. |
| Senha | Obrigatório. Senha para autenticação. |
 
### Lidar com autenticação AAD em código

Por padrão, a autenticação do Active Directory do Azure que você habilita no Portal não tem autorizações refinadas. Por exemplo, ela não vincula sua API a um usuário ou aplicativo específico, mas somente a um locatário específico.

Se você quiser restringir a API somente para o Aplicativo Lógico, por exemplo, no código, você pode extrair o cabeçalho que contém o JWT e verificar quem é o chamador, rejeitando todas as solicitações que não corresponderem.

Além disso, se você deseja implementá-lo inteiramente em seu próprio código e não utilizar o recurso de Portal, pode ler este artigo: [Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure](web-sites-authentication-authorization.md).

Você ainda precisará seguir as etapas acima para criar uma identidade de aplicativo para seu Aplicativo Lógico e usá-la para chamar a API.

<!---HONumber=Oct15_HO2-->