---
title: Usar o Draft com AKS e Registro de Contêiner do Azure
description: Usar o Draft com AKS e Registro de Contêiner do Azure
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: 15fbf254eb479f0935e154806795ebd00cff6adf
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345940"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Usar Rascunho com o AKS (Serviço de Kubernetes do Azure)

Draf é uma ferramenta de software livre que ajuda a empacotar e implantar contêineres de aplicativos em um cluster do Kubernetes, deixando você livre para concentrar-se no ciclo de desenvolvimento – o "loop interno" do desenvolvimento concentrado. O Rascunho funciona como o código está sendo desenvolvido, mas antes de comprometer-se com o controle de versão. Com o Rascunho, você pode reimplantar rapidamente um aplicativo no Kubernetes quando ocorrem alterações de código. Para obter mais informações sobre o Rascunho, consulte a [Documentação do Draft no Gitub][draft-documentation].

Este artigo mostra como usar o Rascunho com um cluster do Kubernetes no AKS.

## <a name="prerequisites"></a>Pré-requisitos

As etapas detalhadas neste artigo assumem que você criou um cluster do AKS e estabeleceu uma conexão `kubectl` com o cluster. Se você precisar desses itens, consulte o [Início rápido do AKS][aks-quickstart].

É necessário ter um registro privado do Docker no ACR (Registro de Contêiner do Azure). Para obter instruções sobre como criar uma instância do ACR, consulte o início rápido do [Registro de Contêiner do Azure][acr-quickstart].

Helm também deve ser instalado em seu cluster AKS. Para obter mais informações sobre como instalar e configurar o Helm, consulte [Usar Helm com o AKS (Serviço de Kubernetes do Azure)][aks-helm].

