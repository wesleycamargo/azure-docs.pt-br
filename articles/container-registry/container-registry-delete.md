---
title: Excluir recursos de imagem no Registro de Contêiner do Azure
description: Detalhes sobre como gerenciar com eficiência o tamanho do registro, excluindo os dados da imagem do contêiner.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.openlocfilehash: f3206da25a3c0727e3f9fe12190580a6c28c81a3
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983244"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Excluir imagens de contêiner no Registro de Contêiner do Azure

Para manter o tamanho do registro do contêiner do Azure, exclua periodicamente os dados da imagem obsoletos. Embora algumas imagens de contêiner implantadas na produção possam exigir armazenamento de longo prazo, outras normalmente podem ser excluídas mais rapidamente. Por exemplo, em um cenário de criação e teste automatizado, o registro pode ser preenchido rapidamente com imagens que talvez nunca sejam implementadas e podem ser eliminadas logo após a conclusão da passagem de teste e de compilação.

Como você pode excluir dados de imagem de várias maneiras diferentes, é importante entender como cada operação de exclusão afeta o uso do armazenamento. Este artigo primeiro apresenta os componentes de um registro do Docker e imagens de contêineres e, em seguida, abrange vários métodos para excluir dados de imagem.

## <a name="registry"></a>Registro

Um registro de *contêiner* é um serviço que armazena e distribui imagens de contêiner. O Docker Hub é um registro de contêiner do Docker público, enquanto o Azure Container Registry fornece registros de contêiner do Docker particulares no Azure.

## <a name="repository"></a>Repositório

Os registros de contêiner gerenciam *repositórios*, coleções de imagens de contêiner com o mesmo nome, mas diferentes tags. Por exemplo, as três imagens a seguir estão no repositório "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Também podem incluir nomes de repositório [namespaces](container-registry-best-practices.md#repository-namespaces). Namespaces permitem agrupar imagens usando nomes de repositório avanço delimitada por barra "/", por exemplo:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Componentes de uma imagem

Uma imagem de contêiner dentro de um registro é associada a uma ou mais tags, possui uma ou mais camadas e é identificada por um manifesto. Compreender como esses componentes se relacionam uns com os outros pode ajudá-lo a determinar o melhor método para liberar espaço no seu registro.

### <a name="tag"></a>Marca

Uma imagem *marca* especifica sua versão. Uma única imagem em um repositório pode receber uma ou várias tags e também pode ser "sem tag". Ou seja, você pode excluir todas as tags de uma imagem, enquanto os dados da imagem (suas camadas) permanecem no registro.

O repositório (ou repositório e namespace) mais uma tag define o nome de uma imagem. Você pode empurrar e puxar uma imagem especificando seu nome na operação push ou pull.

Em um registro privado como o Azure Container Registry, o nome da imagem também inclui o nome completo do host do registro. O host do registro para imagens no ACR está no formato *acrname.azurecr.io*. Por exemplo, o nome completo da primeira imagem no namespace 'marketing' na seção anterior seria:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Para ver uma discussão sobre as práticas recomendadas de marcação de imagens, consulte [Marcação no Docker: Best practices for tagging and versioning docker images][tagging-best-practices] (Marcação no Docker: práticas recomendadas para a marcação e o controle de versão de imagens do Docker) no MSDN.

### <a name="layer"></a>Camada

As imagens são compostas de uma ou mais *camadas*, cada uma correspondendo a uma linha no Dockerfile que define a imagem. As imagens em um registro compartilham camadas comuns, aumentando a eficiência do armazenamento. Por exemplo, várias imagens em repositórios diferentes podem compartilhar a mesma camada base de Linux Alpine, mas apenas uma cópia dessa camada é armazenada no registro.

Também é o compartilhamento de camada otimiza a distribuição de camada para nós com várias camadas comuns de compartilhamento de imagens. Por exemplo, se uma imagem já está em um nó inclui a camada de Linux Alpine como sua base, o pull subsequentes de uma imagem diferente referenciando a mesma camada não transfere a camada para o nó. Em vez disso, ele faz referência à camada já existente no nó.

### <a name="manifest"></a>Manifesto

Cada imagem de contêiner enviada para um registro de contêiner é associada a um *manifesto*. O manifesto, gerado pelo registro quando a imagem é enviada, identifica de forma exclusiva a imagem e especifica suas camadas. Você pode listar os manifestos de um repositório com o comando da CLI do Azure [az acr repository show-manifests ][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Por exemplo, listar o manifesto resumos para o repositório "acr-helloworld":

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

O manifesto discutido aqui é diferente do manifesto da imagem que você pode ver no portal do Azure ou com [manifesto do docker inspecionar][docker-manifest-inspect]. Na seção a seguir, "digest de manifesto" refere-se para o resumo da mensagem gerado pela operação de envio por push, não o *config.digest* no manifesto de imagem. Você pode extrair e excluir imagens pelo **digest do manifesto**, não pelo config.digest. A imagem a seguir ilustra os dois tipos de resumos.

![Resumo de manifesto e config.digest no portal do Azure][manifest-digest]

### <a name="manifest-digest"></a>Resumo do manifesto

Manifestos são identificados por um único hash SHA-256, ou *manifesto digest*. Cada imagem – se marcadas ou não – é identificada por seu resumo. O valor de resumo é exclusivo, mesmo se os dados da camada da imagem forem idênticos aos de outra imagem. Esse mecanismo é o que permite que você envie repetidamente imagens com tags idênticas para um registro. Por exemplo, você pode enviar repetidamente `myimage:latest` para o seu registro sem erro, porque cada imagem é identificada pelo seu resumo único.

Você pode receber uma imagem de um registro, especificando seu resumo na operação de pull. Alguns sistemas podem ser configurados para extrair por digest, pois ela garante que a versão da imagem que está sendo extraída, mesmo se uma imagem de forma idêntica marcada subsequentemente é enviada por push ao registro.

Por exemplo, extrair uma imagem do repositório "acr-helloworld" por digest manifesto:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Se você enviar repetidamente imagens modificadas com marcas idênticas, poderá criar imagens órfãs - imagens sem tag, mas que ainda consomem espaço no registro. Imagens não marcadas não são mostradas na CLI do Azure ou no portal do Azure quando você lista ou exibe imagens por marca. No entanto, suas camadas ainda existem e consomem espaço no seu registro. A seção [Excluir imagens não marcadas](#delete-untagged-images) deste artigo discute a liberação de espaço usada por imagens não marcadas.

## <a name="delete-image-data"></a>Excluir dados de imagem

Você pode excluir dados de imagem do seu registro de contêiner de várias maneiras:

* Excluir um [repositório](#delete-repository): exclui todas as imagens e todas as camadas exclusivas do repositório.
* Excluir por [marca](#delete-by-tag): exclui uma imagem, a marca, todas as camadas exclusivas referenciadas pela imagem e todas as outras marcas associadas à imagem.
* Excluir por [resumo do manifesto](#delete-by-manifest-digest): exclui uma imagem, todas as camadas exclusivas referenciadas pela imagem e todas as marcas associadas à imagem.

## <a name="delete-repository"></a>Excluir repositório

A exclusão de um repositório exclui todas as imagens no repositório, incluindo todas as tags, camadas exclusivas e manifestos. Quando você exclui um repositório, recupera o espaço de armazenamento usado pelas imagens que estavam nesse repositório.

O seguinte comando da CLI do Azure exclui o repositório "acr-helloworld" e todas as tags e manifestos no repositório. Se as camadas referenciadas pelos manifestos excluídos não forem referenciadas por outras imagens no registro, os dados da camada também serão excluídos.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Excluir por marca

Você pode excluir imagens individuais de um repositório especificando o nome e a marca do repositório na operação de exclusão. Quando você exclui por marca, recupera o espaço de armazenamento usado por qualquer camada exclusiva na imagem (camadas não compartilhadas por outras imagens no registro).

Para excluir por marca, use [exclusão de repositório de acr az][az-acr-repository-delete] e especifique o nome da imagem no`--image` parâmetro. Todas as camadas exclusivas da imagem e quaisquer outras tags associadas à imagem são excluídas.

Por exemplo, excluindo a imagem "acr-helloworld: latest" do registro "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> A exclusão de *pela tag* não deve ser confundida com a exclusão de uma tag (desmarcação). Você pode excluir uma marca com o comando da CLI do Azure [repositório do acr az desmarcar][az-acr-repository-untag]. Nenhum espaço é liberado quando você desmarcar uma imagem porque seu [manifesto](#manifest) e camada de dados permanece no registro. Somente a referência de marca em si é excluída.

## <a name="delete-by-manifest-digest"></a>Excluir pelo manifesto digest

Um [resumo do manifesto](#manifest-digest) pode ser associado a uma, nenhuma ou várias tags. Quando você exclui por digest, todas as tags referenciadas pelo manifesto são excluídas, assim como os dados da camada para qualquer camada exclusiva da imagem. Compartilhado a camada de dados não são excluídos.

Para excluir, digest, a primeira lista o manifesto resumos para o repositório que contém as imagens que você deseja excluir. Por exemplo: 

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Em seguida, especifique o resumo da mensagem que deseja excluir na [exclusão de repositório de acr az][az-acr-repository-delete] comando. O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo, para excluir o último manifesto listado na saída anterior (com a tag "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

A imagem "acr-helloworld: v2" é excluída do registro, assim como qualquer dado de camada exclusivo para essa imagem. Se um manifesto é associado com várias marcas, todas as marcas associadas também são excluídas.

## <a name="delete-untagged-images"></a>Excluir imagens sem marcas

Conforme mencionado na [manifesto digest](#manifest-digest) seção, enviar por push uma imagem modificada usando uma marca existente **untags** a imagem anteriormente enviada, resultando em uma imagem órfão (ou "pendentes"). A imagem anteriormente enviada de manifesto – e seus dados de camada-- permanece no registro. Considere a seguinte sequência de eventos:

1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modifique *acr helloworld* Dockerfile
1. Enviar imagem por push *acr helloworld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Como você pode ver na saída da última etapa na sequência, há agora uma órfãos de manifesto cuja `"tags"` propriedade é uma lista vazia. Esse manifesto ainda existe no registro, junto com todos os dados de camada exclusivos que ele referencia. **Para excluir tais órfão imagens e seus dados de camada, você deve excluir digest manifesto**.

### <a name="list-untagged-images"></a>Listar imagens sem marcas

Você pode listar todas as imagens não identificadas em seu repositório usando o seguinte comando da CLI do Azure. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>Excluir todas as imagens não marcas

Use os scripts de exemplo a seguir com cuidado – excluído dados de imagem são IRRECUPERÁVEL.

**CLI do Azure no Bash**

O script Bash a seguir exclui todas as imagens não marcadas de um repositório. Ele requer a CLI do Azure e **xargs**. Por padrão, o script não realiza nenhuma exclusão. Altere o `ENABLE_DELETE` valor para `true` para ativar a exclusão da imagem.

> [!WARNING]
> Se você tiver sistemas que obtêm imagens pelo resumo do manifesto (em oposição ao nome da imagem), você não deve executar esse script. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de receber pelo manifesto, considerar a adoção de uma *marcação exclusiva* esquema, um [melhor prática recomendada][tagging-best-practices].

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**CLI do Azure no PowerShell**

O seguinte script do PowerShell exclui todas as imagens não identificadas de um repositório. Ele requer o PowerShell e a CLI do Azure. Por padrão, o script não realiza nenhuma exclusão. Altere o `$enableDelete` valor para `$TRUE` para ativar a exclusão da imagem.

> [!WARNING]
> Se você tiver sistemas que obtêm imagens pelo resumo do manifesto (em oposição ao nome da imagem), você não deve executar esse script. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de receber pelo manifesto, considerar a adoção de uma *marcação exclusiva* esquema, um [melhor prática recomendada][tagging-best-practices].

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o armazenamento de imagens no Azure Container Registry, consulte [Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
