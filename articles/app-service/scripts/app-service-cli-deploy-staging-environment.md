---
title: "Exemplo de Script CLI do Azure - Criar um aplicativo Web e implantar o código em um ambiente de preparo | Microsoft Docs"
description: "Exemplo de Script da CLI do Azure - Criar um aplicativo web e implantar o código em um ambiente de preparo"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 15a811d35aab9995f4895a9106771310a95f7a41
ms.openlocfilehash: 6ef9b43fb5fb88591186b20b52dd8f06b7d46cec
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Criar um aplicativo web e implantar o código em um ambiente de preparo

Este script de exemplo cria um aplicativo Web no serviço de aplicativo com um slot de implantação adicional chamado "teste" e, em seguida, implanta um aplicativo de exemplo para o slot de "teste".

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. 

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Criar um aplicativo web e implantar o código em um ambiente de preparo")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução doo exemplo de script, o comando a seguir poderá ser usado para remover o Grupo de Recursos, o aplicativo do Serviço de Aplicativo e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Cria um aplicativo Web do Azure. |
| [az appservice web deployment slot create](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#create) | Crie um slot de implantação. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa a um aplicativo Web do Azure com um repositório Git ou Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Abra um aplicativo Web do Azure em um navegador. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#swap) | Troca um slot de implantação especificado para produção. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
