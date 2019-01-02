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
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080258"
---
# <a name="configure-containers"></a>Configurar contêineres

O ambiente de tempo de execução do contêiner do LUIS (Serviço Inteligente de Reconhecimento Vocal) é configurado usando argumentos de comando `docker run`. O LUIS tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as [configurações de montagem](#mount-settings) de entrada e as configurações de cobrança. 

Configurações de contêiner são [hierárquicos](#hierarchical-settings) e podem ser definidos com [variáveis de ambiente](#environment-variable-settings) ou [argumentos de linha de comando](#command-line-argument-settings) do Docker.

## <a name="configuration-settings"></a>Definições de configuração

Esse contêiner tem as seguintes configurações:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|SIM|[ApiKey](#apikey-setting)|Usado para rastrear informações de cobrança.|
|Não |[ApplicationInsights](#applicationinsights-setting)|Permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner.|
|SIM|[Cobrança](#billing-setting)|Especifica o URI do ponto de extremidade do recurso _Reconhecimento Vocal_ no Azure.|
|SIM|[Eula](#eula-setting)| Indica que você aceitou a licença para o contêiner.|
|Não |[Fluentd](#fluentd-settings)|Gravar log e, opcionalmente, dados telemétricos em um servidor do Fluentd.|
|Não |[Registro em log](#logging-settings)|Fornece suporte a registro de log do ASP.NET Core para seu contêiner. |
|SIM|[Mounts](#mount-settings)|Lê e grava dados do [computador host](luis-container-howto.md#the-host-computer) para o contêiner e do contêiner de volta para o computador host.|

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Configuração ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor de ApiKey e o valor deve ser uma chave válida para o recurso _Reconhecimento Vocal_ especificado para a definição de configuração [`Billing`](#billing-setting).

Essa configuração pode ser encontrada em dois locais:

* Portal do Azure: gerenciamento de recurso do **Serviço Inteligente de Reconhecimento Vocal**, em **Chaves**
* Portal do LUIS: página **Configurações de Ponto de Extremidade e Chaves**. 

Não use a chave inicial ou de criação. 

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

A configuração `ApplicationInsights` permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner. O Application Insights fornece um monitoramento detalhado do seu contêiner. Você pode monitorar facilmente seu contêiner quanto a disponibilidade, desempenho e uso. Você pode identificar e diagnosticar erros em seu contêiner rapidamente.

A tabela a seguir descreve as definições de configuração com suporte sob o `ApplicationInsights` seção.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Não | `InstrumentationKey` | Cadeia de caracteres | A chave de instrumentação da instância do Application Insights para o qual telemetria de dados para o contêiner são enviados. Para obter mais informações, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Configuração de cobrança

A configuração `Billing` especifica o URI de ponto de extremidade do recurso _Reconhecimento Vocal_ no Azure usado para medir informações de cobrança para o contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de terminal válido para um recurso do _Serviço Inteligente de Reconhecimento Vocal_ no Azure.

Essa configuração pode ser encontrada em dois locais:

* Portal do Azure: Visão Geral do**Serviço Inteligente de Reconhecimento Vocal**, chamado `Endpoint`
* Portal do LUIS: página **Chaves e configurações de Ponto de Extremidade**, como parte do URI do ponto de extremidade.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|SIM| `Billing` | Cadeia de caracteres | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Configuração de EULA

A configuração `Eula` indica que você aceitou a licença para o contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser definido como `accept`.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|SIM| `Eula` | Cadeia de caracteres | Aceitação da licença<br><br>Exemplo:<br>`Eula=accept` |

Os contêineres de Serviços Cognitivos estão licenciados de acordo com o [contrato](https://go.microsoft.com/fwlink/?linkid=2018657) que rege seu uso do Azure. Se você não tiver um contrato existente que rege o uso do Azure, você concorda que o seu contrato que rege o uso do Azure é o [Contrato de Assinatura do Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), que incorpora os [Termos de Serviços Online ](https://go.microsoft.com/fwlink/?linkid=2018760). Para versões prévias, você também concorda com os [Termos de Uso Adicionais para Versões Prévias do Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Ao usar o contêiner, você concorda com estes termos.

## <a name="fluentd-settings"></a>Configurações de Fluentd

O Fluentd é um coletor de dados de software livre para registro em log unificado. As configurações do `Fluentd` gerenciam a conexão do contêiner com um servidor [Fluentd](https://www.fluentd.org). O contêiner do LUIS inclui um provedor de registro em log do Fluentd, que permite que seu contêiner grave logs e, opcionalmente, dados de métrica para um servidor do Fluentd.

A tabela a seguir descreve as definições de configuração com suporte sob o `Fluentd` seção.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Host` | Cadeia de caracteres | O endereço IP ou o nome do host DNS do servidor Fluentd. |
| `Port` | Número inteiro | A porta do servidor Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de evento tiver sido enviado antes que esse intervalo expire, uma pulsação será enviada ao servidor Fluentd. O valor padrão é 60.000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço de buffer de rede, em bytes, alocado para operações de envio. O valor padrão é 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo de limite, em milissegundos, para estabelecer uma conexão SSL/TLS com o servidor do Fluentd. O valor padrão é 10.000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como false, esse valor é ignorado. |
| `UseTLS` | BOOLEAN | Indica se o contêiner deve usar SSL / TLS para se comunicar com o servidor Fluentd. O valor padrão é falso. |

## <a name="logging-settings"></a>Configurações de registro em log

As configurações `Logging` gerenciam o suporte ao registro em log do ASP.NET Core para o contêiner. Você pode usar as mesmas definições de configuração e valores para o contêiner usado em um aplicativo ASP.NET Core. 

Os provedores de registro em log a seguir são compatíveis com os contêineres do LUIS:

|Provedor|Finalidade|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O ASP.NET Core `Console` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.|
|[Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O ASP.NET Core `Debug` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.|
|[Disco](#disk-logging)|O provedor de log do JSON. Esse provedor de log grava dados de log na montagem de saída.|

### <a name="disk-logging"></a>Registro em log do Disk
  
O provedor de registro `Disk` suporta as seguintes configurações:  

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Format` | Cadeia de caracteres | O formato de saída para arquivos de log.<br/> **Observação:** Esse valor deve ser definido como `json` para habilitar o provedor de logs. Se esse valor for especificado sem especificar também uma montagem de saída durante a instanciação de um contêiner, ocorrerá um erro. |
| `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um arquivo de log. Quando o tamanho do arquivo de log atual atende ou excede esse valor, um novo arquivo de log é iniciado pelo provedor de log. Se -1 for especificado, o tamanho do arquivo de log será limitado apenas pelo tamanho máximo do arquivo, se houver, para a montagem de saída. O valor padrão é 1. |

Para obter mais informações sobre como configurar o suporte ao registro em log do ASP.NET Core, consulte [Configuração do arquivo de configurações](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

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

As configurações do contêiner do LUIS são hierárquicas e todos os contêineres no [computador host](luis-container-howto.md#the-host-computer) usam uma hierarquia compartilhada.

Você pode usar uma das seguintes opções para especificar as configurações:

* [Variáveis de ambiente](#environment-variable-settings)
* [Argumentos de linha de comando](#command-line-argument-settings)

Os valores de variáveis de ambiente substituem os valores do argumento da linha de comandos, os quais, por sua vez, substituem os valores padrão da imagem do contêiner. Se você especificar valores diferentes em uma variável de ambiente e um argumento de linha de comando para a mesma configuração, o valor na variável de ambiente será usado pelo contêiner instanciado.

|Precedência|Local da configuração|
|--|--|
|1|Variável de ambiente| 
|2|Linha de comando|
|3|Valor padrão de imagem de contêiner|

### <a name="environment-variable-settings"></a>Configurações da variável de ambiente

Os benefícios de se usar variáveis de ambiente são:

* Várias configurações podem ser definidas.
* Vários contêineres podem usar as mesmas configurações.

### <a name="command-line-argument-settings"></a>Configurações do argumento de linha de comando

A vantagem de se usar argumentos de linha de comando é que cada contêiner pode usar configurações diferentes.

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
> O comando anterior usa o diretório fora da unidade `c:` para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez precise conceder ao Docker permissão de serviço. O comando Docker anterior usa a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova a barra invertida com base nos requisitos do sistema operacional de seu [computador host](luis-container-howto.md#the-host-computer). não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.


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