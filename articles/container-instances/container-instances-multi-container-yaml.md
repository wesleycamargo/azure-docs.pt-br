---
title: Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure com a CLI do Azure e YAML
description: Saiba como implantar um grupo de contêiner com vários contêineres em Instâncias de Contêiner do Azure usando a CLI do Azure e um arquivo YAML.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 10f2340bd85da3dabcd50d51a4dd56d58d31675b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372430"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Implantar um grupo com vários contêineres com YAML

As Instâncias de Contêiner do Azure são compatíveis com a implantação de vários contêineres em um único host utilizando um [grupo de contêineres](container-instances-container-groups.md). Os grupos com vários contêineres são úteis ao criar um aplicativo secundário para registro em log, monitoramento ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Há dois métodos para implantar o contêiner com vários grupos usando a CLI do Azure:

* Implantação do arquivo YAML (este artigo)
* [Implantação de modelo do Resource Manager](container-instances-multi-container-group.md)

Devido à natureza mais concisa do formato YAML, recomendamos a implantação com um arquivo YAML quando sua implantação incluir *somente* instâncias de contêiner. Se você precisar implantar recursos adicionais de serviço do Azure (por exemplo, um compartilhamento de Arquivos do Azure) no momento da implantação da instância de contêiner, recomendamos a implantação de modelo do Resource Manager.

> [!NOTE]
> Grupos com vários contêineres são atualmente restritos a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Configurar o arquivo YAML

Para implantar um grupo de vários contêineres com o comando [az container create][az-container-create] na CLI do Azure, você precisa especificar a configuração do grupo de contêineres em um arquivo YAML e passar o arquivo YAML como um parâmetro para o comando.

Comece copiando o YAML a seguir em um novo arquivo chamado **deploy-aci.yaml**.

Esse arquivo YAML define um grupo de contêineres nomeado "myContainerGroup" com dois contêineres, um endereço IP público e duas portas expostas. Os contêineres são implantados de imagens públicas do Microsoft. O primeiro contêiner no grupo executa um aplicativo Web voltado para a Internet. O segundo contêiner, o secundário, faz periodicamente solicitações HTTP para o aplicativo Web em execução no primeiro contêiner por meio da rede local do grupo de contêiner.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Implantar o grupo de contêineres

Crie um grupo de recursos com o comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o grupo de contêineres com o comando [az container create][az-container-create], passando o arquivo YAML como argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o comando [az container show][az-container-show] a seguir:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se quiser exibir o aplicativo em execução, navegue até o endereço IP dele em seu navegador. Por exemplo, o IP é `52.168.26.124` nesta saída de exemplo:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Exibir logs

Veja a saída de log de um contêiner usando o comando [az container logs][az-container-logs]. O argumento `--container-name` especifica o contêiner do qual efetuar pull dos logs. Neste exemplo, é especificado o primeiro contêiner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os logs do contêiner secundário, execute o mesmo comando, especificando o nome do segundo contêiner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Como você pode ver, o secundário está periodicamente fazendo uma solicitação HTTP ao aplicativo Web principal por meio da rede local do grupo a fim de garantir que ele esteja em execução. Este exemplo secundário pode ser expandido para disparar um alerta, caso receba um código de resposta HTTP diferente de 200 OK.

## <a name="deploy-from-private-registry"></a>Implantar a partir de registro privado

Para usar um registro de imagem de contêiner privado, inclua o seguinte YAML com valores modificados para seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Por exemplo, o seguinte YAML implanta um grupo de contêineres com um único contêiner cuja imagem é puxada de um Registro de Contêiner do Azure privado chamado "myregistry":

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Exportar o grupo de contêineres para YAML

Você pode exportar a configuração de um grupo de contêineres existente para um arquivo YAML usando o comando da CLI do Azure [az container export][az-container-export].

Útil para preservar a configuração do grupo de contêineres, a exportação permite que você armazene suas configurações de grupo de contêineres no controle de versão para "configuração como código". Ou então, usar o arquivo exportado como ponto de partida ao desenvolver uma nova configuração em YAML.

Exporte a configuração do grupo de contêiner que você criou anteriormente emitindo o seguinte comando [az container export][az-container-export]:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Nenhuma saída será exibida se o comando for bem-sucedido, mas você pode exibir o conteúdo do arquivo para ver o resultado. Por exemplo, as primeiras linhas com `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
```

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou as etapas necessárias para implantar uma instância de contêiner do Azure de vários contêineres. Para uma experiência de ponta a ponta de Instâncias de Contêiner do Azure, incluindo o uso de um registro de contêiner privado do Azure, consulte o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
