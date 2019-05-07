---
title: Instalar os contêineres de fala
titleSuffix: Azure Cognitive Services
description: Instalar e executar os contêineres de fala. A conversão de fala em texto transcreve, em tempo real, fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2adcbad55236917685ddcdbabe4809f36ab5a730
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153060"
---
# <a name="install-and-run-speech-service-containers"></a>Instalar e executar contêineres do serviço de fala

Contêineres de fala permitem que os clientes criar uma arquitetura de aplicativos de fala que é otimizada para tirar proveito dos recursos de nuvem robusto e localidade de borda. Os contêineres de dois fala damos suporte agora estão **fala em texto** e **texto em fala**. 

São os contêineres de duas fala **fala em texto** e **texto em fala**. 

|Função|Recursos|Mais recente|
|-|-|--|
|Conversão de fala em texto| <li>Transcreve fala em tempo real contínua em texto.<li>Pode transcrever em lotes fala de gravações de áudio. <li>Dá suporte a resultados intermediários, detecção de final de fala, formatação automática de texto e mascaramento de conteúdo ofensivo. <li>Pode chamar [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) (Reconhecimento vocal) para avaliar a intenção do usuário a partir da fala transcrita.\*|1.1.1|
|Conversão de texto em fala| <li>Converte o texto em fala que soa natural. <li>Oferece vários gêneros e/ou dialetos para muitos idiomas com suporte. <li>Dá suporte à entrada de texto sem formatação ou a SSML (Speech Synthesis Markup Language). |1.1.0|

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de fala:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recursos de fala |Para usar esses contêineres, você deve ter:<br><br>Um _fala_ recursos do Azure para obter a chave de cobrança associada e o URI de ponto de extremidade cobrança. Ambos os valores estão disponíveis no portal Azure **fala** páginas de visão geral e as chaves e são necessárias para iniciar o contêiner.<br><br>**{BILLING_KEY}**: chave do recurso<br><br>**{BILLING_ENDPOINT_URI}**: exemplo de URI de terminal é: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Solicitação de acesso para o registro de contêiner

