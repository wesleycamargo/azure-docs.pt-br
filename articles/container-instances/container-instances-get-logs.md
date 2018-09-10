---
title: Obter logs e eventos de contêiner com as Instâncias de Contêiner do Azure
description: Saiba como depurar com logs e eventos de contêiner com as Instâncias de Contêiner do Azure
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/30/18
ms.author: juluk
ms.custom: mvc
ms.openlocfilehash: 4c8197e570c429893084fc1c2f8e4b36fd43a721
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425581"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Recuperar logs e eventos de contêiner nas Instâncias de Contêiner do Azure

Quando você tiver um contêiner com comportamento inadequado, inicie exibindo logs com [az container logs][az-container-logs]e transmita sua saída padrão e erro padrão com [az container attach][az-container-attach].

## <a name="view-logs"></a>Exibir logs

Para exibir logs do seu código de aplicativo em um contêiner, você pode usar o comando [az container logs][az-container-logs].

A seguir está a saída do log do contêiner de exemplo baseado em tarefa em [Executar uma tarefa em contêineres em ACI](container-instances-restart-policy.md), depois de ter alimentado-o uma URL inválida para processar:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

O comando [az container attach][az-container-attach] fornece informações de diagnóstico durante a inicialização do contêiner. Depois que o contêiner for iniciado, ele transmite STDOUT e STDERR para o console local.

Por exemplo, aqui está a saída do contêiner com base em tarefa na [Executar uma tarefa em contêineres em ACI](container-instances-restart-policy.md), depois de ter fornecido a uma URL válida para processar um arquivo de texto grande:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Obtendo eventos de diagnóstico

Se o contêiner não implantar com êxito, você deve examinar as informações de diagnóstico fornecidas pelo provedor de recursos das Instâncias de Contêiner do Azure. Para exibir os eventos do contêiner, execute o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A saída inclui as propriedades principais do contêiner, juntamente com eventos de implantação (exibidos aqui truncados):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Próximas etapas
Saiba como [solucionar problemas de contêiner e implantação](container-instances-troubleshooting.md) nas Instâncias de Contêiner do Azure.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs