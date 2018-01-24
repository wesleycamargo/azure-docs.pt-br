---
title: "Implantação por push do zip para o Azure Functions | Microsoft Docs"
description: "Use os recursos de implantação de arquivo .zip do serviço de implantação do Kudu para publicar seu Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Implantação por push do zip para o Azure Functions 
Este artigo descreve como implantar seus arquivos de projeto de aplicativo de função para o Azure de um arquivo .zip (compactado). Você aprenderá a realizar uma implantação de envio por push, usando a CLI do Azure e usando as APIs REST. 

O Azure Functions tem a gama completa de opções de implantação e integração contínuas que são fornecidas pelo Serviço de Aplicativo do Azure. Para saber mais, confira [Implantação contínua do Azure Functions](functions-continuous-deployment.md). 

Para uma interação mais rápida durante o desenvolvimento, geralmente é mais fácil implantar seus arquivos de projeto de aplicativo da funções diretamente de um arquivo .zip compactado. Essa implantação de arquivo .zip usa o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua, incluindo:

+ Exclusão de arquivos que foram mantidos de implantações anteriores.
+ Personalização da implantação, incluindo execução de scripts de implantação.
+ Logs de implantação.
+ Gatilhos de função de sincronização em um aplicativo de funções de [Plano de consumo](functions-scale.md).

Para obter mais informações, consulte a [referência de implantação por push do .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Requisitos de implantação de arquivo .zip
O arquivo .zip que você usa para a implantação de envio deve conter todos os arquivos de projeto em seu aplicativo de funções, incluindo o código de função. 

>[!IMPORTANT]
> Quando você usa a implantação de envio de arquivos .zip, todos os arquivos de uma implantação existente que não são encontrados no arquivo .zip são excluídos do seu aplicativo de funções.  

### <a name="function-app-folder-structure"></a>Estrutura de pastas do aplicativo de funções

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Baixe o seu projeto de aplicativo de funções

Quando você estiver desenvolvendo em um computador local, é fácil criar um arquivo .zip da pasta do projeto de aplicativo de funções no computador de desenvolvimento. 

No entanto, você pode ter criado as suas funções usando o editor no Portal do Azure. Para baixar o seu projeto de aplicativo de funções do Portal: 

1. Conecte-se no [Portal do Azure](https://portal.azure.com) e, em seguida, vá para o seu aplicativo de funções.

2. Na guia **Visão Geral**, selecione **Baixar conteúdo do aplicativo**. Selecione as opções de download e, em seguida, selecione **Baixar**.     

    ![Baixe o projeto de aplicativo de funções](./media/deployment-zip-push/download-project.png)

O arquivo .zip baixado está no formato correto para ser republicado no seu aplicativo de funções usando a implantação por push do .zip.

Você também pode baixar um arquivo .zip de um repositório do GitHub. Note que quando você baixa um repositório GitHub como um arquivo .zip, o GitHub adiciona um nível extra de pasta para o branch. Esse nível extra de pasta significa que você não pode implantar o arquivo .zip diretamente como baixou do GitHub. Se você estiver usando um repositório GitHub para manter seu aplicativo de funções, você deve usar a [integração contínua](functions-continuous-deployment.md) para implantar seu aplicativo.  

## <a name="cli"></a>Implantar usando a CLI do Azure

Você pode usar a CLI do Azure para disparar uma implantação de envio por push. Implante o arquivo .zip em seu aplicativo de funções usando o comando [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip). Para usar esse comando, você deve usar a CLI do Azure versão 2.0.21 ou posterior. Para ver qual versão do CLI do Azure que você está usando, use o comando `az --version`.

No comando a seguir, substitua o espaço reservado `<zip_file_path>` pelo caminho para o local do seu arquivo .zip. Além disso, substitua `<app_name>` com o nome exclusivo do seu aplicativo de funções. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Esse comando implanta os arquivos de projeto do arquivo .zip baixado para seu aplicativo de funções no Azure. Em seguida, ele reinicia o aplicativo. Para visualizar a lista de implantações para o aplicativo, é necessário usar as APIs REST.

Quando você estiver usando a CLI do Azure em seu computador local, `<zip_file_path>` é o caminho para o arquivo .zip em seu computador. Você também pode executar a CLI do Azure no [Azure Cloud Shell](../cloud-shell/overview.md). Quando você usa o Cloud Shell, você deve primeiro carregar seu arquivo .zip de implantação para a conta do Azure Files associada com o seu Cloud Shell. Nesse caso, `<zip_file_path>` é o local de armazenamento que a sua conta do Cloud Shell usa. Para obter mais informações, consulte [Persistir arquivos no Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
