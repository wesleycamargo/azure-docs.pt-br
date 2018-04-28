---
title: Definir variáveis de ambiente em Instâncias de Contêiner do Azure
description: Saiba como definir variáveis de ambiente em Instâncias de Contêiner do Azure
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Configurar variáveis de ambiente

A definição de variáveis de ambiente nas suas instâncias de contêiner permitem fornecer a configuração dinâmica do aplicativo ou script executado pelo contêiner.

Você atualmente é capaz de definir variáveis de ambiente da CLI e do PowerShell. Em ambos os casos, você usaria um sinalizador nos comandos para definir as variáveis de ambiente. A definição de variáveis de ambiente em ACI é semelhante ao argumento de linha de comando `--env` para `docker run`. Por exemplo, se você usar a imagem de contêiner aci/microsoft-wordcount, você poderá modificar o comportamento especificando as seguintes variáveis de ambiente:

*NumWords*: O número de palavras enviadas para STDOUT.

*MinLength*: O número mínimo de caracteres em uma palavra a serem contados. Um número mais alto ignora palavras comuns como "de" e "a" ou “o”.

## <a name="azure-cli-example"></a>Exemplos de CLI do Azure

Para ver a saída padrão do contêiner, execute o seguinte comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Ao especificar `NumWords=5` e `MinLength=8` para as variáveis de ambiente do contêiner, os logs do contêiner devem exibir uma saída diferente.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Depois que o status do contêiner aparecer como *Encerrado* (use [az container show][az-container-show] para verificar o status), exiba os logs para ver a saída.  Para exibir a saída do contêiner sem nenhuma variável de ambiente, defina --nome como mycontainer1 em vez de mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Exemplo de Azure PowerShell

Para ver a saída padrão do contêiner, execute o seguinte comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Ao especificar `NumWords=5` e `MinLength=8` para as variáveis de ambiente do contêiner, os logs do contêiner devem exibir uma saída diferente.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Depois que o status do contêiner estiver *Encerrado* (use [Get-AzureRmContainerInstanceLog][azure-instance-log] para verificar o status), exiba os logs para ver a saída. Para exibir os logs de contêiner sem nenhuma variável de ambiente, defina ContainerGroupName como mycontainer1 em vez de mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Exemplo de saída sem as variáveis de ambiente

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>Exemplo de saída com as variáveis de ambiente

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como personalizar a entrada para o contêiner, saiba como manter a saída de contêineres que são executados até o fim.
> [!div class="nextstepaction"]
> [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show