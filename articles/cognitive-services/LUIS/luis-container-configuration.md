---
title: Configurações de contêiner do Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: O ambiente de tempo de execução do contêiner do LUIS é configurado usando argumentos de comando `docker run`. O LUIS tem várias configurações obrigatórias e outras configurações opcionais.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: e8e838fae0da3a47fe1b3ec8d412f956f5f28034
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975502"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurar contêineres do docker do Serviço Inteligente de Reconhecimento Vocal 

O ambiente de tempo de execução do contêiner do LUIS (Serviço Inteligente de Reconhecimento Vocal) é configurado usando argumentos de comando `docker run`. O LUIS tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as [configurações de montagem](#mount-settings) de entrada e as configurações de cobrança. 

Configurações de contêiner são [hierárquicos](#hierarchical-settings) e podem ser definidos com [variáveis de ambiente](#environment-variable-settings) ou [argumentos de linha de comando](#command-line-argument-settings) do Docker.

## <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|SIM|[ApiKey](#apikey-setting)|Usado para rastrear informações de cobrança.|
|Não |[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|SIM|[Cobrança](#billing-setting)|Especifica o URI do ponto de extremidade do recurso de serviços no Azure.|
|SIM|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não |[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não |[Registro em log](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|SIM|[Mounts](#mount-settings)|Lê e grava dados do computador host para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Configuração ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor de ApiKey e o valor deve ser uma chave válida para o recurso _Reconhecimento Vocal_ especificado para a definição de configuração [`Billing`](#billing-setting).

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: gerenciamento de recurso do **Serviço Inteligente de Reconhecimento Vocal**, em **Chaves**
* Portal do LUIS: página **Configurações de Ponto de Extremidade e Chaves**. 

Não use a chave inicial ou de criação. 

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Configuração de cobrança

A configuração `Billing` especifica o URI de ponto de extremidade do recurso _Reconhecimento Vocal_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de terminal válido para um recurso do _Serviço Inteligente de Reconhecimento Vocal_ no Azure.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: Visão Geral do**Serviço Inteligente de Reconhecimento Vocal**, chamado `Endpoint`
* Portal do LUIS: página **Chaves e configurações de Ponto de Extremidade**, como parte do URI do ponto de extremidade.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|SIM| `Billing` | Cadeia de caracteres | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/). 

O contêiner do LUIS não usa montagens de entrada ou saída para armazenar dados de treinamento ou de serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, o local de montagem do [computador host](luis-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões do local de montagem do host. 

A tabela a seguir descreve as configurações com suporte.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|SIM| `Input` | Cadeia de caracteres | O destino de montagem de entrada. O valor padrão é `/input`. Esse é o local dos arquivos de pacote do LUIS. <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Não | `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de consulta do LUIS e logs do contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Configurações hierárquicas

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](luis-container-howto.md#stop-the-container).


* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Placeholder | Valor | Formato ou exemplo |
|-------------|-------|---|
|{ENDPOINT_KEY} | A chave do ponto de extremidade do aplicativo LUIS treinado. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | O valor do ponto de extremidade de cobrança está disponível na página Visão Geral do Serviço Inteligente de Reconhecimento Vocal do portal do Azure.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](luis-container-howto.md#billing).
> O valor de ApiKey é a **Chave** da página Chaves e Pontos de extremidade no portal do LUIS e também está disponível na página de chaves do recurso Reconhecimento Vocal do Azure. 

### <a name="basic-example"></a>Exemplo básico

O exemplo abaixo tem o menor número de argumentos possível para executar o contêiner:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> O comando anterior usa o diretório fora da unidade `c:` para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço. O comando Docker anterior usa a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova a barra invertida com base nos requisitos do sistema operacional de seu [computador host](luis-container-howto.md#the-host-computer). não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.


### <a name="applicationinsights-example"></a>Exemplo de ApplicationInsights

O exemplo a seguir define o argumento ApplicationInsights para enviar telemetria ao Application Insights enquanto o contêiner está em execução:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Exemplo de registro em log com argumentos de linha de comando

O comando abaixo define o nível de registro em log, `Logging:Console:LogLevel`, para configurar o nível de registro em log como [`Information`](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Exemplo de registro em log com variável de ambiente

O comandos a seguir usam uma variável de ambiente denominada `Logging:Console:LogLevel` para configurar o nível de registro em log como [`Information`](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](luis-container-howto.md)
* Consulte [Perguntas frequentes (FAQ)](luis-resources-faq.md) para resolver problemas relacionados à funcionalidade LUIS.