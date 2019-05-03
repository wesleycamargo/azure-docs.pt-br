---
title: Configurar os contêineres - detector de anomalias
titleSuffix: Azure Cognitive Services
description: O ambiente de tempo de execução de contêiner do Detector de anomalias é configurado usando o `docker run` argumentos do comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 0d09ce29aa5431de3eb82e5d9fe7440d4e3352e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026385"
---
# <a name="configure-anomaly-detector-containers"></a>Configurar os contêineres do Detector de anomalias

O **Detector de anomalias** ambiente de tempo de execução do contêiner é configurado usando o `docker run` argumentos do comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

# <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Sim|[ApiKey](#apikey-configuration-setting)|Usado para rastrear informações de cobrança.|
|Não |[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|Sim|[Billing](#billing-configuration-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|Sim|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não |[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não |[Proxy HTTP](#http-proxy-credentials-settings)|Configure um proxy HTTP para fazer solicitações de saída.|
|Não |[Logging](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|Não |[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o _Detector de anomalias_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Detector de anomalias** gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

O `Billing` configuração especifica o URI do ponto de extremidade do _Detector de anomalias_ recursos no Azure é usado para monitorar informações de cobrança para o contêiner. Você deve especificar um valor para este parâmetro de configuração e o valor deve ser um URI de ponto de extremidade válido para um _Detector de anomalias_ recursos no Azure.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Detector de anomalias** visão geral, rotulado `Endpoint`

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres do Detector de anomalias não usam a entrada ou saída monta para armazenar dados de serviço ou treinamento. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](anomaly-detector-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| NOME | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Contêineres do Detector de anomalias não usam isso.|
|Opcional| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](anomaly-detector-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha para um shell bash. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. Por exemplo, o caractere de continuação de linha para o windows é um acento circunflexo, `^`. Substitua a barra invertida com o cursor. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua o valor entre colchetes, `{}`, com seus próprios valores:

| Placeholder | Value | Formato ou exemplo |
|-------------|-------|---|
|{BILLING_KEY} | A chave do ponto de extremidade do recurso Detector de anomalias. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | O valor de ponto de extremidade cobrança, incluindo a região.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](anomaly-detector-container-howto.md#billing).
> O valor de ApiKey é o **chave** na página de chaves de recurso de Detector de anomalias do Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemplos de Docker de contêiner de Detector de anomalias

Os exemplos de Docker a seguir são para o contêiner do Detector de anomalias. 

### <a name="basic-example"></a>Exemplo básico 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de registro em log com argumentos de linha de comando

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
