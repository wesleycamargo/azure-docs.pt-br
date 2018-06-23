---
title: Definir variáveis de ambiente em Instâncias de Contêiner do Azure
description: Saiba como definir variáveis de ambiente nos contêineres que você executa nas Instâncias de Contêiner do Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/07/2018
ms.author: marsma
ms.openlocfilehash: bc30352f50344031f8356d2be1b800dd035f12ad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830455"
---
# <a name="set-environment-variables"></a>Configurar variáveis de ambiente

A definição de variáveis de ambiente nas suas instâncias de contêiner permitem fornecer a configuração dinâmica do aplicativo ou script executado pelo contêiner. Para definir variáveis de ambiente em um contêiner, especifique-as ao criar uma instância de contêiner. É possível definir variáveis de ambiente ao iniciar um contêiner com a [CLI do Azure](#azure-cli-example), o [Azure PowerShell](#azure-powershell-example) e o [portal do Azure](#azure-portal-example).

Por exemplo, se você executar a imagem de contêiner [microsoft/aci-wordcount][aci-wordcount], poderá modificar o comportamento especificando as variáveis de ambiente a seguir:

*NumWords*: O número de palavras enviadas para STDOUT.

*MinLength*: O número mínimo de caracteres em uma palavra a serem contados. Um número mais alto ignora palavras comuns como "de" e "a" ou “o”.

Se você precisa passar segredos como variáveis de ambiente, as Instâncias de Contêiner do Azure são compatíveis com [valores seguros](#secure-values) para contêineres do Windows e do Linux.

## <a name="azure-cli-example"></a>Exemplos de CLI do Azure

Para ver a saída padrão do contêiner [microsoft/aci-wordcount][aci-wordcount] execute-a primeiro com este comando [az container create][az-container-create] (sem ambiente variáveis especificadas):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Para modificar a saída, inicie um segundo contêiner com o argumento `--environment-variables` incluído, especificando valores para as variáveis *NumWords* e *MinLength*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Quando o estado de ambos os contêineres for exibido como *Encerrado* (use [az container show][az-container-show] para verificar o estado), exiba os registros com [az container logs][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A saída dos contêineres mostra como você modificou o comportamento do script do segundo container definindo variáveis de ambiente.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemplo de Azure PowerShell

Definir variáveis de ambiente no PowerShell é semelhante à CLI, mas usa o argumento de linha de comando `-EnvironmentVariable`.

Primeiro, inicialize o contêiner [microsoft/aci-wordcount][aci-wordcount] na configuração padrão com este comando [New-AzureRmContainerGroup][new-azurermcontainergroup]:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Em seguida, execute o seguinte comando [New-AzureRmContainerGroup][new-azurermcontainergroup]. Este especifica as variáveis de ambiente *NumWords* e *MinLength* depois de preencher uma variável de matriz `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Quando o estado de ambos os contêineres for *Encerrado* (use [Get-AzureRmContainerInstanceLog][azure-instance-log] para verificar o estado), efetue pull dos logs com o comando [Get-AzureRmContainerInstanceLog][azure-instance-log].

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

A saída de cada contêiner mostra como você modificou o script executado pelo contêiner, definindo variáveis de ambiente.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exemplo do portal do Azure

Para definir variáveis de ambiente ao iniciar um contêiner no portal do Azure, especifique-as na página **Configuração** ao criar o contêiner.

Ao implantar usando o portal, atualmente está limitado a três variáveis e é necessário inseri-las neste formato: `"variableName":"value"`

Para ver um exemplo, inicie o contêiner [microsoft/aci-wordcount][aci-wordcount] com as variáveis *NumWords* e *MinLength*.

1. Em **Configuração**, configure a **Política de Reinício** para *Em falha*
2. Insira `"NumWords":"5"` para a primeira variável, selecione **Sim** em **Adicionar variáveis de ambiente adicionais** e insira `"MinLength":"8"` para a segunda variável. Selecione **OK** para verificar e implantar o contêiner.

![Página do portal mostrando a variável de ambiente Habilitar botão e caixas de texto][portal-env-vars-01]

Para exibir os logs do contêiner, em **CONFIGURAÇÕES** selecione **Contêineres** e, em seguida, **Logs**. Semelhante à saída mostrada nas seções CLI e PowerShell anteriores, é possível ver como o comportamento do script foi modificado pelas variáveis de ambiente. Apenas cinco palavras são exibidas, cada uma com um comprimento mínimo de oito caracteres.

![Portal mostrando a saída do log de contêiner][portal-env-vars-02]

## <a name="secure-values"></a>Valores seguros
Objetos com valores seguros servem para proteger informações confidenciais como senhas ou chaves para seu aplicativo. Usar valores seguros para variáveis de ambiente é mais seguro e flexível do que incluí-los na imagem de contêiner. Outra opção é usar os volumes secretos, descritos em [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md).

Variáveis de ambiente seguro com valores seguros não revelam o valor seguro nas propriedades do contêiner, assim, o valor só pode ser acessado de dentro de seu contêiner. Por exemplo, as propriedades de contêiner exibidas no portal do Azure ou na CLI do Azure não serão exibidas em uma variável de ambiente com um valor seguro.

A variável de ambiente seguro podem ser definida especificando a propriedade `secureValue` em vez de `value` para o tipo da variável. As duas variáveis definidas no YAML a seguir demonstram os dois tipos de variável.

### <a name="yaml-deployment"></a>Implantação do YAML

Crie um arquivo `secure-env.yaml` com o trecho a seguir.

```yaml
apiVersion: 2018-06-01
location: westus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "SECRET"
          "secureValue": "my-secret-value"
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Para implantar o grupo de contêineres com YAML, execute o comando a seguir.

```azurecli-interactive
az container create --resource-group myRG --name securetest -f secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verifique as variáveis de ambiente

Execute o seguinte comando para consultar as variáveis de ambiente do contêiner.

```azurecli-interactive
az container show --resource-group myRG --name securetest --query 'containers[].environmentVariables`
```

A resposta JSON com detalhes desse contêiner mostrará apenas a variável de ambiente não seguro e protegerá a chave da variável de ambiente.

```json
  "environmentVariables": [
    {
      "name": "NOTSECRET",
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET"
    }
```

É possível examinar se a variável de ambiente seguro está definida com o comando `exec`, o que permite executar um comando dentro de um contêiner em execução. 

Execute o comando a seguir para iniciar uma sessão interativa do Bash com o contêiner.
```azurecli-interactive
az container exec --resource-group myRG --name securetest --exec-command "/bin/bash"
```

Dentro do contêiner, imprima a variável de ambiente com o seguinte comando do Bash.
```bash
echo $SECRET
```

## <a name="next-steps"></a>Próximas etapas

Cenários baseados em tarefas, como o processamento em lote de um grande conjunto de dados com vários contêineres, podem se beneficiar de variáveis de ambiente personalizadas no tempo de execução. Para obter mais informações sobre a execução de contêineres com base em tarefas, consulte [Executar tarefas em contêineres nas Instâncias de Contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
