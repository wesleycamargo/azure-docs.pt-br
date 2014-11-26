<properties linkid="develop-nodejs-common-tasks-specifying-a-node-version" urlDisplayName="Specifying a Node.js Version" pageTitle="Specifying a Node.js Version" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Especificar uma versão do Node. js em um aplicativo do Azure

Ao hospedar um aplicativo Node.js, talvez você queira garantir que seu aplicativo use uma versão específica do Node.js. Há várias maneiras de fazer isso para aplicativos hospedados no Azure.

## Versões padrão

Atualmente o Azure fornece as versões 0.6.17, 0.6.20 e 0.8.4. do Node.js. A menos que especificado em contrário, o 0.6.20 é a versão padrão a ser usada.

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Se voc&ecirc; estiver hospedando o seu aplicativo em um Servi&ccedil;o de Nuvem do Azure (fun&ccedil;&atilde;o Web ou de trabalho), e &eacute; a primeira vez que voc&ecirc; implantou o aplicativo, o Azure tentar&aacute; usar a mesma vers&atilde;o do Node.js instalada em seu ambiente de desenvolvimento se ele corresponder a uma das vers&otilde;es padr&atilde;o dispon&iacute;veis no Azure.</p>
</div>

## Controle de versão com o package.json

Você pode especificar a versão do Node.js a ser usada adicionando o seguinte ao seu arquivo **package.json**:

    "engines":{"node":version}

No qual *versão* é o número da versão específica a ser usado. Você pode especificar condições mais complexas de versão, como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Como 0.6.22 não é uma das versões disponíveis no ambiente de hospedagem, a versão mais recente da série 0.8 disponível será usada em vez de - 0.8.4.

## Controle de versão dos Serviços de Nuvem com o PowerShell

Se você estiver hospedando o aplicativo em um Serviço de Nuvem e estiver implantando o aplicativo usando o PowerShell do Azure, você pode substituir a versão padrão do Node.js, usando o cmdlet do PowerShell **Set-AzureServiceProjectRole**. Por exemplo:

    Set-AzureServiceProjectRole WebRole1 node 0.8.4

Você também pode usar o **Get-AzureServiceProjectRoleRuntime** para recuperar uma lista de versões do Node.js disponíveis para aplicativos hospedados como um Serviço de Nuvem.

## Usando uma versão personalizada com Sites do Azure

Enquanto o Azure fornece várias versões padrão do Node.js, talvez você queira usar uma versão que não é fornecida por padrão. Se o aplicativo está hospedado como um Site do Azure, você pode fazer isso usando o arquivo **iisnode.yml**. As etapas a seguir orientam sobre o processo de uso de uma versão personalizada do Node.js com um Site do Azure:

1.  Crie um novo diretório e crie um arquivo **server.js** no diretório. O arquivo **server.js** deve conter o seguinte:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Será exibida a versão do Node.js que está sendo usada quando você navega no site.

2.  Crie um novo Site e anote o nome do site. Por exemplo, o seguinte site usa as [Ferramentas de linha de comando do Azure][Ferramentas de linha de comando do Azure] para criar um novo Site do Azure chamado **mywebsite**e ative um repositório de Git para o site.

        azure site create mywebsite --git

3.  Crie um novo diretório chamado **bin** como um filho do diretório que contém o arquivo **server.js**.

4.  Baixe a versão específica do **node.exe** (a versão do Windows) que você deseja usar com o aplicativo. Por exemplo, os seguintes usos **curl** para baixar a versão 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Salve o arquivo **node.exe** na pasta **bin** criada anteriormente.

5.  Crie um arquivo **iisnode.yml** no mesmo diretório que o arquivo **server.js** e adicione o seguinte conteúdo para o arquivo **iisnode.yml**:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Esse caminho é onde o arquivo **node.exe** no seu projeto será localizado depois de ter publicado o aplicativo no Site do Azure.

6.  Publique o aplicativo. Por exemplo, uma vez que criei um novo site com o parâmetro --git anteriormente, os comandos a seguir adicionarão os arquivos do aplicativo ao meu repositório Git local e serão enviados por push ao repositório do site:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Depois que o aplicativo tiver sido publicado, abra o site em um navegador. Você deve ver uma mensagem dizendo "Olá da versão do nó que executa o Azure: v0.8.1".

## Próximas etapas

Agora que você aprendeu como especificar a versão do Node.js usada pelo seu aplicativo, saiba como [trabalhar com módulos][trabalhar com módulos], [criar e implantar um Site no Node.js][criar e implantar um Site no Node.js] e [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][Ferramentas de linha de comando do Azure].

  [Ferramentas de linha de comando do Azure]: /pt-br/documentation/articles/xplat-cli/
  [trabalhar com módulos]: /pt-br/documentation/articles/nodejs-use-node-modules-azure-apps/
  [criar e implantar um Site no Node.js]: /pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/
