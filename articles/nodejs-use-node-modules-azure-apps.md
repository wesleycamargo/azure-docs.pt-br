<properties urlDisplayName="Working with Node.js Modules" pageTitle="Trabalhando com módulos do Node.js"metaKeywords ="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Usando Módulos no Node.js com aplicativos do Windows Azure

Este documento fornece orientação sobre como usar módulos no Node.js com aplicativos hospedados no Microsoft Azure. Fornece orientação para garantir que seu aplicativo use uma versão específica do módulo, bem como para usar módulos nativos com o Azure.

Se você já estiver acostumado a usar os módulos no Node.js e os arquivos **package.json** e **npm-shrinkwrap.json**, a seguir apresentamos um rápido resumo do que será discutido neste artigo:

* Os sites do Azure compreendem arquivos **package.json** e **npm-shrinkwrap.json** e podem instalar módulos com base em entradas nestes arquivos.
* Os Serviços de Nuvem do Microsoft Azure esperam que todos os módulos sejam instalados no ambiente de desenvolvimento e que o diretório **node\_modules** seja incluído como parte do pacote de implantação.

<div class="dev-callout">
<strong>Observação</strong>
<p>As Máquinas Virtuais do Windows Azure não serão discutidas neste artigo, já que a experiência de implantação em uma VM depende do sistema operacional hospedado pela Máquina Virtual.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>É possível habilitar o suporte para instalar módulos usando arquivos <b>package.json</b> ou <b>npm-shrinkwrap.json</b> no Windows Azure, no entanto, isto requer uma personalização dos scripts padrão usados por projetos de Serviços de Nuvem. Para obter um exemplo de como realizar isto, consulte a <a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">Tarefa de Inicialização do Windows Azure para executar a instalação do npm e evitar a implantação de módulos de nó </a></p>
</div>

##Módulos no Node.js

Os módulos são pacotes carregáveis do JavaScript que fornecem funcionalidade específica para seu aplicativo. Em geral, instala-se um módulo com a ferramenta de linha de comando **npm**, porém, alguns módulos (como o http) são fornecidos como parte do pacote base do Node.js.

Quando os módulos são instalados, são armazenados no diretório **node\_modules** na raiz da estrutura do diretório de seu aplicativo. Cada módulo dentro do diretório **node\_modules** mantém seu próprio diretório **node\_modules** que contém todos os módulos dos quais ele depende; o mesmo ocorre para cada módulo em toda a cadeia de dependência. Isso permite que cada módulo instalado tenha seus próprios requisitos de versão para os módulos dos quais depende, porém, o resultado pode ser uma estrutura de diretório muito grande.

Ao implantar o diretório **node\_modules** como parte de seu aplicativo, este aumentará o tamanho da implantação em comparação ao uso de um arquivo **package.json** ou **npm-shrinkwrap.json** mas garantirá que a versão dos módulos usados na produção seja a mesma dos módulos usados no desenvolvimento.

###Módulos Nativos

Enquanto a maioria dos módulos é de simples arquivos de texto JavaScript, outros são imagens binárias específicas de plataforma. Estes módulos são compilados no momento da instalação, geralmente usando o Python e o node-gyp. Uma limitação específica dos sites do Microsoft Azure é que, embora compreendam nativamente como instalar módulos especificados em um arquivo **package.json** ou **npm-shrinkwrap.json** não fornecem o Python ou node-gyp, além de não permitirem criar módulos nativos.

	Uma vez que os Serviços de Nuvem do Microsoft Azure contam com a pasta **node\_modules** sendo implantada como parte do aplicativo, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar em um serviço de nuvem, desde que tenha sido instalado e compilado em um sistema de desenvolvimento do Windows. 

Módulos nativos não são compatíveis com sites do Microsoft Azure. Alguns módulos, como o JSDOM e o MongoDB, têm dependências nativas opcionais e funcionarão com aplicativos hospedados em sites do Microsoft Azure.

###Usando um arquivo package.json

O arquivo **package.json** é uma maneira de especificar as dependências de nível superior que seu aplicativo requer para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir a inclusão da pasta **node\_packages** como parte da implantação. Após o aplicativo ser implantado, o comando **npm install** é usado para analisar o arquivo **package.json** e instalar todas as dependências listadas.

Durante o desenvolvimento, você pode usar os parâmetros **--save**, **--save-dev**, ou **--save-optional** ao instalar módulos para adicionar uma entrada para o módulo no arquivo **package.json** automaticamente. Para obter mais informações, consulte [npm-install](https://npmjs.org/doc/install.html).

Um problema potencial com o arquivo **package.json** é que este só especifica a versão para dependências de nível superior. Cada módulo instalado pode ou não especificar a versão dos módulos da qual depende e, desta forma, é possível que você acabe com uma cadeia de dependências diferente daquele usada no desenvolvimento. 

> [WACOM.NOTE]
> Ao realizar uma implantação em um site do Azure, se seu arquivo <b>package.json</b> fizer referência a um módulo nativo ao publicar o aplicativo usando o Git, você verá um erro semelhante ao seguinte:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1	


###Usando um arquivo npm-shrinkwrap.json

O arquivo **npm-shrinkwrap.json** é uma tentativa de resolver as limitações da versão do módulo do arquivo **package.json**. Enquanto o arquivo **package.json** inclui apenas versões para os módulos de nível superior, o arquivo **npm-shrinkwrap.json** contém os requisitos de versão para a cadeia de dependências de módulo completo.

Quando seu aplicativo estiver pronto para produção, você poderá bloquear os requisitos de versão e criar um arquivo **npm-shrinkwrap.json** usando o comando **npm shrinkwrap**. Isso usará as versões atualmente instaladas na pasta **node\_modules** e registrá-las no arquivo **npm-shrinkwrap.json**. Após o aplicativo ser implantado no ambiente de hospedagem, o comando **instalar npm** é usado para analisar o arquivo **npm-shrinkwrap.json** e instalar todas as dependências listadas. Para obter mais informações, consulte [npm-install](https://npmjs.org/doc/install.html).

> [WACOM.NOTE]
>Ao realizar uma implantação em um site do Azure, se seu arquivo <b>npm-shrinkwrap.json</b> fizer referência a um módulo nativo ao publicar o aplicativo usando o Git, você verá um erro semelhante ao seguinte:
		
>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Próximas etapas

Agora que você aprendeu a usar os módulos no Node.js com o Windows Azure, saiba como [especificar a versão do Node.js], [para criar e implantar um site no Node.js], e [Como usar as ferramentas de linha de comando do Microsoft Azure para Mac e Linux].

[especificar a versão do Node.js]: /pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/
[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: /pt-br/documentation/articles/xplat-cli/
[criar e implantar um Site no Node.js]: /pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[O aplicativo para Web Node.js com armazenamento no MongoDB (MongoLab)]: /pt-br/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/
[Publicando com o Git]: /pt-br/documentation/articles/web-sites-publish-source-control/
[Criar e implantar um aplicativo Node.jc para um Serviço de Nuvem do Windows Azure]: /pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/


