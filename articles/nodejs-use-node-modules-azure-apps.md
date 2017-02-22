---
title: "Trabalhando com módulos de Node.js"
description: "Aprenda a trabalhar com os módulos de Node.js ao usar o Serviço de Aplicativo ou Serviços de Nuvem do Azure."
services: 
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: f9a216f3f76ef84e745ef29575d20da971e5eb61
ms.openlocfilehash: 2ccd414e0eec04dbee7c412cef245e1ab0da2bff


---
# <a name="using-nodejs-modules-with-azure-applications"></a>Usando Módulos no Node.js com aplicativos do Microsoft Azure
Este documento fornece orientação sobre como usar módulos no Node.js com aplicativos hospedados no Microsoft Azure. Fornece orientação para garantir que seu aplicativo use uma versão específica do módulo, bem como para usar módulos nativos com o Microsoft Azure.

Se já estiver acostumado a usar os módulos no Node.js e os arquivos **package.json** e **npm-shrinkwrap.json**, encontre a seguir um rápido resumo do que será discutido neste artigo:

* O Serviço de Aplicativo do Azure compreende arquivos **package.json** e **npm-shrinkwrap.json** e podem instalar módulos com base em entradas nestes arquivos.
* Os Serviços de Nuvem do Microsoft Azure esperam que todos os módulos sejam instalados no ambiente de desenvolvimento e que o diretório **node\_modules** seja incluído como parte do pacote de implantação. É possível habilitar o suporte para instalar módulos usando arquivos **package.json** ou **npm-shrinkwrap.json** nos Serviços de Nuvem; no entanto, isto requer uma personalização dos scripts padrão usados por projetos de Serviço de Nuvem. Para obter um exemplo de como fazer isso, consulte [Tarefa de Inicialização do Azure para executar npm install e evitar a implantação de módulos de nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> As Máquinas Virtuais do Microsoft Azure não serão discutidas neste artigo, já que a experiência de implantação em uma VM depende do sistema operacional hospedado pela Máquina Virtual.
> 
> 

## <a name="nodejs-modules"></a>Módulos no Node.js
Os módulos são pacotes carregáveis do JavaScript que fornecem funcionalidade específica para seu aplicativo. Em geral, instala-se um módulo com a ferramenta de linha de comando **npm**. Porém, alguns módulos (como o http) são fornecidos como parte do pacote base do Node.js.

Quando os módulos são instalados, são armazenados no diretório **node\_modules**, na raiz da estrutura do diretório de seu aplicativo. Cada módulo dentro do diretório **node\_modules** mantém seu próprio diretório **node\_modules**, que contém todos os módulos dos quais ele depende; o mesmo ocorre para cada módulo em toda a cadeia de dependência. Isso permite que cada módulo instalado tenha seus próprios requisitos de versão para os módulos dos quais depende, porém, o resultado pode ser uma estrutura de diretório muito grande.

Ao implantar o diretório **node\_modules** como parte de seu aplicativo, este aumentará o tamanho da implantação em comparação ao uso de um arquivo **package.json** ou **npm-shrinkwrap.json**, mas assegurará que a versão dos módulos usados na produção seja a mesma dos módulos usados no desenvolvimento.

### <a name="native-modules"></a>Módulos Nativos
Enquanto a maioria dos módulos são simples arquivos de texto JavaScript, outros são imagens binárias específicas de plataforma. Estes módulos são compilados no momento da instalação, geralmente usando o Python e o node-gyp. Uma vez que os Serviços de Nuvem do Microsoft Azure contam com a pasta **node\_modules** sendo implantada como parte do aplicativo, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar em um serviço de nuvem, desde que tenha sido instalado e compilado em um sistema de desenvolvimento do Windows.

O Serviço de Aplicativo do Azure não dá suporte a todos os módulos nativos e pode falhar em compilá-los com pré-requisitos muito específicos. Embora alguns módulos populares como o MongoDB tenham dependências nativas opcionais e funcionem bem sem elas, duas soluções alternativas foram comprovadamente bem-sucedidas com quase todos os módulos nativos disponíveis hoje:

* Execute a **npm install** em uma máquina do Windows que tenha todos os pré-requisitos do módulo nativo. Em seguida, implante a pasta **node\_modules** criada como parte do aplicativo no Serviço de Aplicativo do Azure.
  * Antes de compilar, verifique se sua instalação local do Node.js tem arquitetura correspondente e se a versão é a mais próxima possível da usada no Azure (os valores atuais podem ser verificados no tempo de execução das propriedades **process.arch** e **process.version**).
* O Serviço de Aplicativo do Azure podem ser configurados para executar scripts bash ou de shell personalizados durante a implantação, oferecendo a você a oportunidade de executar comandos personalizados e configurar com precisão a maneira como o **npm install** é executado. Para obter um vídeo mostrando como fazer isso, consulte [Scripts de implantação de site personalizado com o Kudu].

### <a name="using-a-packagejson-file"></a>Usando um arquivo package.json
O arquivo **package.json** é uma maneira de especificar as dependências de nível superior que seu aplicativo requer para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir a inclusão da pasta **node\_packages** como parte da implantação. Após o aplicativo ser implantado, o comando **instalar npm** é usado para analisar o arquivo **package.json** e instalar todas as dependências listadas.

Ao instalar módulos durante o desenvolvimento, você pode usar os parâmetros **--save**, **--save-dev** ou **--save-optional** para adicionar, automaticamente, uma entrada para o módulo de seu arquivo **package.json**. Para obter mais informações, consulte [instalar-npm](https://docs.npmjs.com/cli/install).

Um problema potencial com o arquivo **package.json** é que este só especifica a versão para dependências de nível superior. Cada módulo instalado pode ou não especificar a versão dos módulos da qual depende e, desta forma, é possível que você acabe com uma cadeia de dependências diferente daquele usada no desenvolvimento.

> [!NOTE]
> Quando estiver implantando no Serviço de Aplicativo do Azure, se o seu arquivo <b>package.json</b> fizer referência a um módulo nativo, você verá um erro semelhante ao seguinte ao publicar o aplicativo usando Git:
> 
> npm ERR! Instalar module-name@0.6.0: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp rebuild"' falhou com 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Usando um arquivo npm-shrinkwrap.json
O arquivo **npm-shrinkwrap.json** é uma tentativa de resolver as limitações da versão do módulo do arquivo **package.json**. Enquanto o arquivo **package.json** inclui apenas versões para os módulos de nível superior, o arquivo **npm-shrinkwrap.json** contém os requisitos de versão para a cadeia de dependências de módulo completo.

Quando seu aplicativo estiver pronto para produção, você poderá bloquear os requisitos de versão e criar um arquivo **npm-shrinkwrap.json** usando o comando **npm shrinkwrap**. Isto usará as versões atualmente instaladas na pasta **node\_modules** e registrá-las no arquivo **npm-shrinkwrap.json**. Após o aplicativo ser implantado no ambiente de hospedagem, o comando **instalar npm** é usado para analisar o arquivo **npm-shrinkwrap.json** e instalar todas as dependências listadas. Para obter mais informações, consulte [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Quando estiver implantando no Serviço de Aplicativo do Azure, se o seu arquivo <b>npm-shrinkwrap.json</b> fizer referência a um módulo nativo, você verá um erro semelhante ao seguinte ao publicar o aplicativo usando Git:
> 
> npm ERR! Instalar module-name@0.6.0: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp rebuild"' falhou com 1
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como usar os módulos no Node.js com o Azure, aprenda como [especificar a versão do Node.js], [compilar e implantar um aplicativo Web Node.js](app-service-web/web-sites-nodejs-develop-deploy-mac.md) e [Como usar a Interface de Linha de Comando do Azure para Mac e Linux].

Para saber mais, confira o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

[especificar a versão do Node.js]: nodejs-specify-node-version-azure-apps.md
[Como usar a Interface de Linha de Comando do Azure para Mac e Linux]: xplat-cli-install.md
[build and deploy a Node.js web app]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de implantação de site personalizado com o Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/



<!--HONumber=Jan17_HO2-->


