---
title: "Executar tarefas em contêineres nas Instâncias de Contêiner do Azure"
description: "Saiba como usar as Instâncias de Contêiner do Azure para executar tarefas que são executadas até a conclusão, como na compilação, teste ou trabalhos de renderização de imagem."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3c7c57b05220d1e82c3baa8bc266e02d961a84be
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Executar uma tarefa em contêiner nas Instâncias de Contêiner do Azure

A facilidade e a velocidade de implantação de contêineres nas Instâncias de Contêiner do Azure fornece uma plataforma atraente para executar tarefas de execução única como compilação, teste e renderização de imagem em uma instância de contêiner.

Com uma política de reinicialização configurável, você pode especificar que os contêineres são interrompidos quando os seus processos são concluídos. Como as instâncias de contêiner são cobradas por segundo, você será cobrado somente pelos recursos de computação usados enquanto o contêiner que executa a tarefa estiver em execução.

Os exemplos apresentados neste artigo usam a CLI do Azure. Você deve ter a CLI do Azure versão 2.0.21 ou superior [instalada localmente](/cli/azure/install-azure-cli), ou usar a CLI no [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinicialização de contêiner

Quando você cria um contêiner nas Instâncias de Contêiner do Azure, você pode especificar uma entre três configurações de política de reinicialização.

| Política de reinicialização   | Descrição |
| ---------------- | :---------- |
| `Always` | Contêineres no grupo de contêiner sempre são reiniciados. Este é a configuração **padrão** aplicada quando nenhuma política de reinicialização é especificada na criação do contêiner. |
| `Never` | Os contêineres no grupo de contêineres nunca reiniciados. Os contêineres são executados no máximo uma vez. |
| `OnFailure` | Os contêineres no grupo de contêineres são reiniciados somente quando o processo executado no contêiner falha (quando ele termina com um código de saída diferente de zero). Os contêineres são executados pelo menos uma vez. |

## <a name="specify-a-restart-policy"></a>Especificar uma política de reinicialização

Como especificar uma política de reinicialização depende de como você cria suas instâncias de contêiner, como com a CLI do Azure, cmdlets do Azure PowerShell, ou no portal do Azure. Na CLI do Azure, especifique o parâmetro `--restart-policy` ao chamar [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Executar o exemplo de conclusão

Para ver a política de reinicialização em ação, crie uma instância de contêiner a partir da imagem [microsoft/aci-wordcount](https://hub.docker.com/r/microsoft/aci-wordcount/) e especifique a política de reinicialização `OnFailure`. Este contêiner de exemplo executa um script de Python que, por padrão, analisa o texto de Shakespeare [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), grava as 10 palavras mais comuns em STDOUT e, em seguida, sai.

Execute o contêiner de exemplo com o seguinte comando [az container create][az-container-create]:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

As Instâncias de Contêiner do Azure iniciam o contêiner e, em seguida, o interrompem quando seu aplicativo (ou script, neste caso) é encerrado. Quando as Instâncias de Contêiner do Azure param um contêiner cuja política de reinicialização é `Never` ou `OnFailure`, o status do contêiner é definido como **Encerrado**. Você pode verificar o status de um contêiner usando o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Saída de exemplo:

```bash
"Terminated"
```

Depois que o status do contêiner de exemplo mostrar *Encerrado*, você pode ver a saída da tarefa ao exibir os logs do contêiner. Execute o comando [az container logs][az-container-logs] comando para exibir a saída do script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Saída:

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

Este exemplo mostra a saída que o script envia para STDOUT. As tarefas em contêineres, no entanto, podem escrever a saída para o armazenamento persistente para recuperação posterior. Por exemplo, para um [compartilhamento de arquivos do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Configurar os contêineres em tempo de execução

Quando você cria uma instância de contêiner, você pode definir suas **variáveis de ambiente**, bem como especificar uma **linha de comando** personalizada a ser executada quando o contêiner é iniciado. Você pode usar essas configurações em seus trabalhos em lotes para preparar cada contêiner com a configuração específica da tarefa.

## <a name="environment-variables"></a>Variáveis de ambiente

Defina variáveis de ambiente no seu contêiner para fornecer a configuração dinâmica do aplicativo ou script executado pelo contêiner. Isso é semelhante ao argumento de linha de comando `--env` para `docker run`.

Por exemplo, você pode modificar o comportamento do script no contêiner de exemplo especificando as seguintes variáveis de ambiente, quando você cria a instância de contêiner:

*NumWords*: O número de palavras enviadas para STDOUT.

*MinLength*: O número mínimo de caracteres em uma palavra a serem contados. Um número mais alto ignora palavras comuns como "de" e "a" ou “o”.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Ao especificar `NumWords=5` e `MinLength=8` para as variáveis de ambiente do contêiner, os logs do contêiner devem exibir uma saída diferente. Depois que o status do contêiner aparecer como *Encerrado* (use `az container show` para verificar o status), exiba os logs para ver a nova saída:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Substituição da linha de comando

Especifique uma linha de comando quando criar uma instância de contêiner para substituir a linha de comando implantada na imagem de contêiner. Isso é semelhante ao argumento de linha de comando `--entrypoint` para `docker run`.

Por exemplo, você fazer que o contêiner de exemplo analise um texto diferente de *Hamlet* especificando uma linha de comando diferente. O script de Python executado pelo contêiner, *wordcount.py*, aceita uma URL como um argumento e processará o conteúdo da página em vez do padrão.

Por exemplo, para determinar as três principais palavras de cinco letras em *Romeu e Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois que o contêiner estiver *Encerrado*, exiba a saída, mostrando os logs do contêiner:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Saída:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Próximas etapas

### <a name="persist-task-output"></a>Persistir saída da tarefa

Para obter detalhes sobre como persistir a saída de seus contêineres que são executados até a conclusão, consulte [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show