---
title: "Criar o primeiro contêiner de Instâncias de Contêiner do Azure | Azure Docs"
description: "Implantar e começar a trabalhar com as Instâncias de Contêiner do Azure"
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Criar o primeiro contêiner nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure facilitam a criação e o gerenciamento de contêineres no Azure. Neste início rápido, você criará um contêiner no Azure e o exporá à Internet com um endereço IP público. Essa operação é concluída com um único comando. Em poucos segundos, você verá o seguinte em seu navegador:

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI das Instâncias de Contêiner do Azure atualmente só estão disponíveis no Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As Instâncias de Contêiner do Azure são recursos do Azure e devem ser colocadas em um grupo de recursos do Azure, um conjunto lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contêiner

Você pode criar um contêiner fornecendo um nome, uma imagem do Docker e um grupo de recursos do Azure. Opcionalmente, você pode expor o contêiner à Internet com um endereço IP público. Nesse caso, vamos usar um contêiner que hospeda um aplicativo Web muito simples escrito em [Node.js](http://nodejs.org).

Os comandos da CLI das Instâncias de Contêiner do Azure atualmente só estão disponíveis no Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

Em alguns segundos, você deve obter uma resposta à solicitação. Inicialmente, o contêiner fica no estado **Criando**, mas ele deve começar em alguns segundos. Você pode verificar o status usando o comando `show`:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Na parte inferior da saída, você verá o estado de provisionamento do contêiner e seu endereço IP:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Depois que o contêiner muda para o estado **Êxito**, você pode acessá-lo no navegador usando o endereço IP fornecido. 

![Os aplicativos implantados usando Instâncias de Contêiner do Azure são exibidos no navegador][aci-app-browser]

## <a name="pull-the-container-logs"></a>Acessar os logs de contêiner

Você pode acessar os logs para o contêiner criado usando o comando `logs`:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Saída:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Excluir o contêiner

Quando você conclui o contêiner, pode removê-lo usando o comando `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Todo o código para o contêiner usado neste início rápido está disponível [no GitHub][app-github-repo], juntamente com seu Dockerfile. Se você quiser tentar criá-lo e implantá-lo nas Instâncias de Contêiner do Azure usando o Registro de Contêiner do Azure, prossiga para o tutorial sobre Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutoriais sobre Instâncias de Contêiner do Azure](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
