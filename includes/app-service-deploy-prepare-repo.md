---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850761"
---
## <a name="prepare-your-repository"></a>Preparar o repositório

Para obter as compilações automáticas do servidor de compilação Kudu do Serviço de Aplicativo do Azure, certifique-se de que a raiz do repositório tem os arquivos corretos em seu projeto.

| Tempo de execução | Arquivos do diretório raiz |
|-|-|
| ASP.NET (somente Windows) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (somente Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ com um script de início |
| Python (somente Windows) | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Trabalhos Web | _\<nome_trabalho>/run.\<extension>_ em _App\_Data/jobs/continuous_ (para WebJobs contínuos) ou _App\_Data/jobs/triggered_ (para WebJobs disparados). Para saber mais, veja a [documentação de WebJobs Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funções | Confira [Implantação contínua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar a implantação, você pode incluir um arquivo _.deployment_ na raiz do repositório. Para saber mais, confira [Personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se você desenvolver no Visual Studio, permita que o [Visual Studio cria um repositório para você](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). O projeto esatará imediatamente pronto para ser implantado usando o Git.
>
>

