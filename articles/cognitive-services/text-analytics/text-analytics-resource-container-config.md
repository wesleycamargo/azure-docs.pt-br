---
title: Configurar contêineres
titlesuffix: Text Analytics - Cognitive Services - Azure
description: Definições de configuração para contêineres da Análise de Texto.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 0f6b8fa27d2db45be2c677a52c53cff5847acf4a
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634865"
---
# <a name="configure-containers"></a>Configurar contêineres

A Análise de Texto fornece uma estrutura de configuração comum a cada contêiner para que você possa facilmente definir e gerenciar configurações de armazenamento, registro em log, telemetria e segurança de seus contêineres.

## <a name="configuration-settings"></a>Definições de configuração

As definições de configuração nos contêineres de Análise de Texto são hierárquicas e todos os contêineres usam uma hierarquia compartilhada, com base na seguinte estrutura de nível superior:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Autenticação](#authentication-configuration-settings)
* [Cobrança](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Registro em log](#logging-configuration-settings)
* [Montagens](#mounts-configuration-settings)

Você pode usar as [variáveis de ambiente](#configuration-settings-as-environment-variables) ou os [argumentos de linha de comando](#configuration-settings-as-command-line-arguments) para especificar as definições de configuração ao instanciar um contêiner de contêineres de Análise de Texto.

Os valores de variáveis de ambiente substituem os valores de argumento da linha de comando, que por sua vez, substituem os valores padrão para a imagem de contêiner. Em outras palavras, se você especificar valores diferentes em uma variável de ambiente e em um argumento da linha de comando para a mesma definição de configuração, como `Logging:Disk:LogLevel`, e então criar uma instância de um contêiner, o valor na variável de ambiente será usado pelo contêiner instanciado.

### <a name="configuration-settings-as-environment-variables"></a>Definições de configuração como variáveis de ambiente

Você pode usar a [sintaxe de variável de ambiente do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) para especificar as definições de configuração.

O contêiner lerá as variáveis de ambiente do usuário quando for instanciado. Se uma variável de ambiente existir, o valor da variável de ambiente substituirá o valor padrão para a definição de configuração especificada. A vantagem de usar variáveis de ambiente é que várias definições de configuração podem ser definidas antes da instanciação de contêineres e vários contêineres podem usar automaticamente o mesmo conjunto de definições de configuração.

Por exemplo, os comandos a seguir usam uma variável de ambiente para configurar o nível de log de console para [LogLevel.Information](https://msdn.microsoft.com), depois criam uma instância de um contêiner com base na imagem de contêiner da Análise de Sentimento. O valor da variável de ambiente substitui a definição de configuração padrão.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Definições de configuração como argumentos de linha de comando

Você pode usar a [sintaxe de argumento de linha de comando do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) para especificar definições de configuração.

Você pode especificar as definições de configuração no parâmetro opcional `ARGS` do comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) usado para criar uma instância de um contêiner com base em uma imagem de contêiner baixada. A vantagem de usar argumentos de linha de comando é que cada contêiner pode usar um conjunto de definições de configuração diferente e personalizado.

Por exemplo, o comando a seguir cria uma instância de um contêiner com base na imagem de contêiner da Análise de Sentimento e configura o nível de log de console para LogLevel.Information, substituindo a definição de configuração padrão.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A definição de configuração `ApiKey` especifica a chave de configuração do recurso de Análise de Texto no Azure usado para rastrear informações de cobrança para o contêiner. Você precisa especificar um valor para esta definição de configuração, o qual precisa ser uma chave de configuração válida para o recurso de Análise de Texto especificado para a definição de configuração [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> As definições de configuração [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você precisa fornecer valores válidos para todos os três. Caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, confira [Cobrança](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Definições de configuração do ApplicationInsights

As definições de configuração na seção `ApplicationInsights` permitem adicionar suporte à telemetria do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) ao contêiner. O Application Insights fornece monitoramento detalhado do contêiner até o nível de código. Você pode monitorar facilmente o contêiner quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente no contêiner sem esperar que um usuário os relate.

A tabela a seguir descreve as definições de configuração com suporte de acordo com a seção `ApplicationInsights`.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `InstrumentationKey` | Cadeia de caracteres | A chave de instrumentação da instância do Application Insights para a qual os dados telemétricos do contêiner são enviados. Para obter mais informações, confira [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Definições de configuração de autenticação

As definições de configuração de `Authentication` oferecem opções de segurança do Azure para seu contêiner. Embora as definições de configuração nesta seção estejam disponíveis para todos os contêineres nos contêineres de Análise de Texto, a maneira em que os valores das definições de configuração são usados é específica para cada contêiner, sendo que os contêineres não podem usar esta seção de modo nenhum.

A tabela a seguir descreve as definições de configuração com suporte de acordo com a seção `Authentication`.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `ApiKey` | Cadeia de caracteres ou matriz | As chaves de assinatura do Azure usadas pelo contêiner para acessar outros recursos do Azure se exigido pelo contêiner.<br/> Se mais de uma chave de assinatura é usada pelo contêiner, esse valor é especificado como uma matriz de cadeias de caracteres; caso contrário, um valor de cadeia de caracteres é usado para especificar uma chave de assinatura única usada pelo contêiner. |

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A definição de configuração `Billing` especifica o URI de ponto de extremidade do recurso de Análise de Texto no Azure usado para rastrear informações de cobrança para o contêiner. Você precisa especificar um valor para esta definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de análise de texto no Azure.

> [!IMPORTANT]
> As definições de configuração [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você precisa fornecer valores válidos para todos os três. Caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, confira [Cobrança](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Definição de configuração EULA

A definição de configuração `Eula` indica que você aceitou a licença para o contêiner. Para esta definição de configuração, você precisa especificar um valor que precisa ser definido como `accept`.

> [!IMPORTANT]
> As definições de configuração [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você precisa fornecer valores válidos para todos os três. Caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas definições de configuração para criar uma instância de um contêiner, confira [Cobrança](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="fluentd-configuration-settings"></a>Definições de configuração do Fluentd

A seção `Fluentd` gerencia as definições de configuração da seção [Fluentd](https://www.fluentd.org), um coletor de dados de software livre para registro em log unificado. Os contêineres de Análise de Texto incluem um provedor de registro em log do Fluentd, que permite que seu contêiner grave o log e, opcionalmente, dados de métrica para um servidor do Fluentd.

A tabela a seguir descreve as definições de configuração com suporte de acordo com a seção `Fluentd`.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Host` | Cadeia de caracteres | O endereço IP ou nome do host DNS do servidor do Fluentd. |
| `Port` | Número inteiro | A porta do servidor do Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de eventos tiver sido enviado antes desse intervalo expirar, uma pulsação será enviada para o servidor do Fluentd. O valor padrão é 60.000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço do buffer de rede, em bytes, alocado para operações de envio. O valor padrão é 32.768 bytes (32 quilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo limite, em milissegundos, para estabelecer uma conexão SSL/TLS com o servidor do Fluentd. O valor padrão é 10.000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como false, esse valor será ignorado. |
| `UseTLS` | BOOLEAN | Indica se o contêiner deve usar SSL/TLS para se comunicar com o servidor do Fluentd. O valor padrão é falso. |

## <a name="logging-configuration-settings"></a>Definições de configuração de registro em log

As definições de configuração `Logging` gerenciam o suporte de registro em log do ASP.NET Core para o contêiner. Para o contêiner, você pode usar os mesmos valores e definições de configuração que usa para um aplicativo ASP.NET Core. Os provedores de registro em log a seguir são compatíveis com os contêineres de Análise de Texto:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  O provedor de registro em log `Console` do ASP.NET Core. Todos os valores padrão e as definições de configuração do ASP.NET Core para este provedor de log têm suporte.
* [Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  O provedor de registro em log `Debug` do ASP.NET Core. Todos os valores padrão e as definições de configuração do ASP.NET Core para este provedor de log têm suporte.
* Disco  
  O provedor de registro em log JSON. Este provedor de registro em log grava os dados de log na montagem de saída.  
  O provedor de registro em log `Disk` dá suporte às seguintes definições de configuração:  

  | NOME | Tipo de dados | DESCRIÇÃO |
  |------|-----------|-------------|
  | `Format` | Cadeia de caracteres | O formato de saída para arquivos de log.<br/> **Observação:** esse valor deve ser definido como `json` para habilitar o provedor de registro em log. Se esse valor for especificado sem especificar também uma montagem de saída ao instanciar um contêiner, ocorrerá um erro. |
  | `MaxFileSize` | Número inteiro | O tamanho máximo, em MB (megabytes), de um arquivo de log. Quando o tamanho do arquivo de log atual alcança ou excede esse valor, um novo arquivo de log é iniciado pelo provedor de registro em log. Se -1 for especificado, o tamanho do arquivo de log será limitado apenas pelo tamanho máximo do arquivo, se houver, para a montagem de saída. O valor padrão é 1. |

Para obter mais informações sobre como configurar o suporte de registro em log do ASP.NET Core, confira [Configuração do arquivo de definições](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Definições de configuração de montagens

Os contêineres do Docker fornecidos pelos contêineres de Análise de Texto são projetados para serem sem estado e imutáveis. Em outras palavras, os arquivos criados dentro de um contêiner são armazenados em uma camada de contêiner gravável, que persiste somente enquanto o contêiner está em execução e não podem ser acessados facilmente. Se esse contêiner for interrompido ou removido, os arquivos criados dentro desse contêiner junto com ele serão destruídos.

No entanto, já que eles são contêineres do Docker, você pode usar opções de armazenamento do Docker, como volumes e montagens de associação, para ler e gravar dados persistentes fora do contêiner, desde que o contêiner dê suporte a isso. Para obter mais informações sobre como especificar e gerenciar opções de armazenamento do Docker, confira [Gerenciar dados no Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Normalmente, você não precisará alterar os valores dessas definições de configuração. Em vez disso, você usará os valores especificados nessas definições de configuração como destinos ao especificar montagens de entrada e de saída para seu contêiner. Para obter mais informações sobre como especificar montagens de entrada e de saída, confira [Montagens de entrada e de saída](#input-and-output-mounts).

A tabela a seguir descreve as definições de configuração com suporte de acordo com a seção `Mounts`.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Input` | Cadeia de caracteres | O destino da montagem de entrada. O valor padrão é `/input`. |
| `Output` | Cadeia de caracteres | O destino da montagem de saída. O valor padrão é `/output`. |

### <a name="input-and-output-mounts"></a>Montagens de entrada e de saída

Por padrão, cada contêiner pode dar suporte a uma *montagem de entrada*, da qual o contêiner pode ler dados, bem como a uma *montagem saída*, na qual o contêiner pode gravar dados. No entanto, contêineres não precisam dar suporte a montagens de entrada ou de saída, sendo que cada contêiner pode usar montagens de entrada e de saída para fins específicos a cada contêiner, além das opções de registro em log compatíveis com os contêineres de Análise de Texto. A tabela a seguir lista o suporte a montagens de entrada e de saída para cada contêiner em contêineres de Análise de Texto.

| Contêiner | Montagem de Entrada | Montagem de Saída |
|-----------|-------------|--------------|
|[Extração de Frases-chave](#working-with-key-phrase-extraction) | Sem suporte | Opcional |
|[Detecção de Idioma](#working-with-language-detection) | Sem suporte | Opcional |
|[Análise de Sentimento](#working-with-sentiment-analysis) | Sem suporte | Opcional |

Você pode especificar uma montagem de entrada ou de saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/), usado para criar uma instância de um contêiner com base em uma imagem de contêiner baixada. Por padrão, a montagem de entrada usa `/input` como seu destino e a saída de montagem usa `/output` como seu destino. Qualquer opção de armazenamento do Docker disponível para o host de contêiner do Docker pode ser especificada na opção `--mount`.

Por exemplo, o comando a seguir define uma montagem de associação do Docker à pasta `D:\Output` no computador host como a montagem de saída, depois cria uma instância de um contêiner da imagem de contêiner de Análise de Sentimento, salvando arquivos de log no formato JSON na montagem de saída.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
