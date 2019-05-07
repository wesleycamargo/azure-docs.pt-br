---
title: Configurar um contêiner – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Saiba como usar o contêiner do Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 6066e7856ddf8ef757afc2072218c87420a37c10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025546"
---
# <a name="configure-form-recognizer-containers"></a>Configurar contêineres do Reconhecimento de Formulários

Os contêineres do Reconhecimento de Formulários permitem que os clientes criem uma arquitetura de aplicativo otimizada para aproveitar as funcionalidades de nuvem robustas e a localidade de borda.

O ambiente de tempo de execução do contêiner do **Reconhecimento de Formulários** é configurado usando os argumentos de comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. É necessário especificar um valor para a ApiKey e o valor precisa ser uma chave válida para o recurso _Reconhecimento de Formulários_ especificado para a definição de configuração [`Billing`](#billing-configuration-setting).

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: Gerenciamento de Recursos do **Reconhecimento de Formulários** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade do recurso _Reconhecimento de Formulários_ no Azure usado para medir informações de cobrança para o contêiner. É necessário especificar um valor para essa definição de configuração, e o valor precisa ser um URI do ponto de extremidade válido para um recurso do _Reconhecimento de Formulários_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: Visão Geral do **Reconhecimento de Formulários**, `Endpoint` rotulada

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia de caracteres | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Configurações de registro em log

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres do Reconhecimento de Formulários exigem uma montagem de entrada e saída. A montagem de entrada pode ser somente leitura e é necessária para acessar os dados que serão usados para treinamento e pontuação. A montagem de saída deve ser gravável e será usada para armazenar os modelos e os dados temporários.

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host.

|Opcional| NOME | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|Obrigatório| `Input` | Cadeia de caracteres | O destino de montagem de entrada. O valor padrão é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obrigatório| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](form-recognizer-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host.
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Placeholder | Valor |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página Chaves do Reconhecimento de Formulários do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor de URI do ponto de extremidade de cobrança está disponível na página Visão Geral do Reconhecimento de Formulários do portal do Azure.|
|{COMPUTER_VISION_API_KEY}| A chave está disponível na página Chaves de API de Pesquisa Visual Computacional do portal do Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|O ponto de extremidade de cobrança. Se você estiver usando um recurso baseado em nuvem da Pesquisa Visual Computacional, o valor do URI estará disponível na página Visão Geral da API de Pesquisa Visual Computacional do portal do Azure. Se você estiver usando um contêiner do `cognitive-services-recognize-text`, use a URL do ponto de extremidade de cobrança passada para o contêiner no comando `docker run`.|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing-configuration-setting).
> O valor da ApiKey é a **Chave** da página de chaves do Recurso do Reconhecimento de Formulários do Azure.

## <a name="form-recognizer-container-docker-examples"></a>Exemplos do Docker do contêiner do Reconhecimento de Formulários

Os exemplos do Docker a seguir referem-se ao contêiner do Reconhecimento de Formulários.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico do Reconhecimento de Formulários

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemplo de log do Reconhecimento de Formulários

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](form-recognizer-container-howto.md)
