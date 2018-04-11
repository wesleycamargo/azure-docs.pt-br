---
title: Usar o Rascunho com AKS e Registro de Contêiner do Azure
description: Usar o Rascunho com AKS e Registro de Contêiner do Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2ab79e3a6308d01d836a82f356f43eccb6af9791
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="use-draft-with-azure-container-service-aks"></a>Usar o Rascunho com o Serviço de Contêiner do Azure (AKS)

Rascunho é uma ferramenta de software livre que ajuda a conter e implantar esses contêineres em um cluster do Kubernetes, deixando-o livre para concentrar-se no ciclo de desenvolvimento - o "loop interno" do desenvolvimento concentrado. O Rascunho funciona como o código está sendo desenvolvido, mas antes de comprometer-se com o controle de versão. Com o Rascunho, você pode reimplantar rapidamente um aplicativo no Kubernetes quando ocorrem alterações de código. Para obter mais informações sobre o Rascunho, consulte a [Documentação do Rascunho no Gitub][draft-documentation].

Este documento detalha o uso do Rascunho com um cluster do Kubernetes no AKS.

## <a name="prerequisites"></a>pré-requisitos

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, consulte o [Início rápido do AKS][aks-quickstart].

Você também precisa de um Registro do Docker privado no ACR (Registro de Contêiner do Azure). Para obter instruções sobre como implantar uma instância do ACR, consulte o [Início rápido do Registro de Contêiner do Azure][acr-quickstart].

Helm também deve ser instalado em seu cluster AKS. Para obter mais informações sobre como instalar o comando, consulte [Usar o Helm com o serviço de contêiner do Azure (AKS)][aks-helm].