Finalmente, você deve instalar o [Docker](https://www.docker.com).

## <a name="install-draft"></a>Instalar o Draft

A CLI de Rascunho é um cliente que executa no sistema de desenvolvimento e permite implantar o código em um cluster do Kubernetes. Para instalar a CLI de Rascunho em um Mac, use `brew`. Para obter opções adicionais de instalação, consulte o [Guia de instalação do Rascunho][draft-documentation].

> [!NOTE]
> Se você instalou o Rascunho antes da versão 0.12, primeiro exclua o Rascunho do cluster usando `helm delete --purge draft` e, em seguida, remova a configuração local, executando `rm -rf ~/.draft`. Se estiver no MacOS, execute `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Agora, inicialize o Rascunho com o `draft init`:

```console
draft init
```

## <a name="configure-draft"></a>Configurar o Draft

O Draft compila as imagens de contêiner localmente e implanta-as a partir do registro local (como Minikube) ou usa um registro de imagem que você especificar. Este artigo usa o ACR (Registro de Contêiner do Azure), portanto, é necessário estabelecer uma relação de confiança entre o cluster do AKS e o registro do ACR e, em seguida, configurar o Draft para enviar as imagens de contêiner ao ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Criar confiança entre o cluster do AKS e o ACR

Para estabelecer confiança entre um cluster do AKS e um registro do ACR, conceda permissões à entidade de serviço do Azure Active Directory usado pelo cluster do AKS para acessar o registro do ACR. Nos comandos a seguir, forneça seu próprio `<resourceGroupName>`, substitua `<aksName>`pelo nome do cluster do AKS e substitua `<acrName>` pelo nome do registro do ACR:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Para obter mais informações sobre essas etapas para acessar o ACR, consulte [autenticar com ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurar o Draft para enviar e implantar a partir do ACR

Agora que há uma relação de confiança entre o AKS e o ACR, habilite o uso do ACR no cluster do AKS.

1. Defina o valor de *registro* da configuração do Draft. Nos comandos a seguir, substitua `<acrName>` pelo nome do registro do ACR:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Faça logon no registro do ACR com [az acr login][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Como uma relação de confiança foi criada entre o AKS e o ACR, senhas ou segredos não são necessários para efetuar push ou efetuar pull do registro do ACR. A autenticação ocorre no nível do Microsoft Azure Resource Manager, usando o Azure Active Directory.

## <a name="run-an-application"></a>Executar um aplicativo

Para ver o Draft em ação, vamos implantar um aplicativo de exemplo do [repositório do Draft][draft-repo]. Primeiro, clone o repositório:

```console
git clone https://github.com/Azure/draft
```

Altere para o diretório de exemplos Java:

```console
cd draft/examples/example-java/
```

Use o comando `draft create` para iniciar o processo. Este comando cria os artefatos que são usados para executar o aplicativo em um cluster do Kubernetes. Esses itens incluem um Dockerfile, um gráfico de Helm e um arquivo *draft.toml*, que é o arquivo de configuração do Draft.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Para executar o aplicativo de exemplo no cluster do AKS, use o comando `draft up`. Esse comando compila o Dockerfile para criar uma imagem de contêiner, envia a imagem para o ACR e finalmente instala o gráfico Helm para iniciar o aplicativo no AKS.

Na primeira vez em que esse comando for executado, efetuar push e efetuar pull da imagem de contêiner pode demorar algum tempo. Depois que as camadas de base são armazenadas em cache, o tempo gasto para implantar o aplicativo é drasticamente reduzido.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Se você enfrentar problemas ao efetuar push da imagem do Docker, certifique-se de ter efetuado logon com êxito no registro do ACR com [az acr login][az-acr-login] e, em seguida, tente o comando `draft up` novamente.

## <a name="test-the-application-locally"></a>Testar o aplicativo localmente

Para testar o aplicativo, use o comando `draft connect`. Esse comando gera proxy de uma conexão segura com o pod do Kubernetes. Ao concluir, o aplicativo pode ser acessado na URL fornecida.

> [!NOTE]
> Pode demorar alguns minutos para que a imagem de contêiner seja baixada e o aplicativo iniciar. Se você receber um erro ao acessar o aplicativo, tente novamente a conexão.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Para acessar o aplicativo, abra um navegador da Web para o endereço e a porta especificados na saída `draft connect` como *http://localhost:49804*. 

![Aplicativo Java de exemplo em execução com Draft](media/kubernetes-draft/sample-app.png)

Use `Control+C` para interromper a conexão proxy.

> [!NOTE]
> Também é possível usar o comando `draft up --auto-connect` para compilar e implantar o aplicativo e, em seguida, conectar imediatamente ao primeiro contêiner em execução.

## <a name="access-the-application-on-the-internet"></a>Acessar o aplicativo na internet

A etapa anterior criou uma conexão proxy para o pod do aplicativo no cluster AKS. Na medida em que você desenvolve e testa o aplicativo, convém disponibilizar o aplicativo na Internet. Para expor um aplicativo na Internet, crie um serviço do Kubernetes com um tipo de [LoadBalancer][kubernetes-service-loadbalancer] ou crie um [controlador de entrada][kubernetes-ingress]. Vamos criar um serviço *LoadBalancer*.

Primeiro, atualize o pacote de Draft *values.yaml* para especificar que um serviço com um tipo *LoadBalancer* deve ser criado:

```console
vi charts/java/values.yaml
```

Localize a propriedade *service.type* e atualize o valor do *ClusterIP* para *LoadBalancer*, conforme mostrado no exemplo condensado a seguir:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Salve e feche o arquivo e use `draft up` para executar novamente o aplicativo:

```console
draft up
```

Demora alguns minutos para o serviço retornar um endereço IP público. Para monitorar o andamento, use o comando `kubectl get service` com o parâmetro *watch*:

```console
kubectl get service --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço aparece como *pendente*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Depois que o endereço EXTERNAL-IP for alterado de *pendente* para um endereço IP, use `Control+C` para interromper o processo watch `kubectl`:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Para ver o aplicativo, navegue até o endereço IP externo do balanceador de carga com `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterar no aplicativo

Agora que o Draft foi configurado e o aplicativo está em execução no Kubernetes, você está pronto para a iteração de código. Sempre que quiser testar o código atualizado, execute o comando `draft up` para atualizar o aplicativo em execução.

Neste exemplo, atualize o aplicativo de exemplo Java para alterar o texto de exibição. Abra o arquivo *Hello.java*:

```console
vi src/main/java/helloworld/Hello.java
```

Atualize o texto de saída para exibir, *Olá Mundo, sou Java em AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Execute o comando `draft up` para reimplementar o aplicativo:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Para ver o aplicativo atualizado, execute ondulação do endereço IP do balanceador de carga novamente:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o uso do Draft, consulte a documentação do Draft no GitHub.

> [!div class="nextstepaction"]
> [Documentação do Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
