---
title: "Implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo zip | Microsoft Docs"
description: "Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo zip."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo zip

Este artigo mostra como usar um arquivo zip para implantar seu aplicativo Web no [Serviço de Aplicativo do Azure](app-service-web-overview.md). 

Essa implantação de arquivo zip usa o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua. O Kudu dá suporte para a seguinte funcionalidade para implantação de arquivo zip: 

- Exclusão de arquivos remanescentes de uma implantação anterior.
- Opção para ativar o processo de compilação padrão que inclui a restauração do pacote.
- [Personalização da implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluindo execução de scripts de implantação.  
- Logs de implantação. 

Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>pré-requisitos

Para completar as etapas neste artigo:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.

## <a name="create-a-project-zip-file"></a>Criar um projeto de arquivo zip

>[!NOTE]
> Se você fez o download dos arquivos em um arquivo zip, primeiro extraia os arquivos. Por exemplo, se você fez o download de um arquivo zip do GitHub, não será possível implantar esse arquivo. O GitHub adiciona diretórios aninhados adicionais que não funcionam com o Serviço de Aplicativo. 
>

Em uma janela do terminal local, navegue até o diretório raiz do projeto do aplicativo. 

Esse diretório deve conter o arquivo de entrada no seu aplicativo Web, como _index.html_, _index.php_ e _app.js_. Além disso, é possível conter arquivos de gerenciamento de pacotes como _project.json_, _composer.json_, _package.json_, _bower.json_ e _requirements.txt_.

Criar um arquivo zip de tudo em seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Fazer upload do arquivo zip para Cloud Shell

Se você optar por executar a CLI do Azure do seu terminal local, ignore essa etapa.

Siga as etapas aqui para fazer upload do seu arquivo zip para o Cloud Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Para obter mais informações, consulte [Persistir arquivos no Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Implantar arquivo ZIP

Implante o arquivo zip carregado no seu aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Se você optar por não usar o Cloud Shell, certifique-se de que a versão da CLI do Azure é 2.0.21 ou mais recente. Para ver qual versão você possui, execute o comando `az --version` na janela do terminal local. 

O exemplo a seguir implanta o arquivo zip do upload que você fez. Ao usar uma instalação local da CLI do Azure, especifique o caminho para o arquivo zip local para `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Esse comando implanta os arquivos e diretórios do arquivo zip para sua pasta de aplicativo do Serviço de Aplicativo do Azure padrão (`\home\site\wwwroot`) e reinicia o aplicativo. Se qualquer processo de compilação personalizada adicional for configurado, ele também será executado. Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Para visualizar a lista de implantações para o aplicativo, é necessário usar as APIs REST (veja a próxima seção). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Próximas etapas

Para cenários mais avançados de implantação, tente [implantação no Azure com Git](app-service-deploy-local-git.md). Implantação baseada em Git no Azure permite o controle de versão, restauração do pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: implementação de um arquivo zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciais de implantação do Serviço de Aplicativo do Azure](app-service-deploy-ftp.md)
