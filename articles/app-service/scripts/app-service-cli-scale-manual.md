---
title: Exemplo de Script CLI do Azure - Dimensionar um aplicativo Web manualmente usando a CLI do Azure 2.0 | Microsoft Docs
description: Exemplo de Script CLI do Azure - Dimensionar um aplicativo Web manualmente usando a CLI do Azure 2.0
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: ddd4646bd900ad5766c2a41a1afa70cdbaa581b1
ms.lasthandoff: 02/27/2017

---

# <a name="scale-a-web-app-manually"></a>Dimensionar manualmente um aplicativo Web

Nesse cenário, você aprenderá a criar um grupo de recursos, o aplicativo Web e o Plano do Serviço de Aplicativo. Em seguida, dimensionará o Plano do Serviço de Aplicativo de uma única instância para várias instâncias.

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`.

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/scale-manual/scale-manual.sh "Escala manual")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução doo exemplo de script, o comando a seguir poderá ser usado para remover o Grupo de Recursos, o aplicativo do Serviço de Aplicativo e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos, um aplicativo Web e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Cria um aplicativo web do Azure no plano do Serviço de Aplicativo. |
| [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) | Atualiza as propriedades do Plano do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
