---
title: "Usar Rascunho com o Serviço de Contêiner do Azure e Registro de Contêiner do Azure | Microsoft Docs"
description: "Crie um cluster Kubernetes ACS e um Registro de Contêiner do Azure para criar seu primeiro aplicativo no Azure com o Rascunho."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, Contêineres, microsserviços, Kubernetes, Rascunho, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: b70d2340c0f1286fa355a78a4cd0cb1ce37cbc39
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Use o Rascunho com o Serviço de Contêiner do Azure e o Registro de Contêiner do Azure para criar e implantar um aplicativo no Kubernetes

[Rascunho](https://aka.ms/draft) é uma nova ferramenta de software livre que facilita o desenvolvimento de aplicativos baseados em contêiner e a implantação em clusters Kubernetes sem saber muito sobre Docker e Kubernetes, ou até mesmo sem instalá-los. O uso de ferramentas como o Rascunho permite que você e sua equipe se concentrem na criação do aplicativo com Kubernetes sem prestar muita atenção à infraestrutura.

Você pode usar o Rascunho com qualquer registro de imagem do Docker e qualquer cluster Kubernetes, incluindo os locais. Este tutorial mostra como usar o ACS com Kubernetes, ACR e DNS do Azure para criar um pipeline de desenvolvedor CI/CD ativo usando o Rascunho.


## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure
Você pode [criar um novo Registro de Contêiner do Azure](../../container-registry/container-registry-get-started-azure-cli.md) facilmente, mas as etapas são as seguintes:

1. Crie um grupo de recursos do Azure para gerenciar o registro ACR e o cluster Kubernetes no ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Criar um registro de imagem ACR usando [az acr create](/cli/azure/acr#create)
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Criar um Serviço de Contêiner do Azure com Kubernetes

Agora você está pronto para usar [az acs create](/cli/azure/acs#create) a fim de criar um cluster ACS usando Kubernetes como o valor `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Como Kubernetes não é o tipo de orquestrador padrão, use a opção `--orchestrator-type kubernetes`.

A saída, quando bem-sucedida, fica mais ou menos assim.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Agora que você tem um cluster, pode importar as credenciais usando o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Agora você tem um arquivo de configuração local para seu cluster, que é que o Helm e o Rascunho precisam para concluir o trabalho.

## <a name="install-and-configure-draft"></a>Instalar e configurar o Rascunho
As instruções de instalação do Rascunho estão no [repositório do Rascunho](https://github.com/Azure/draft/blob/master/docs/install.md). Elas são relativamente simples, mas exigem alguma configuração, pois ela depende do [Helm](https://aka.ms/helm) para criar e implantar um gráfico Helm no cluster Kubernetes.

1. [Baixe e instale o Helm](https://aka.ms/helm#install).
2. Use o Helm para procurar e instalar `stable/traefik` e o controlador de entrada para permitir solicitações de entrada para as builds.
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Agora, defina um observador no controlador `ingress` para capturar o valor de IP externo quando for implantado. Esse endereço IP será o que é [mapeado para seu domínio de implantação](#wire-up-deployment-domain) na próxima seção.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    Neste caso, o IP externo para o domínio de implantação é `13.64.108.240`. Agora, você pode mapear seu domínio para esse IP.

## <a name="wire-up-deployment-domain"></a>Configurar domínio de implantação

O Rascunho cria uma versão para cada gráfico do Helm criado, ou seja, cada aplicativo em que você está trabalhando. Cada um obtém um nome gerado que é usado pelo Rascunho como um _subdomínio_ acima do _domínio de implantação_ raiz que você controla. (Neste exemplo, usamos `squillace.io` como o domínio de implantação.) Para habilitar esse comportamento de subdomínio, você deve criar um registro a para `'*'` nas entradas DNS do seu domínio de implantação, para que cada subdomínio gerado seja roteado para o controlador de entrada do cluster Kubernetes.

Seu próprio provedor de domínio tem sua própria maneira de atribuir servidores DNS: para [delegar nameservers para seu domínio DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md), execute as seguintes etapas:

1. Crie um grupo de recursos para a zona.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Crie uma zona DNS para seu domínio.
Use o comando [az network dns zone create](/cli/azure/network/dns/zone#create) para obter os nameservers e delegar o controle DNS para o DNS do Azure em relação ao seu domínio.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Adicione os servidores DNS recebidos ao provedor de domínio do seu domínio de implantação, o que permite que você use o DNS do Azure para realinhar seu domínio da maneira desejada.
4. Crie uma entrada de conjunto de registros A para o mapeamento do domínio de implantação para o IP `ingress` da etapa 2 da seção anterior.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
A saída se parece com:
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Configure o Rascunho para usar seu registro e crie subdomínios para cada gráfico do Helm criado. Para configurar o Rascunho, você precisará:
  - do nome do Registro de Contêiner do Azure (neste exemplo, `draft`)
  - da chave do registro, ou senha, de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.
  - do domínio raiz da implantação que você configurou para mapear para o endereço IP externo de entrada do Kubernetes (aqui, `squillace.io`)

  Chame `draft init` e o processo de configuração solicitará os valores acima. O processo ficará mais ou menos assim na primeira vez que você executá-lo.
 ```bash
    $ draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

Agora você está pronto para implantar um aplicativo.


## <a name="build-and-deploy-an-application"></a>Criar e implantar um aplicativo

No repositório do Rascunho, existem [seis aplicativos de exemplo simples](https://github.com/Azure/draft/tree/master/examples). Clone o repositório e vamos usar o [exemplo do Python](https://github.com/Azure/draft/tree/master/examples/python). Mude para o diretório de exemplos/Python e digite `draft create` para compilar o aplicativo. Ele deve ficar parecido com o exemplo a seguir.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

A saída inclui um Dockerfile e um gráfico do Helm. Para criar e implantar, basta digitar `draft up`. A saída é abrangente, mas começa como o exemplo a seguir.
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

e quando bem-sucedida, termina com algo semelhante ao exemplo a seguir.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Seja qual for o nome do gráfico, agora você pode `curl http://gangly-bronco.squillace.io` para receber a resposta `Hello World!`.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um cluster ACS Kubernetes, pode investigar o uso do [Registro de Contêiner do Azure](../../container-registry/container-registry-intro.md) para criar outras implantações diferentes deste cenário. Por exemplo, você pode criar um conjunto de registro DNS de domínio de rascunho _basedomain.toplevel_ que controla coisas de um subdomínio mais profundo para implantações específicas do ACS.