Você deve primeiro concluir e enviar o [formulário de solicitação de contêineres de fala de serviços Cognitivos](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Suporte de extensão de vetor avançado

O **host** é o computador que executa o contêiner do Docker. O host deve suportar [extensões de vetor avançadas](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Você pode verificar esse suporte em hosts do Linux com o seguinte comando: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU e memória para alocar para cada contêiner fala mínimos e recomendados.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | Dois núcleos<br>2 GB de memória  | Quatro núcleos<br>4 GB de memória  |
|cognitive-services-text-to-speech | 1 núcleo, 0,5 GB de memória| 2 núcleos, 1 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.


Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

**Observação**; Mínimos e recomendados são baseadas em limites de Docker *não* recursos do computador host. Por exemplo, partes de mapa de memória de contêineres de fala em texto de um modelo de linguagem grande e ele é _recomendado_ que o arquivo inteiro se ajuste na memória, que é um adicional de 4 a 6 GB. Além disso, a primeira execução de qualquer contêiner poderá demorar mais, uma vez que os modelos estão sendo paginados na memória.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Imagens de contêiner para conversão de fala estão disponíveis. 

| Contêiner | Repositório |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Localidade do idioma é na marca de contêiner

O `latest` pulls de marca a `en-us` localidade e `jessarus` voz. 

#### <a name="speech-to-text-locales"></a>Conversão de fala em localidades de texto

Todas as marcas, exceto para `latest` estão no formato a seguir, onde o `<culture>` indica o contêiner de localidade:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Marca a seguir está um exemplo do formato:

```
1.0.0-amd64-en-us-preview
```

A tabela a seguir lista as localidades com suporte para **fala em texto** na 1.1.1 versão do contêiner:

|Localidade do idioma|tags|
|--|--|
|Chinês|`zh-cn`|
|Inglês |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francês |`fr-ca`<br>`fr-fr`|
|Alemão|`de-de`|
|Italiano|`it-it`|
|Japonês|`ja-jp`|
|Coreano|`ko-kr`|
|Português|`pt-br`|
|Espanhol|`es-es`<br>`es-mx`|


#### <a name="text-to-speech-locales"></a>Localidades de texto em fala

Todas as marcas, exceto para `latest` estão no formato a seguir, onde o `<culture>` indica a localidade e o `<voice>` indica a voz do contêiner:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Marca a seguir está um exemplo do formato:

```
1.0.0-amd64-en-us-jessarus-preview
```

A tabela a seguir lista as localidades com suporte para **fala** no 1.1.0 versão do contêiner:

|Localidade do idioma|tags|Vozes com suporte|
|--|--|--|
|Chinês|`zh-cn`|huihuirus<br>kangkang-apollo<br>apollo yaoyao|
|Inglês |`en-au`|Catherine<br>hayleyrus|
|Inglês |`en-gb`|George apollo<br>hazelrus<br>Susan apollo|
|Inglês |`en-in`|apollo heera<br>priyarus<br>ravi-apollo<br>|
|Inglês |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francês|`fr-ca`|Carolina<br>harmonierus|
|Francês|`fr-fr`|hortenserus<br>Julie apollo<br>Paul apollo|
|Alemão|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italiano|`it-it`|apollo cosimo<br>luciarus|
|Japonês|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coreano|`ko-kr`|heamirus|
|Português|`pt-br`|Daniel apollo<br>heloisarus|
|Espanhol|`es-es`|elenarus<br>Laura apollo<br>Pablo apollo<br>|
|Espanhol|`es-mx`|hildarus<br>Ricardo apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Pull do docker para os contêineres de fala

#### <a name="speech-to-text"></a>Conversão de fala em texto

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Conversão de texto em fala

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run), com as configurações de cobrança necessárias, mas não foi usadas. Há outros [exemplos](speech-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

**Durante a visualização**, as configurações de cobrança devem ser válidas para iniciar o contêiner, mas você não cobrado pelo uso.

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página de chaves de fala do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade cobrança está disponível na página de visão geral de fala do portal do Azure.|

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

### <a name="text-to-speech"></a>Conversão de texto em fala

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Conversão de fala em texto

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Esse comando:

* Execute um contêiner de fala a partir da imagem de contêiner
* Aloca os núcleos de CPU de 2 a 2 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

|Contêiner|Ponto de extremidade|
|--|--|
|Conversão de fala em texto|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Conversão de texto em fala|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Conversão de fala em texto

O contêiner fornece o ponto de extremidade de consulta com base em websocket APIs, que são acessados por meio de [Speech SDK](index.yml).

Por padrão, o SDK de fala usa serviços de fala on-line. Para usar o contêiner, você precisará alterar o método de inicialização. Consulte os exemplos a seguir.

#### <a name="for-c"></a>ParaC#

Alterar o uso dessa chamada de inicialização de nuvem do Azure:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

para essa chamada usando o ponto de extremidade do contêiner:

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Para Python

Alterar o uso dessa chamada de inicialização de nuvem do Azure

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

para essa chamada usando o ponto de extremidade do contêiner:

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Conversão de texto em fala

O contêiner fornece o ponto de extremidade REST APIs que podem ser encontradas [aqui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) e os exemplos podem ser encontrados [aqui](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>solução de problemas

Quando você executa o contêiner, o contêiner usa **stdout** e **stderr** às informações de saída que é útil para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner. 

## <a name="billing"></a>Cobrança

O envio de contêineres de fala cobrança informações do Azure, usando um _fala_ recurso em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](speech-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de fala. Em resumo:

* Fala fornece dois contêineres de Linux para o Docker, encapsulando a conversão de fala em texto e texto em fala.
* Imagens de contêiner são baixadas do registro de contêiner privado no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de fala, especificando o URI do contêiner de host.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
>  Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](speech-container-configuration.md) para configurações
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
