<properties pageTitle="Especificando uma versão de Node.js" description="Saiba como especificar a versão de Node.js usada pelos Sites e Serviços de Nuvem do Azure" services="" documentationCenter="nodejs" authors="MikeWasson" manager="wpickett" editor="mollybos"/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="02/19/2015" ms.author="mwasson"/>





# Especificar uma versão do Node.js em um aplicativo do Azure

Ao hospedar um aplicativo Node.js, talvez você queira garantir que seu aplicativo use uma versão específica do Node.js. Há várias maneiras de fazer isso para aplicativos hospedados no Azure.

##Versões padrão

As versões de Node.js fornecidas pelo Azure são atualizadas constantemente. Salvo indicação em contrário, a versão mais recente será usada. Atualmente, as versões a seguir estão incluídas:

- 0\.12.x: 0.12.0
- 0\.10.x: 0.10.32, 0.10.31, 0.10.29, 0.10.28, 10.26, 0.10.24, 0.10.21, 0.10.18, 0.10.5
- 0\.8.x: 0.8.28, 0.8.27, 0.8.26, 0.8.19, 0.8.2
- 0\.6: 0.6.20, 0.6.17

> [AZURE.NOTE]Se você estiver hospedando o seu aplicativo em um Serviço de Nuvem do Azure (função Web ou de trabalho), e é a primeira vez que você implantou o aplicativo, o Azure tentará usar a mesma versão do Node.js instalada em seu ambiente de desenvolvimento se ele corresponder a uma das versões padrão disponíveis no Azure.

##Controle de versão com o package.json

Você pode especificar a versão do Node.js a ser usada adicionando o seguinte ao seu arquivo **package.json**:

	"engines":{"node":version}

No qual *versão* é o número da versão específica a ser usado. Você pode especificar condições mais complexas de versão, como:

	"engines":{"node": "0.6.22 || 0.8.x"}

Como 0.6.22 não é uma das versões disponíveis no ambiente de hospedagem, a versão mais recente da série 0.8 disponível será usada em vez de - 0.8.4.

##Sites de controle de versão com configurações de aplicativo
Se você estiver hospedando o aplicativo em um site, poderá definir a variável de ambiente **WEBSITE_NODE_DEFAULT_VERSION** para a versão desejada.

##Controle de versão dos Serviços de Nuvem com o PowerShell

Se você estiver hospedando o aplicativo em um Serviço de Nuvem e estiver implantando o aplicativo usando o PowerShell do Azure, você pode substituir a versão padrão do Node.js, usando o cmdlet do PowerShell **Set-AzureServiceProjectRole**. Por exemplo:

	Set-AzureServiceProjectRole WebRole1 node 0.8.4

Você também pode usar o **Get-AzureServiceProjectRoleRuntime** para recuperar uma lista de versões do Node.js disponíveis para aplicativos hospedados como um Serviço de Nuvem.

##Usando uma versão personalizada com Sites do Azure

Enquanto o Azure fornece várias versões padrão do Node.js, talvez você queira usar uma versão que não é fornecida por padrão. Se o aplicativo está hospedado como um Site do Azure, você pode fazer isso usando o arquivo **iisnode.yml**. As etapas a seguir orientam sobre o processo de uso de uma versão personalizada do Node.js com um Site do Azure:

1. Crie um novo diretório e crie um arquivo **server.js** no diretório. O arquivo **server.js** deve conter o seguinte:

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	Será exibida a versão do Node.js que está sendo usada quando você navega no site.

2. Crie um novo Site e anote o nome do site. Por exemplo, o seguinte site usa as [Ferramentas de linha de comando do Azure] para criar um novo Site do Azure chamado **mywebsite**e ative um repositório de Git para o site.

		azure site create mywebsite --git

3. Crie um novo diretório chamado **bin** como um filho do diretório que contém o arquivo **server.js**.

4. Baixe a versão específica do **node.exe** (a versão do Windows) que você deseja usar com o aplicativo. Por exemplo, os seguintes usos **curl** para baixar a versão 0.8.1:

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	Salve o arquivo **node.exe** na pasta **bin** criada anteriormente.

5. Crie um arquivo **iisnode.yml** no mesmo diretório que o arquivo **server.js** e adicione o seguinte conteúdo para o arquivo **iisnode.yml**:

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	Esse caminho é onde o arquivo **node.exe** no seu projeto será localizado depois de ter publicado o aplicativo no Site do Azure.

6. Publique o aplicativo. Por exemplo, uma vez que criei um novo site com o parâmetro --git anteriormente, os comandos a seguir adicionarão os arquivos do aplicativo ao meu repositório Git local e serão enviados por push ao repositório do site:

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	Depois que o aplicativo tiver sido publicado, abra o site em um navegador. Você deve ver uma mensagem dizendo "Olá da versão do nó que executa o Azure: v0.8.1".

##Próximas etapas

Agora que você aprendeu como especificar a versão do Node.js usada pelo seu aplicativo, saiba como [trabalhar com módulos], [criar e implantar um Site no Node.js] e [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux].

[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: xplat-cli.md
[Ferramentas de linha de comando do Azure]: xplat-cli.md
[trabalhar com módulos]: nodejs-use-node-modules-azure-apps.md
[criar e implantar um Site no Node.js]: web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=July15_HO3-->