Finalmente, você deve instalar o [Docker](https://www.docker.com).

## <a name="install-draft"></a>Instalar o Rascunho

A CLI do Rascunho é um cliente executado no sistema de desenvolvimento e que permite que você implante o código rapidamente em um cluster do Kubernetes. 

> [!NOTE] 
> Se você instalou o Rascunho antes da versão 0.12, primeiro exclua o Rascunho do cluster usando `helm delete --purge draft` e, em seguida, remova a configuração local executando `rm -rf ~/.draft`. Se estiver no MacOS, poderá executar `brew upgrade draft`.

Para instalar a CLI do Rascunho em um Mac, use `brew`. Para obter opções adicionais de instalação, consulte [Guia de instalação do Rascunho][install-draft].

```console
brew tap azure/draft
brew install draft
```

Agora inicialize o Rascunho com o comando `draft init`.

```console
draft init
```

## <a name="configure-draft"></a>Configurar o Rascunho

O Rascunho compila as imagens de contêiner localmente e, em seguida, implanta-as a partir do registro local (no caso do Minikube), ou você deverá especificar o registro de imagem a ser utilizado. Esse exemplo usa o ACR (Registro de Contêiner do Azure), portanto, é necessário estabelecer uma relação de confiança entre o cluster do AKS e o registro do ACR e configurar o Rascunho para enviar o contêiner ao ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Criar confiança entre o cluster do AKS e o ACR

Para estabelecer confiança entre um cluster do AKS e um registro do ACR, você modifica a Entidade de Serviço do Azure Active Directory usado com o AKS, adicionando a função de Colaborador a ela com o escopo do repositório do ACR. Para fazer isso, execute os comandos a seguir, substituindo _&lt;aks-rg-name&gt;_ e _&lt;aks-cluster-name&gt;_ com o grupo de recursos e o nome do cluster do AKS e _&lt;acr-rg-nam&gt;_ e _&lt;acr-repo-name&gt;_ com o grupo de recursos e o nome do repositório do repositório do ACR com o qual você deseja criar confiança.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Estas etapas e outros mecanismos de autenticação para acessar o ACR estão em [autenticando com ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurar o Rascunho para enviar e implantar a partir do ACR

Agora que há uma relação de confiança entre o AKS e o ACR, as etapas a seguir permitem o uso do ACR no cluster do AKS.
1. Defina o valor da configuração de rascunho `registry` executando `draft config set registry <registry name>.azurecr.io`, onde o _&lt;nome do registro&lt;_ é o nome do registro do ACR.
2. Faça logon no registro do ACR executando `az acr login -n <registry name>`. 

Como agora você está conectado localmente ao ACR e criou uma relação de confiança com o AKS e o ACR, nenhuma senha ou segredo será necessário ao efetuar push para ou efetuar pull do ACR para o AKS. A autenticação ocorre no nível do Microsoft Azure Resource Manager, usando o Azure Active Directory. 

## <a name="run-an-application"></a>Executar um aplicativo

O repositório do Rascunho inclui vários aplicativos de exemplo que podem ser usados para a demonstração do Rascunho. Crie uma cópia clonada do repositório.

```console
git clone https://github.com/Azure/draft
```

Mude para o diretório de exemplos de Java.

```console
cd draft/examples/example-java/
```

Use o comando `draft create` para iniciar o processo. Este comando cria os artefatos que são usados para executar o aplicativo em um cluster do Kubernetes. Esses itens incluem um Dockerfile, um gráfico do Helm e um arquivo `draft.toml`, que é o arquivo de configuração do Rascunho.

```console
draft create
```

Saída:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Para executar o aplicativo em um cluster do Kubernetes, use o comando `draft up`. Esse comando compila o Dockerfile para criar uma imagem de contêiner, envia a imagem para o ACR e finalmente instala o gráfico Helm para iniciar o aplicativo no AKS.

Na primeira vez que isso executar, ao efetuar push e efetuar pull da imagem de container poderá levar algum tempo. Depois que as camadas de base forem armazenadas em cache, o tempo gasto será drasticamente reduzido.

```console
draft up
```

Saída:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Testar o aplicativo

Para testar o aplicativo, use o comando `draft connect`. Esse comando gera o proxy de uma conexão com o pod do Kubernetes, permitindo uma conexão local segura. Ao concluir, o aplicativo pode ser acessado na URL fornecida.

Em alguns casos, pode levar alguns minutos para que a imagem de contêiner seja baixada e o aplicativo seja iniciado. Se você receber um erro ao acessar o aplicativo, tente novamente a conexão.

```console
draft connect
```

Saída:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Agora você pode testar o aplicativo, navegando até http://localhost:46143 (para o exemplo anterior, sua porta pode ser diferente). Ao concluir o teste do aplicativo, use `Control+C` para interromper a conexão de proxy.

> [!NOTE]
> Também é possível usar o comando `draft up --auto-connect` para criar e implantar o aplicativo e conectar-se imediatamente ao primeiro contêiner em execução para tornar o ciclo de iteração ainda mais rápido.

## <a name="expose-application"></a>Expor aplicativo

Ao testar um aplicativo no Kubernetes, talvez você queira disponibilizar o aplicativo na Internet. Isso pode ser feito usando um serviço do Kubernetes com um tipo de [LoadBalancer][kubernetes-service-loadbalancer] ou um [controlador entrada][kubernetes-ingress]. Este documento detalha o uso de um serviço do Kubernetes.


Primeiro, o pacote do Rascunho precisa ser atualizado para especificar que um serviço com um tipo `LoadBalancer` deve ser criado. Para fazer isso, atualize o tipo de serviço no arquivo `values.yaml`.

```console
vi charts/java/values.yaml
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
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Depois que o endereço EXTERNAL-IP for alterado de `pending` para um `IP address`, use `Control+C` para interromper o processo de inspeção do kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
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
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Execute o comando `draft up --auto-connect` para reimplantar o aplicativo assim que um pod estiver pronto para responder.

```console
draft up --auto-connect
```

Saída

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Por fim, exiba o aplicativo para ver as atualizações.

```console
curl 52.175.224.118
```

Saída:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o uso do Rascunho, consulte a documentação do Rascunho no GitHub.

> [!div class="nextstepaction"]
> [Documentação do Rascunho][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
