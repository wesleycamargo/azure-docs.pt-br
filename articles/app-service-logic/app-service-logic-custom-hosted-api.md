<properties
	pageTitle="Chamar uma API personalizada em Aplicativos Lógicos"
	description="Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="stepsic"/>

# Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos

Embora os Aplicativos Lógicos tenham um conjunto avançado de mais de 40 conectores para uma gama de serviços, convém chamar a sua própria API personalizada que pode executar seu próprio código. Uma das maneiras mais fáceis e mais escalonáveis de hospedar sua própria API Web personalizada é usar o Serviço de Aplicativo. Este artigo aborda como chamar a API Web hospedada em um aplicativo de API do Serviço de Aplicativo, aplicativo Web ou aplicativo móvel.

Para obter informações sobre a criação de APIs como um gatilho ou ação nos Aplicativos Lógicos, confira [este artigo](app-service-logic-create-api-app.md).

## Implante seu aplicativo Web.

Primeiro, você precisa implantar sua API como um aplicativo Web no Serviço de Aplicativo. Estas instruções abordam a implantação básica: [Criar um aplicativo Web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md). Embora você possa chamar qualquer API de um Aplicativo Lógico, para ter a melhor experiência possível, é recomendável adicionar metadados do Swagger para integrar facilmente as ações de Aplicativos Lógicos. Você pode encontrar mais detalhes sobre a [adição ao swagger](../app-service-api/app-service-api-dotnet-get-started.md/#use-swagger-metadata-and-ui).

### Configurações da API

Para o designer de Aplicativos Lógicos analisar o Swagger, é importante habilitar o CORS e definir as propriedades de APIDefinition do seu aplicativo Web. Isso é muito fácil de definir no Portal do Azure. Basta abrir a folha de configurações do seu Aplicativo Web e, na seção da API, configurar a “Definição da API” como a URL do seu arquivo swagger.json (normalmente é https://{name}.azurewebsites.net/swagger/docs/v1)), depois adicionar uma política CORS como “*” para permitir solicitações do Designer de Aplicativos Lógicos.

## Chamando a API

Quando estiver no portal de Aplicativos Lógicos, se você tiver configurado as propriedades do CORS e de Definição da API, você deverá ser capaz de adicionar facilmente ações de API Personalizada ao seu fluxo. No designer, você pode optar que seus sites de assinatura listem os sites com uma URL de swagger definida. Você também pode usar a ação HTTP + Swagger para apontar para um swagger e listar as ações e entradas disponíveis. Por fim, você sempre pode criar uma solicitação usando a ação de HTTP para chamar qualquer API, mesmo aquelas que não têm ou expõem um documento do swagger.

Se desejar proteger sua API, existem algumas maneiras diferentes de fazer isso:

1. Nenhuma alteração de código necessária: o Azure Active Directory pode ser usado para proteger sua API sem exigir alterações ou reimplantação de código.
1. Impor as autenticações básica, AAD ou de certificado no código de sua API.

## Protegendo chamadas à API sem alteração de código

Nesta seção, você vai criar dois aplicativos do Active Directory do Azure, um para o seu Aplicativo Lógico e outro para seu aplicativo Web. Você vai autenticar chamadas para seu aplicativo Web usando a entidade de serviço (id e segredo do cliente) associada ao aplicativo AAD para seu Aplicativo Lógico. Finalmente, você irá incluir os IDs de aplicativo em sua definição do Aplicativo Lógico.

### Parte 1: configurando uma identidade de aplicativo para seu Aplicativo Lógico

Isso é o que o Aplicativo Lógico usa para autenticar no Active Directory. Você *precisa* fazer isso apenas uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos lógicos, embora também possa criar identidades exclusivas por aplicativo lógico se desejar. Você pode fazer isso na interface do usuário ou usar o PowerShell.

#### Criar a identidade do aplicativo usando o Portal clássico do Azure

1. Navegue até o [Active Directory no Portal clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione o diretório que você usa para seu aplicativo Web
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

Neste ponto, um aplicativo é criado automaticamente para você. Você precisará da ID do cliente do aplicativo para a Parte 3, então você precisará:

1. Vá para o [Active Directory no Portal clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione seu diretório.
2. Procure o aplicativo na caixa de pesquisa
3. Clique nele na lista
4. Clique na guia **Configurar**
5. Você deve ver a **ID do cliente**

#### Implantando seu aplicativo Web usando um modelo ARM

Primeiro, você precisa criar um aplicativo para seu aplicativo Web. Ele deve ser diferente do aplicativo que é usado para o seu Aplicativo Lógico. Comece seguindo as etapas acima na Parte 1, mas, para **HomePage** e **IdentifierUris**, use a https://**URL** real do seu aplicativo Web.

>[AZURE.NOTE]Quando você cria o aplicativo para seu aplicativo Web, deve usar a [abordagem do Portal clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), já que o commandlet do PowerShell não configura as permissões necessárias para conectar os usuários a um site.

Quando tiver a ID de cliente a ID de locatário, inclua o seguinte como um recurso secundário do aplicativo Web no seu modelo de implantação:

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

Para executar automaticamente uma implantação que implanta ao mesmo tempo um aplicativo Web em branco e um Aplicativo lógico usando o AAD, clique no seguinte botão:

[![Implantar no Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

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

Você pode usar certificados de cliente para validar solicitações de entrada para seu aplicativo Web. Consulte [Como configurar autenticação mútua TLS para o aplicativo Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para configurar seu código.

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

Além disso, se você deseja implementá-lo inteiramente em seu próprio código e não utilizar o recurso de Portal, pode ler este artigo: [Usar o Active Directory para autenticação no Serviço de Aplicativo do Azure](../app-service-web/web-sites-authentication-authorization.md).

Você ainda precisa seguir as etapas acima para criar uma identidade de aplicativo para seu Aplicativo Lógico e usá-la para chamar a API.

<!---HONumber=AcomDC_0420_2016-->