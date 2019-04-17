---
title: Usar uma linha de comando inicial em instâncias de contêiner do Azure
description: Substituir o ponto de entrada configurado em uma imagem de contêiner quando você implanta uma instância de contêiner do Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: cbe14066cfd7493806176e834373e952daf19339
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610184"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Defina a linha de comando em uma instância de contêiner para substituir a operação de linha de comando padrão

Quando você cria uma instância de contêiner, especifique, opcionalmente, um comando para substituir a instrução de linha de comando padrão incorporada na imagem de contêiner. Esse comportamento é semelhante para o `--entrypoint` argumento de linha de comando para `docker run`.

Como definir [variáveis de ambiente](container-instances-environment-variables.md) para instâncias de contêiner, especificar uma linha de comando inicial é útil para trabalhos em lotes em que você precisa preparar cada contêiner dinamicamente com a configuração específica da tarefa.

## <a name="command-line-guidelines"></a>Diretrizes de linha de comando

* Por padrão, a linha de comando Especifica um *único processo que é iniciado sem um shell* no contêiner. Por exemplo, a linha de comando pode executar um script Python ou o arquivo executável. 

* Para executar vários comandos, começar sua linha de comando, definindo um ambiente de shell no sistema de operacional do contêiner (exemplos: `bin/sh`, `/bin/bash`, `cmd`). Siga as convenções do shell para combinar vários comandos para serem executados em sequência.

* Dependendo da configuração de contêiner, você talvez precise definir um caminho completo para o executável de linha de comando ou argumentos.

* Defina um apropriado [política de reinicialização](container-instances-restart-policy.md) para a instância de contêiner, dependendo se a linha de comando Especifica uma tarefa de longa execução ou uma tarefa de execução única. Por exemplo, uma política de reinicialização dos `Never` ou `OnFailure` é recomendado para uma tarefa de execução única. 

* Se você precisar obter informações sobre o ponto de entrada padrão definido em uma imagem de contêiner, use o [inspecionar de imagem do docker](https://docs.docker.com/engine/reference/commandline/image_inspect/) comando.

## <a name="command-line-syntax"></a>Sintaxe da linha de comando

A sintaxe de linha de comando varia dependendo da API do Azure ou da ferramenta usada para criar as instâncias. Se você especificar um ambiente de shell, também observe as convenções de sintaxe de comando do shell.

* [az container create][az-container-create] command: Passar uma cadeia de caracteres com o `--command-line` parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup] [ new-azurermcontainergroup] cmdlet do PowerShell do Azure: Passar uma cadeia de caracteres com o `-Command` parâmetro. Exemplo: `-Command "echo hello"`.

* Portal do Azure: No **substituição comando** a propriedade da configuração do contêiner, fornecer uma lista separada por vírgulas de cadeias de caracteres sem aspas. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo do Resource Manager ou arquivo YAML ou um dos SDKs do Azure: Especifique a propriedade de linha de comando como uma matriz de cadeias de caracteres. Exemplo: matriz JSON `["python", "myscript.py", "arg1", "arg2"]` em um modelo do Resource Manager. 

  Se você já conhece [Dockerfile](https://docs.docker.com/engine/reference/builder/) sintaxe, esse formato é semelhante ao *exec* forma da instrução CMD.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Único comando | `--command-line "python myscript.py arg1 arg2"` | **Comando substituir**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Vários comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Comando substituir**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplos de CLI do Azure

Por exemplo, modificar o comportamento do [microsoft/aci-wordcount] [ aci-wordcount] imagem de contêiner, que analisa o texto de Shakespeare *Hamlet* para localizar com mais frequência palavras que ocorrem. Em vez de analisar *Hamlet*, você pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do [microsoft/aci-wordcount] [ aci-wordcount] contêiner quando ele analisa o texto padrão, execute-o com o seguinte [criar contêiner az] [ az-container-create] comando. Nenhuma linha de comando inicial for especificada, portanto, para executar o comando de contêiner padrão. Para fins de ilustração, este exemplo define [variáveis de ambiente](container-instances-environment-variables.md) para localizar as principais 3 palavras que têm pelo menos cinco letras longo:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Depois que o estado do contêiner aparece como *encerrado* (usar [show de contêiner do az] [ az-container-show] para verificar o estado), exibir o log com [logs do contêiner az] [ az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora defina um segundo contêiner de exemplo para analisar texto diferente, especificando uma linha de comando diferente. O script de Python executado pelo contêiner, *wordcount.py*, aceita uma URL como um argumento e processa o conteúdo da página em vez do padrão.

Por exemplo, para determinar a parte superior 3 palavras que são pelo menos cinco letras longo em *Romeu e Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois que o contêiner estiver *Encerrado*, exiba a saída, mostrando os logs do contêiner:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Próximas etapas

Cenários baseados em tarefas, como um conjunto de dados grande com vários contêineres, de processamento em lotes podem se beneficiar das linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre como executar contêineres baseados em tarefas, consulte [executar tarefas em contêineres com diretivas de reinicialização](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
