---
title: Usar o OpenFaaS com o AKS (Serviço de Contêiner do Azure)
description: Implantar e usar o OpenFaaS com o AKS (Serviço de Contêiner do Azure)
services: container-service
author: justindavies
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 06706450d8af6f571f002789815290f75da9623d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="using-openfaas-on-aks"></a>Usando o OpenFaaS no AKS

O [OpenFaaS][open-faas] é uma estrutura para a criação de funções sem servidor em contêineres. Como um projeto de Software Livre, ele conquistou adoção em larga escala dentro da comunidade. Este documento fornece detalhes sobre como instalar e usar o OpenFaas em um cluster do AKS (Serviço de Contêiner do Azure).

## <a name="prerequisites"></a>pré-requisitos

Para concluir as etapas deste artigo, você precisa dos itens a seguir.

* Noções básicas sobre o Kubernetes.
* Um cluster do AKS (Serviço de Contêiner do Azure) e credenciais do AKS configuradas no sistema de desenvolvimento.
* CLI do Azure instalada no sistema de desenvolvimento.
* Ferramentas de linha de comando do Git instaladas no sistema.

## <a name="get-openfaas"></a>Obter o OpenFaaS

Clone o repositório do projeto do OpenFaaS no sistema de desenvolvimento.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Altere-o para o diretório do repositório clonado.

```azurecli-interactive
cd faas-netes 
```

## <a name="deploy-openfaas"></a>Implantar o OpenFaaS

Como uma boa prática, o OpenFaaS e as funções do OpenFaaS devem ser armazenados em seu próprio namespace do Kubernetes.

Crie um namespace para o sistema OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Crie um segundo namespace para as funções do OpenFaaS.

```azurecli-interactive 
kubectl create namespace openfaas-fn
```

Um gráfico do Helm para o OpenFaaS está incluído no repositório clonado. Use este gráfico para implantar o OpenFaaS no cluster do AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/ 
```

Saída:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Um endereço IP público é criado para acessar o gateway OpenFaaS. Para recuperar esse endereço IP, use o comando [kubectl get service][kubectl-get]. Pode levar um minuto até que o endereço IP seja atribuído ao serviço.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Saída. 

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para testar o sistema OpenFaaS, navegue para o endereço IP externo na porta 8080, `http://52.186.64.52:8080`, neste exemplo.

![Interface do usuário do OpenFaaS](media/container-service-serverless/openfaas.png)

Por fim, instale a CLI do OpenFaaS. Este exemplo usou o Brew; consulte a [documentação da CLI do OpenFaaS][open-faas-cli] para obter mais opções.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Criar a primeira função

Agora que o OpenFaaS está funcionando, crie uma função usando o portal do OpenFaas.

Clique em **Implantar Nova Função** e pesquise **Figlet**. Selecione a função do Figlet e clique em **Implantar**.

![Figlet](media/container-service-serverless/figlet.png)

Use o Curl para invocar a função. Substitua o endereço IP no exemplo a seguir pelo endereço IP do gateway do OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Saída:

```console
 _   _      _ _            _                        
| | | | ___| | | ___      / \    _____   _ _ __ ___ 
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Criar a segunda função

Agora crie uma segunda função. Este exemplo será implantado por meio da CLI do OpenFaaS e da recuperação de dados de um Cosmos DB e inclui uma imagem de contêiner personalizada. Vários itens precisam ser configurados antes da criação da função. 

Primeiro, crie um novo grupo de recursos para o Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implante uma instância do CosmosDB do tipo `MongoDB`. A instância precisa de um nome exclusivo. Portanto, atualize `openfaas-cosmos` para algo exclusivo no ambiente. 

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obtenha a cadeia de conexão de banco de dados do Cosmos e armazene-a em uma variável. 

Atualize o valor para o argumento `--resource-group` para o nome do grupo de recursos e o argumento `--name` para o nome do Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Agora, popule o Cosmos DB com os dados de teste. Crie um arquivo chamado `plans.json` e copie-o para o JSON a seguir.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Use a ferramenta *mongoimport* para carregar a instância do CosmosDB com os dados. 

Se necessário, instale as ferramentas do MongoDB. O exemplo a seguir instala essas ferramentas por meio do Brew; consulte a [documentação do MongoDB][install-mongo] para obter outras opções.

```azurecli-interactive
brew install mongodb
```

Carregue os dados no banco de dados.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Saída:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Execute o comando a seguir para criar a função. Atualize o valor do argumento `-g` com o endereço do gateway do OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Depois de implantado, você deverá ver o ponto de extremidade do OpenFaaS recém-criado para a função.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Teste a função usando o Curl. Atualize o endereço IP com o endereço do gateway do OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Saída:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Você também pode testar a função na interface do usuário do OpenFaaS.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

# <a name="next-steps"></a>Próximas etapas

A implantação padrão do OpenFaas precisa ser bloqueada para o Gateway e as Funções do OpenFaaS. [Postagem no blog de Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) traz mais detalhes sobre as opções de configuração de segurança. 

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli