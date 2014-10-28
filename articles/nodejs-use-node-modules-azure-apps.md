<properties linkid="develop-nodejs-common-tasks-working-with-node-modules" urlDisplayName="Working with Node.js Modules" pageTitle="Working with Node.js Modules" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Usando Módulos no Node.js com aplicativos do Windows Azure

Este documento fornece orientação sobre como usar módulos no Node.js com aplicativos hospedados no Windows Azure. Fornece orientação para garantir que seu aplicativo use uma versão específica do módulo, bem como para usar módulos nativos com o Windows Azure.

Se já estiver acostumado a usar os módulos no Node.js e os arquivos **package.json** e **shrinkwrap.json npm**, encontre a seguir um rápido resumo do que será discutido neste artigo:

-   Os Sites do Windows Azure compreendem arquivos **package.json** e **npm-shrinkwrap.json** e podem instalar módulos com base em entradas nestes arquivos.
-   Os Serviços de Nuvem do Windows Azure esperam que todos os módulos sejam instalados no ambiente de desenvolvimento e que o diretório **node\_modules** seja incluído como parte do pacote de implantação.

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>As M&aacute;quinas Virtuais do Windows Azure n&atilde;o ser&atilde;o discutidas neste artigo, j&aacute; que a experi&ecirc;ncia de implanta&ccedil;&atilde;o em uma VM depende do sistema operacional hospedado pela M&aacute;quina Virtual.</p>
</div>

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>&Eacute; poss&iacute;vel habilitar o suporte para instalar m&oacute;dulos usando arquivos <b>package.json</b> ou <b>npm-shrinkwrap.json</b> no Windows Azure, no entanto, isto requer uma personaliza&ccedil;&atilde;o dos scripts padr&atilde;o usados por projetos de Servi&ccedil;os de Nuvem. Para obter um exemplo de como realizar isto, consulte a <a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">Tarefa de Inicializa&ccedil;&atilde;o do Windows Azure para executar a instala&ccedil;&atilde;o do npm e evitar a implanta&ccedil;&atilde;o de m&oacute;dulos de n&oacute; </a></p>
</div>

## Módulos no Node.js

Os módulos são pacotes carregáveis do JavaScript que fornecem funcionalidade específica para seu aplicativo. Em geral, instala-se um módulo com a ferramenta de linha de comando **npm**. Porém, alguns módulos (como o http) são fornecidos como parte do pacote base do Node.js.

Quando os módulos são instalados, são armazenados no diretório **node\_modules**, na raiz da estrutura do diretório de seu aplicativo. Cada módulo dentro do diretório **node\_modules** mantém seu próprio diretório **node\_modules**, que contém todos os módulos dos quais ele depende; o mesmo corre para cada módulo em toda a cadeia de dependência. Isso permite que cada módulo instalado tenha seus próprios requisitos de versão para os módulos dos quais depende, porém, o resultado pode ser uma estrutura de diretório muito grande.

Ao implantar o diretório **node\_modules** como parte de seu aplicativo, este aumentará o tamanho da implantação em comparação ao uso de um arquivo **package.json** ou **npm shrinkwrap.json**, mas garantirá que a versão dos módulos usados na produção seja a mesma dos módulos usados no desenvolvimento.

### Módulos Nativos

Enquanto a maioria dos módulos são simples arquivos de texto JavaScript, outros são imagens binárias específicas de plataforma. Estes módulos são compilados no momento da instalação, geralmente usando o Python e o node-gyp. Uma limitação específica dos Sites do Windows Azure é que, embora compreendam nativamente como instalar módulos especificados em um arquivo **package.json** ou **shrinkwrap.json npm**, não fornecem o Python ou o node-gyp, além de não permitirem criar módulos nativos.

Uma vez que os Serviços de Nuvem do Windows Azure contam com a pasta **node\_modules** sendo implantada como parte do aplicativo, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar em um serviço de nuvem, desde que tenha sido instalado e compilado em um sistema de desenvolvimento do Windows.

Módulos nativos não são compatíveis com Sites do Windows Azure. Alguns módulos, como o JSDOM e o MongoDB, têm dependências nativas opcionais e funcionarão com aplicativos hospedados em Sites do Windows Azure.

### Usando um arquivo package.json

O arquivo **package.json** é uma maneira de especificar as dependências de nível superior que seu aplicativo requer para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir a inclusão da pasta **node\_packages** como parte da implantação. Após o aplicativo ser implantado, o comando **instalar npm** é usado para analisar o arquivo **package.json** e instalar todas as dependências listadas.

Ao instalar módulos durante o desenvolvimento, você pode usar os parâmetros **--salvar**, **-salvar-des** ou **– salvar-opcional** para adicionar, automaticamente, uma entrada para o módulo de seu arquivo **package.json**. Para obter mais informações, consulte [instalar-npm][instalar-npm].

Um problema potencial com o arquivo **package.json** é que este só especifica a versão para dependências de nível superior. Cada módulo instalado pode ou não especificar a versão dos módulos da qual depende e, desta forma, é possível que você acabe com uma cadeia de dependências diferente daquele usada no desenvolvimento.

> [WACOM.NOTE]
> Ao realizar uma implantação para um Site do Windows Azure, se seu arquivo **package.json** fizer referência a um módulo nativo, ao publicar o aplicativo usando o Git, você verá um erro semelhante ao seguinte:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1    

### Usando um arquivo npm-shrinkwrap.json

O arquivo **npm-shrinkwrap.json** é uma tentativa de resolver as limitações da versão do módulo do arquivo **package.json**. Enquanto o arquivo **package.json** inclui apenas versões para os módulos de nível superior, o arquivo **npm shrinkwrap.json** contém os requisitos de versão para a cadeia de dependências de módulo completo.

Quando seu aplicativo estiver pronto para produção, você poderá bloquear os requisitos de versão e criar um arquivo **npm-shrinkwrap.json** usando o comando **npm shrinkwrap**. Isto irá usar as versões atualmente instaladas na pasta **node\_modules** e registrá-las no arquivo **npm-shrinkwrap.json**. Após o aplicativo ser implantado no ambiente de hospedagem, o comando **instalar npm** é usado para analisar o arquivo **npm-shrinkwrap.json** e instalar todas as dependências listadas. Para obter mais informações, consulte [instalar-npm][instalar-npm].

> [WACOM.NOTE]
> Ao realizar uma implantação para um Site do Windows Azure, se seu arquivo **npm-shrinkwrap.json** fizer referência a um módulo nativo, ao publicar o aplicativo usando o Git, você verá um erro semelhante ao seguinte:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1

## Próximas etapas

Agora que você aprendeu como usar os módulos no Node.js com o Windows Azure, aprenda como [especificar a versão do Node.js][especificar a versão do Node.js], [criar e implantar um Site no Node.js][criar e implantar um Site no Node.js] e [Como usar as Ferramentas de Linha de Comando do Windows Azure para Mac e Linux][Como usar as Ferramentas de Linha de Comando do Windows Azure para Mac e Linux].

  [Tarefa de Inicialização do Windows Azure para executar a instalação do npm e evitar a implantação de módulos de nó]: http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure
  [instalar-npm]: https://npmjs.org/doc/install.html
  [especificar a versão do Node.js]: /pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/
  [criar e implantar um Site no Node.js]: /pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Como usar as Ferramentas de Linha de Comando do Windows Azure para Mac e Linux]: /pt-br/documentation/articles/xplat-cli/
