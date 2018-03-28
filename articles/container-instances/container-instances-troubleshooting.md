---
title: Solução de problemas de Instâncias de Contêiner do Azure
description: Saiba como solucionar problemas com as Instâncias de Contêiner do Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Solucionar problemas de implantação com as Instâncias de Contêiner do Azure

Este artigo mostra como solucionar problemas ao implantar contêineres para Instâncias de Contêiner do Azure. Ele também descreve alguns dos problemas comuns que você pode encontrar.

## <a name="view-logs-and-stream-output"></a>Exibir os logs e o fluxo de saída

Quando você tiver um contêiner com comportamento inadequado, inicie exibindo logs com [az container logs][az-container-logs]e transmita sua saída padrão e erro padrão com [az container attach][az-container-attach].

### <a name="view-logs"></a>Exibir logs

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

### <a name="attach-output-streams"></a>Anexar fluxos de saída

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

Se o contêiner não implantar com êxito, você deve examinar as informações de diagnóstico fornecidas pelo provedor de recursos das Instâncias de Contêiner do Azure. Para exibir os eventos para o contêiner, execute o comando [az container show][az-container-show]:

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

## <a name="common-deployment-issues"></a>Tarefas de implantação comuns

As seções a seguir descrevem problemas comuns que representam a maioria dos erros na implantação de contêiner:

* [Não há suporte para versão da imagem](#image-version-not-supported)
* [Não é possível efetuar pull da imagem](#unable-to-pull-image)
* [Contêiner sai e reinicia continuamente](#container-continually-exits-and-restarts)
* [Contêiner leva muito tempo para iniciar](#container-takes-a-long-time-to-start)
* [Erro de “Recurso não disponível”](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Não há suporte para versão da imagem

Se você especificar uma imagem, cujas Instâncias de Contêiner do Azure não poderá dar suporte, um erro `ImageVersionNotSupported` será retornado. O valor do erro é `The version of image '{0}' is not supported.` e atualmente se aplica às imagens do Windows 1709. Para atenuar esse problema, use uma imagem do Windows LTS. O suporte para imagens do Windows 1709 está em andamento.

## <a name="unable-to-pull-image"></a>Não é possível efetuar pull da imagem

Se o Instâncias de Contêiner do Azure não for capaz de efetuar pull da imagem inicialmente, ele tentará novamente por um período, até eventualmente falhar. Se não for possível efetuar pull da imagem, eventos como a seguir serão mostrados na saída do comando [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Para resolver, excluir o contêiner e tente novamente realizar a implantação dele, prestando atenção para digitar corretamente o nome da imagem.

## <a name="container-continually-exits-and-restarts"></a>Contêiner sai e reinicia continuamente

Se o contêiner é executado até a conclusão e reinicia automaticamente, talvez seja necessário definir uma [política de reinício](container-instances-restart-policy.md) de **Em caso de Falha** ou **Nunca**. Se você especificar **Em caso de Falha** e ainda continuar sendo reiniciado, pode haver um problema com o aplicativo ou script executado em seu contêiner.

A API de Instâncias de Contêiner inclui uma propriedade `restartCount`. Para verificar o número de reinicializações de um contêiner, você pode usar o comando [az container show][az-container-show] na CLI do Azure 2.0. No seguinte exemplo de saída (que foi truncado para fins de brevidade), você pode ver a propriedade `restartCount` no final da saída.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A maioria das imagens de contêiner para as distribuições de Linux definem um shell (assim como um bash) como o comando padrão. Já que um shell por si só não é um serviço de execução longa, esses contêineres saem imediatamente e entram em um loop de reinicialização quando configurados com a política de reinício padrão **Sempre**.

## <a name="container-takes-a-long-time-to-start"></a>Contêiner leva muito tempo para iniciar

Os dois principais fatores que contribuem para o tempo de inicialização do contêiner em Instâncias de Contêiner do Azure são:

* [Tamanho da imagem](#image-size)
* [Local da imagem](#image-location)

Imagens do Windows têm [considerações adicionais](#use-recent-windows-images).

### <a name="image-size"></a>Tamanho da imagem

Se o contêiner leva muito tempo para iniciar mas eventualmente tem êxito, comece observando o tamanho da sua imagem de contêiner. Já que o Instâncias de Contêiner do Azure efetua pull de sua imagem de contêiner sob demanda, o tempo de inicialização que você experiencia está diretamente relacionado ao tamanho dela.

Você pode exibir o tamanho da sua imagem de contêiner usando o comando `docker images` na CLI do Docker:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que sua imagem final não contenha nada que não deja necessário no tempo de execução. Uma forma de fazer isso é com [builds de vários estágios][docker-multi-stage-builds]. Builds de vários estágios tornam fácil assegurar que a imagem final contenha somente os artefatos necessários para seu aplicativo, sem nenhum conteúdo extra que foi necessário no momento do build.

### <a name="image-location"></a>Local da imagem

Outra maneira de reduzir o impacto do pull da imagem no tempo de inicialização do contêiner é hospedar a imagem de contêiner no [Registro de Contêiner do Azure](/azure/container-registry/) na mesma região em que você pretende implantar as instâncias de contêiner. Isso reduz o caminho de rede que a imagem de contêiner precisa percorrer, reduzindo significativamente o tempo de download.

### <a name="use-recent-windows-images"></a>Usar imagens recentes do Windows

Instâncias de Contêiner do Azure usa um mecanismo de cache para ajudar a acelerar o tempo de inicialização de contêiner para imagens com base em determinadas imagens do Windows.

Para garantir o menor tempo de inicialização do contêiner do Windows, use uma da **três versões mais recentes** das **duas imagens** seguintes como a imagem base:

* [Windows Server 2016][docker-hub-windows-core] (somente LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Erro de recurso não disponível

Devido a diversas cargas de recursos regionais no Azure, você poderá receber o seguinte erro durante a tentativa de implantar uma instância de contêiner:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Esse erro indica que devido à carga pesada na região em que você está tentando implantar, os recursos especificados para o contêiner não poderão ser alocados no momento. Use uma ou mais das seguintes etapas de mitigação para ajudar a resolver o problema.

* Verifique se suas configurações de implantação de contêiner se enquadram nos parâmetros definidos em [Quotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md#region-availability)
* Especificar configurações de CPU e memória inferiores para o contêiner
* Implantar em uma região diferente do Azure
* Implantar em um momento posterior

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show