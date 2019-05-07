---
title: Instalar e executar o contêiner – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Saiba como usar o contêiner do Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 5d4374b329049e2e55966a28567c5232be77abda
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025418"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalar e executar contêineres do Reconhecimento de Formulários
O Reconhecimento de Formulários aplica a tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e tabelas de formulários. Ele associa valores e entradas de tabelas a eles e, em seguida, gera dados estruturados que incluem as relações no arquivo original. Chame seu modelo personalizado do Reconhecimento de Formulários usando uma API REST simples para reduzir a complexidade e integrá-lo com facilidade ao seu processo de automação de fluxo de trabalho ou a outro aplicativo. Apenas cinco documentos (ou um formulário vazio) são necessários, de modo que você possa obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados. Não exige rotulagem nem anotação de dados.

|Função|Recursos|
|-|-|
|Reconhecimento de Formulários| <li>Processa arquivos do tipo PDF, PNG e JPG.<li>Treina modelos personalizados com, no mínimo, cinco formulários do mesmo layout. <li>Extrai pares chave-valor e informações de tabela. <li>Usa o RecognizeText da API de Pesquisa Visual Computacional do Serviço Cognitivo para detectar e extrair texto impresso de imagens em formulários.<li>Não exige anotação nem rotulagem.|

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

