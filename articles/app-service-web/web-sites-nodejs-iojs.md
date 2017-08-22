---
title: "Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure"
description: "Saiba como usar um aplicativo Web no Serviço de Aplicativo do Azure com io.js."
services: app-service\web
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d6320725-ffcb-4ad7-ba63-fc72fa2f2808
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 5c14bd2ae2effd554f52662961c2e219433f8cd8
ms.contentlocale: pt-br
ms.lasthandoff: 01/20/2017

---
# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure
A bifurcação de nó popular [io.js] apresenta várias diferenças em relação ao projeto do Node.js do Joyent, incluindo um modelo de controle mais aberto, um ciclo de lançamento mais rápido e uma adoção mais rápida de recursos novos e experimentais de JavaScript.

Embora os Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) tenham várias versões de Node.js pré-instaladas, também é possível usar um binário de Node.js fornecido pelo usuário. Este artigo discute dois métodos que habilitam o uso de io.js em Aplicativos Web do de Serviço de Aplicativo: o uso de um script de implantação estendido, que configura automaticamente o Azure para usar a última versão do io.js disponível, bem como o carregamento manual de um binário de io.js. 

<a id="deploymentscript"></a>

## <a name="using-a-deployment-script"></a>Usando um Script de implantação
Após a implantação de um aplicativo de Node.js, os Aplicativos Web do Serviço de Aplicativo executam uma série de pequenos comandos para garantir que o ambiente seja configurado corretamente. Ao usar um script de implantação, esse processo pode ser personalizado para incluir o download e a configuração de io.js.

O [Script de Implantação io.js](https://github.com/felixrieseberg/iojs-azure) está disponível no GitHub. Para habilitar io.js em seu aplicativo Web, basta copiar **.deployment**, **deploy.cmd** e **IISNode.yml** para a raiz da pasta do aplicativo e implantar nos Aplicativos Web.  

O primeiro arquivo, **.deployment**, instrui os Aplicativos Web a executar **deploy.cmd** na implantação. Esse script executa todas as etapas normais para um aplicativo Node. js, mas também baixa a versão mais recente do io.js. Por fim, **IISNode.yml** configura os Aplicativos Web para usar apenas o binário do io.js baixado em vez de um binário pré-instalado do Node.js.

> [!NOTE]
> Para atualizar o binário do io.js usado, apenas reimplante seu aplicativo - o script baixará uma nova versão do io.js toda vez que o aplicativo for implantado.
> 
> 

<a id="manualinstallation"></a>

## <a name="using-manual-installation"></a>Usando a instalação manual
A instalação manual de uma versão personalizada do io.js inclui apenas duas etapas. Primeiro, baixe o binário de **win-x64** diretamente da [distribuição de io.js]. São necessários dois arquivos: **iojs.exe** e **iojs.lib**. Salve os dois arquivos e uma pasta dentro de seu aplicativo Web, por exemplo, em **bin/iojs**.

Para configurar Aplicativos Web para usar **iojs.exe** em vez de uma versão pré-instalada de Node, crie um arquivo **IISNode.yml** na raiz do aplicativo e adicione a linha a seguir.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar io.js com Aplicativos Web do Serviço de Aplicativo, usando os scripts de implantação fornecidos, bem como a instalação manual. 

> [!NOTE]
> O io.js está em desenvolvimento e mais atualizado do que o Node.js. Vários módulos de Node.js talvez não funcionem com io.js. Confira [io.js no GitHub] para a solução de problemas.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[io.js]: https://iojs.org
[distribuição de io.js]: https://iojs.org/dist/
[io.js no GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure

