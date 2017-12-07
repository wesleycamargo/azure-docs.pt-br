---
title: "Usar o Rascunho com AKS e Registro de Contêiner do Azure"
description: "Usar o Rascunho com AKS e Registro de Contêiner do Azure"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df5614d8a708b49ee1368c4d7983f45d29920fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Usar o Rascunho com o Serviço de Contêiner do Azure (AKS)

O Rascunho é uma ferramenta de software livre que ajuda a compactar e executar o código em um cluster Kubernetes. O Rascunho é direcionado ao ciclo de iteração de desenvolvimento, conforme o código está sendo desenvolvido, mas antes de confirmar no controle de versão. Com o Rascunho, você pode reimplantar rapidamente um aplicativo no Kubernetes quando ocorrem alterações de código. Para obter mais informações sobre o Rascunho, consulte a [Documentação do Rascunho no Gitub](https://github.com/Azure/draft/tree/master/docs).

Este documento detalha o uso do Rascunho com um cluster do Kubernetes no AKS.

## <a name="prerequisites"></a>Pré-requisitos

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, veja o [Início rápido do AKS](./kubernetes-walkthrough.md).

Você também precisa de um Registro do Docker privado no ACR (Registro de Contêiner do Azure). Para obter instruções sobre como implantar uma instância do ACR, consulte o [Início rápido do Registro de Contêiner do Azure](../container-registry/container-registry-get-started-azure-cli.md).

## <a name="install-helm"></a>Instalar o Helm

A CLI do Helm é um cliente executado em seu sistema de desenvolvimento e permite que você inicie, pare e gerencie aplicativos com gráficos Helm.

Para instalar a CLI do Helm em um Mac, use `brew`. Para obter opções adicionais de instalação, consulte [Instalando o Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md).

```console
brew install kubernetes-helm
```

Saída:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Instalar o Rascunho

A CLI do Rascunho é um cliente executado no sistema de desenvolvimento e que permite que você implante o código rapidamente em um cluster do Kubernetes.

Para instalar a CLI do Rascunho em um Mac, use `brew`. Para obter opções adicionais de instalação, consulte [Guia de instalação do Rascunho](https://github.com/Azure/draft/blob/master/docs/install.md).

```console
brew install draft
```

Saída:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Configurar o Rascunho

Ao configurar o Rascunho, um Registro de contêiner deve ser especificado. Nesse exemplo, é usado o Registro de Contêiner do Azure.

Execute o seguinte comando para obter o nome e o nome do servidor de logon da instância do ACR. Atualize o comando com o nome do grupo de recursos que contém sua instância do ACR.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

A senha de instância do ACR também é necessária.

Execute o comando a seguir para retornar a senha do ACR. Atualize o comando com o nome da instância do ACR.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Inicializar o Rascunho com o comando `draft init`.

```console
draft init
```

Durante esse processo, serão solicitadas as credenciais de Registro de contêiner. Ao usar um Registro de Contêiner do Azure, a URL do Registro é o nome do servidor de logon do ACR, o nome de usuário é o nome da instância do ACR e a senha é a senha do ACR.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Uma vez concluído, o Rascunho está configurado no cluster do Kubernetes e está pronto para uso.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Executar um aplicativo

O repositório do Rascunho inclui vários aplicativos de exemplo que podem ser usados para a demonstração do Rascunho. Crie uma cópia clonada do repositório.

```console
git clone https://github.com/Azure/draft
```

Mude para o diretório de exemplos de Java.

```console
cd draft/examples/java/
```

Use o comando `draft create` para iniciar o processo. Este comando cria os artefatos que são usados para executar o aplicativo em um cluster do Kubernetes. Esses itens incluem um Dockerfile, um gráfico do Helm e um arquivo `draft.toml`, que é o arquivo de configuração do Rascunho.

```console
draft create
```

Saída:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Para executar o aplicativo em um cluster do Kubernetes, use o comando `draft up`. Esse comando carrega os arquivos de configuração e código do aplicativo para o cluster do Kubernetes. Em seguida, executa o Dockerfile para criar uma imagem de contêiner, envia por push a imagem para o Registro de contêiner e finalmente executa o gráfico do Helm para iniciar o aplicativo.

```console
draft up
```

Saída:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Testar o aplicativo

Para testar o aplicativo, use o comando `draft connect`. Esse comando gera o proxy de uma conexão com o pod do Kubernetes, permitindo uma conexão local segura. Ao concluir, o aplicativo pode ser acessado na URL fornecida.

Em alguns casos, pode levar alguns minutos para que a imagem de contêiner seja baixada e o aplicativo seja iniciado. Se você receber um erro ao acessar o aplicativo, tente novamente a conexão.

```console
draft connect
```

Saída:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Ao concluir o teste do aplicativo, use `Control+C` para interromper a conexão de proxy.

## <a name="expose-application"></a>Expor aplicativo

Ao testar um aplicativo no Kubernetes, talvez você queira disponibilizar o aplicativo na Internet. Isso pode ser feito usando um serviço do Kubernetes com um tipo de [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) ou um [controlador entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/). Este documento detalha o uso de um serviço do Kubernetes.


Primeiro, o pacote do Rascunho precisa ser atualizado para especificar que um serviço com um tipo `LoadBalancer` deve ser criado. Para fazer isso, atualize o tipo de serviço no arquivo `values.yaml`.

```console
vi chart/java/values.yaml
```

Localize a propriedade `service.type` e atualize o valor de `ClusterIP` para `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Execute `draft up` para executar o aplicativo novamente.

```console
draft up
```

Pode levar alguns minutos para que o Serviço retorne um endereço IP público. Para monitorar o progresso, use o comando `kubectl get service` com uma inspeção.

```console
kubectl get service -w
```

Inicialmente, o *EXTERNAL-IP* do serviço aparece como `pending`.

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Depois que o endereço EXTERNAL-IP for alterado de `pending` para um `IP address`, use `Control+C` para interromper o processo de inspeção do kubectl.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Para consultar o aplicativo, navegue até o endereço IP externo.

```console
curl 52.175.224.118
```

Saída:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterar no aplicativo

Agora que o Rascunho foi configurado e o aplicativo está em execução no Kubernetes, você está pronto para a iteração de código. Cada vez que desejar testar o código atualizado, execute o comando `draft up` para atualizar o aplicativo em execução.

Neste exemplo, atualize o aplicativo Olá, Mundo Java.

```console
vi src/main/java/helloworld/Hello.java
```

Atualize o texto Olá, Mundo.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Execute o comando `draft up` para reimplantar o aplicativo.

```console
draft up
```

Saída

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Por fim, exiba o aplicativo para ver as atualizações.

```console
curl 52.175.224.118
```

Saída:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o uso do Rascunho, consulte a documentação do Rascunho no GitHub.

> [!div class="nextstepaction"]
> [Documentação do Rascunho](https://github.com/Azure/draft/tree/master/docs)