É necessário atender aos seguintes pré-requisitos antes de usar contêineres do Reconhecimento de Formulários:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.|
|CLI do Azure| É necessário instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no host.|
|Recurso da API de Pesquisa Visual Computacional| Para processar imagens e documentos digitalizados, é necessário ter um **recurso da Pesquisa Visual Computacional**. Acesse o recurso **Reconhecimento de Texto** como um recurso do Azure (API REST ou SDK) ou como um contêiner do `cognitive-services-recognize-text`. Os valores de cobrança normais se aplicam. <br><br>É necessário passar a chave e o ponto de extremidade de cobrança para o recurso específico da Pesquisa Visual Computacional (nuvem do Azure ou contêiner dos Serviços Cognitivos). Use essa chave e o ponto de extremidade de cobrança como {COMPUTER_VISION_API_KEY} e {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Se você usar o **contêiner do `cognitive-services-recognize-text`**, verifique se:<br><br>*A chave da Pesquisa Visual Computacional para o contêiner do Reconhecimento de Formulários é a chave especificada no comando `docker run` da Pesquisa Visual Computacional para o contêiner do `cognitive-services-recognize-text`.<br>*O ponto de extremidade de cobrança é o ponto de extremidade do contêiner, como `https://localhost:5000`. Se você usar os contêineres da Pesquisa Visual Computacional e do Reconhecimento de Formulários juntos no mesmo host, eles não poderão ser iniciados com a porta padrão `5000`.  |  
|Recurso do Reconhecimento de Formulários |Para usar esses contêineres, será necessário ter:<br><br>Um recurso do Azure do _Reconhecimento de Formulários_ para obter a chave de cobrança e o URI do ponto de extremidade de cobrança associados. Ambos os valores estão disponíveis nas páginas Visão Geral e Chaves do **Reconhecimento de Formulários** do portal do Azure e são necessários para iniciar o contêiner.<br><br>**{BILLING_KEY}**: chave do recurso<br><br>**{BILLING_ENDPOINT_URI}**: exemplo de URI de terminal é: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Primeiro, é necessário preencher e enviar o [formulário Solicitação de Contêineres do Reconhecimento de Formulários dos Serviços Cognitivos](https://aka.ms/FormRecognizerRequestAccess) para solicitar acesso ao contêiner. Isso também fará sua inscrição na Pesquisa Visual Computacional. Você não precisa se inscrever no formulário de solicitação da Pesquisa Visual Computacional separadamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU e a memória mínimos e recomendados a serem alocados para cada contêiner do Reconhecimento de Formulários.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 núcleos, 4 GB de memória | 4 núcleos, 8 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

> [!Note]
> Os valores mínimos e recomendados se baseiam nos limites do Docker e *não* nos recursos do computador host.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Estão disponíveis imagens de contêiner para o Reconhecimento de Formulários.

| Contêiner | Repositório |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull para o contêiner do Reconhecimento de Formulários

#### <a name="form-recognizer"></a>Reconhecimento de Formulários

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias, mas não utilizadas. Há outros [exemplos](form-recognizer-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

| Placeholder | Valor |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página Chaves do Reconhecimento de Formulários do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor de URI do ponto de extremidade de cobrança está disponível na página Visão Geral do Reconhecimento de Formulários do portal do Azure.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível na página Chaves de API de Pesquisa Visual Computacional do portal do Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto de extremidade de cobrança. Se você estiver usando um recurso baseado em nuvem da Pesquisa Visual Computacional, o valor do URI estará disponível na página Visão Geral da API de Pesquisa Visual Computacional do portal do Azure. Se você estiver usando um contêiner do `cognitive-services-recognize-text`, use a URL do ponto de extremidade de cobrança passada para o contêiner no comando `docker run`.|

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

### <a name="form-recognizer"></a>Reconhecimento de Formulários

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Esse comando:

* Executa um contêiner do Reconhecimento de Formulários na imagem de contêiner
* Aloca 2 núcleos de CPU e 8 GB (gigabytes) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.
* Monta um volume /input e /output no contêiner

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Executar contêineres separados como comandos de execução do docker separados

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto hospedada localmente no mesmo host, dois comandos de exemplo da CLI do Docker são mostrados a seguir.

Executar o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Executar o segundo contêiner na porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Cada contêiner seguinte deve estar em uma porta diferente. 

### <a name="run-separate-containers-with-docker-compose"></a>Executar contêineres separados com o Docker Compose

Para a combinação de Reconhecimento de Formulários e Reconhecimento de Texto hospedada localmente no mesmo host, um arquivo YAML de exemplo do Docker Compose é mostrado a seguir. A `{COMPUTER_VISION_API_KEY}` do Reconhecimento de Texto precisa ser a mesma para os contêineres `formrecognizer` e `ocr`. O `{COMPUTER_VISION_ENDPOINT_URI}` só é usado no contêiner `ocr` porque ele `formrecognizer` usa o nome e a porta do `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey`, bem como `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri`, precisam ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

|Contêiner|Ponto de extremidade|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Reconhecimento de Formulários

O contêiner fornece APIs do ponto de extremidade de consulta baseadas em WebSocket, que são acessadas por meio da [documentação do SDK de serviços do Reconhecimento de Formulários](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Por padrão, o SDK do Reconhecimento de Formulários usa os serviços online. Para usar o contêiner, você precisa alterar o método de inicialização. Confira os exemplos abaixo.

#### <a name="for-c"></a>Para o C#

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

use esta chamada usando o ponto de extremidade do contêiner:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para o Python

Em vez de usar esta chamada de inicialização de nuvem do Azure

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

use esta chamada usando o ponto de extremidade do contêiner:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Reconhecimento de Formulários

O contêiner fornece APIs REST do ponto de extremidade, que podem ser encontradas [aqui](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api), e os exemplos podem ser encontrados [aqui](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>solução de problemas

Quando você executa o contêiner, ele usa **StdOut** e **stderr** para gerar informações úteis para solucionar problemas que ocorrem durante a inicialização ou a execução do contêiner.

## <a name="billing"></a>Cobrança

Os contêineres do Reconhecimento de Formulários enviam informações de cobrança para o Azure usando um recurso do _Reconhecimento de Formulários_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](form-recognizer-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e o fluxo de trabalho para baixar, instalar e executar contêineres do Reconhecimento de Formulários. Em resumo:

* O Reconhecimento de Formulários fornece um contêiner do Linux para o Docker.
* As imagens de contêiner são baixadas do Registro de contêiner particular no Azure.
* Imagens de contêiner são executadas no Docker.
* Use a API REST ou o SDK para chamar operações no contêiner do Reconhecimento de Formulários especificando o URI de host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
>  Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](form-recognizer-container-configuration.md) para configurações
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
