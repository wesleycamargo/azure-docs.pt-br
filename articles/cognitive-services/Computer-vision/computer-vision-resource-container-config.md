---
title: Configure os contêineres - Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Defina várias configurações para contêineres de Reconhecimento de Texto na Pesquisa Visual Computacional.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 48d3bc7ecdd66565372be8347897202cae3ec158
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579780"
---
# <a name="configure-recognize-text-containers"></a>Configure os contêineres de Reconhecimento de Texto

O API da Pesquisa Visual Computacional fornece o contêiner Recognize Text com uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente as configurações de armazenamento, registro e telemetria e segurança de seus contêineres.

## <a name="configuration-settings"></a>Definições de configuração

As definições de configuração nos contêineres do API da Pesquisa Visual Computacional são hierárquicas e todos os contêineres usam uma hierarquia compartilhada, com base na seguinte estrutura de nível superior:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Autenticação](#authentication-configuration-settings)
* [Cobrança](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Registro em log](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

É possível usar [variáveis de ambiente](#configuration-settings-as-environment-variables) ou [argumentos da linha de comandos](#configuration-settings-as-command-line-arguments) para especificar definições de configuração ao instanciar um contêiner de contêineres do API da Pesquisa Visual Computacional.

Os valores de variáveis de ambiente substituem os valores do argumento da linha de comandos, os quais, por sua vez, substituem os valores padrão da imagem do contêiner. Em outras palavras, se você especificar valores diferentes em uma variável de ambiente e um argumento de linha de comando para a mesma configuração, como `Logging:Disk:LogLevel`, instanciar um contêiner, o valor na variável de ambiente será usado pelo contêiner instanciado.

### <a name="configuration-settings-as-environment-variables"></a>Definições de configuração como variáveis de ambiente

Você pode usar a [sintaxe da variável de ambiente do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#environment-variables-configuration-provider) para especificar as configurações.

O contêiner lê variáveis de ambiente do usuário quando o contêiner é instanciado. Se existir uma variável de ambiente, o valor da variável de ambiente substituirá o valor padrão da configuração especificada. O benefício de usar variáveis de ambiente é que várias configurações podem ser definidas antes de instanciar contêineres e vários contêineres podem usar automaticamente o mesmo conjunto de configurações.

Por exemplo, os comandos a seguir usam uma variável de ambiente para configurar o nível de log do console para [LogLevel.Information](https://msdn.microsoft.com) e, em seguida, instancia um contêiner da imagem do contêiner de Reconhecimento de Texto. O valor da variável de ambiente substitui a configuração padrão.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Definições de configuração como argumentos da linha de comandos

Você pode usar a sintaxe do [argumento de linha de comando do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) para especificar as configurações.

Você pode especificar definições de configuração no parâmetro `ARGS` opcional do comando de [execução do docker](https://docs.docker.com/engine/reference/commandline/run/) usado para instanciar um contêiner a partir de uma imagem de contêiner do download feito. O benefício de usar argumentos de linha de comando é que cada contêiner pode usar um conjunto diferente e personalizado de definições de configuração.

Por exemplo, o comando a seguir instancia um contêiner da imagem do contêiner de reconhecimento de Texto e configura o nível de log do console como LogLevel.Information, substituindo a configuração padrão.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A `ApiKey` definição de configuração especifica a chave de configuração do recurso de pesquisa API da Pesquisa Visual Computacional no Azure usado para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser uma chave de configuração válida para o recurso do API da Pesquisa Visual Computacional especificado para a definição de configuração [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> As configurações de [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Definições de configuração do ApplicationInsights

As definições de configuração na seção `ApplicationInsights` permitem que você adicione o suporte de telemetria do [Aplicativo Azure AD Insights](https://docs.microsoft.com/azure/application-insights) ao seu contêiner. O Application Insights fornece um monitoramento detalhado do seu contêiner até o nível do código. Você pode monitorar facilmente seu contêiner quanto a disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar rapidamente erros em seu contêiner sem esperar que um usuário os denuncie.

A tabela a seguir descreve as definições de configuração com suporte sob o `ApplicationInsights` seção.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `InstrumentationKey` | Cadeia de caracteres | A chave de instrumentação da instância do Application Insights para o qual telemetria de dados para o contêiner são enviados. Para obter mais informações, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Definições de configuração de autenticação

As definições de configuração `Authentication` fornecem opções de segurança do Azure para o seu contêiner. Embora as definições de configuração nesta seção estejam disponíveis, o recipiente de reconhecimento de Texto não usa esta seção.

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A definição de configuração `Billing` especifica o URI de ponto de extremidade do recurso de Pesquisa Visual Computacional no Azure usado para medir as informações de cobrança para o contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de terminal válido para um recurso do API da Pesquisa Visual Computacional.

> [!IMPORTANT]
> As configurações de [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Definição de configuração do EULA

A configuração `Eula` indica que você aceitou a licença do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser definido como `accept`.

> [!IMPORTANT]
> As configurações de [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-configuration-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).

Os contêineres de Serviços Cognitivos estão licenciados sob [seu contrato](https://go.microsoft.com/fwlink/?linkid=2018657) que rege o uso do Azure. Se você não tiver um contrato existente que rege o uso do Azure, você concorda que o seu contrato que rege o uso do Azure é o [Contrato de Assinatura do Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), que incorpora os [Termos de Serviços Online ](https://go.microsoft.com/fwlink/?linkid=2018760). Para versões prévias, você também concorda com os [Termos de Uso Adicionais para Versões Prévias do Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Ao usar o contêiner, você concorda com estes termos.

## <a name="fluentd-configuration-settings"></a>Configurações do Fluentd

A seção `Fluentd` gerencia as definições de configuração do [Fluentd](https://www.fluentd.org), um coletor de dados de código aberto para o registro unificado. Os contêineres da API da Pesquisa Visual Computacional incluem um provedor de registro Fluentd que permite que seu contêiner grave dados de log e, opcionalmente, dados métricos em um servidor Fluentd.

A tabela a seguir descreve as definições de configuração com suporte sob o `Fluentd` seção.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Host` | Cadeia de caracteres | O endereço IP ou o nome do host DNS do servidor Fluentd. |
| `Port` | Número inteiro | A porta do servidor Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de evento tiver sido enviado antes que esse intervalo expire, uma pulsação será enviada ao servidor Fluentd. O valor padrão é 60.000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço de buffer de rede, em bytes, alocado para operações de envio. O valor padrão é 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo de limite, em milissegundos, para estabelecer uma conexão SSL/TLS com o servidor do Fluentd. O valor padrão é 10.000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como false, esse valor é ignorado. |
| `UseTLS` | BOOLEAN | Indica se o contêiner deve usar SSL / TLS para se comunicar com o servidor Fluentd. O valor padrão é falso. |

## <a name="logging-configuration-settings"></a>Definições de configuração de registro em log

As definições de configuração `Logging` gerenciam o suporte ao log do ASP.NET Core para o seu contêiner. Você pode usar as mesmas configurações e valores para o contêiner que puder para um aplicativo ASP.NET Core. Os seguintes provedores de registro são suportados pelos contêineres do API da Pesquisa Visual Computacional:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  O ASP.NET Core `Console` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.
* [Depurar](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  O ASP.NET Core `Debug` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.
* Disco  
  O provedor de log do JSON. Esse provedor de log grava dados de log na montagem de saída.  
  O provedor de registro `Disk` suporta as seguintes configurações:  

  | NOME | Tipo de dados | DESCRIÇÃO |
  |------|-----------|-------------|
  | `Format` | Cadeia de caracteres | O formato de saída para arquivos de log.<br/> **Observação:** Esse valor deve ser definido como `json` para habilitar o provedor de logs. Se esse valor for especificado sem especificar também uma montagem de saída durante a instanciação de um contêiner, ocorrerá um erro. |
  | `MaxFileSize` | Número inteiro | O tamanho máximo, em megabytes (MB), de um arquivo de log. Quando o tamanho do arquivo de log atual atende ou excede esse valor, um novo arquivo de log é iniciado pelo provedor de log. Se -1 for especificado, o tamanho do arquivo de log será limitado apenas pelo tamanho máximo do arquivo, se houver, para a montagem de saída. O valor padrão é 1. |

Para obter mais informações sobre como configurar o suporte ao registro em log do ASP.NET Core, consulte [Fazendo logon no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#configuration).

## <a name="mounts-configuration-settings"></a>Monta definições de configuração

Os contêineres Docker fornecidos pela API da Pesquisa Visual Computacional são projetados para serem stateless e imutáveis. Em outras palavras, os arquivos criados dentro de um contêiner são armazenados em uma camada de contêiner gravável, que persiste apenas enquanto o contêiner está em execução e não pode ser acessado facilmente. Se esse contêiner for interrompido ou removido, os arquivos criados dentro desse contêiner serão destruídos.

No entanto, como eles são contêineres do Docker, você pode usar as opções de armazenamento do Docker, como volumes e montagens de ligação, para ler e gravar dados persistentes fora do contêiner, se o contêiner suportar. Para obter mais informações sobre como especificar e gerenciar opções de armazenamento do Docker, consulte [Gerenciar dados no Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Você normalmente não precisará alterar os valores dessas configurações. Em vez disso, você usará os valores especificados nessas configurações como destinos ao especificar montagens de entrada e saída para seu contêiner. Para obter mais informações sobre como especificar montagens de entrada e saída, consulte [Entradas de entrada e saída](#input-and-output-mounts).

A tabela a seguir descreve as definições de configuração com suporte sob o `Mounts` seção.

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Input` | Cadeia de caracteres | O destino de montagem de entrada. O valor padrão é `/input`. |
| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. |

### <a name="input-and-output-mounts"></a>Montagens de entrada e saídas

Por padrão, cada contêiner pode suportar uma *montagem de entrada*, a partir da qual o contêiner pode ler dados, e um *montagem de saída*, para o qual o contêiner pode gravar dados. Os contêineres não precisam suportar montagens de entrada ou saída, e cada contêiner pode usar montagens de entrada e saída para fins específicos do contêiner, além das opções de registro suportadas pelos contêineres do API da Pesquisa Visual Computacional.

O contêiner de Reconhecimento de Texto não suporta montagens de entrada e, opcionalmente, suporta montagens de saída.

Você pode especificar uma montagem de entrada ou montagem de saída especificando a opção `--mount` no comando de [execução do docker](https://docs.docker.com/engine/reference/commandline/run/) usado para instanciar um contêiner a partir de uma imagem de contêiner transferida para fazer o download. Por padrão, a montagem de entrada usa `/input` como seu destino e a montagem de saída usa `/output` como seu destino. Qualquer opção de armazenamento do Docker disponível para o host do contêiner Docker pode ser especificada na opção `--mount`.

Por exemplo, o comando a seguir define uma montagem de ligação do Docker para a pasta `D:\Output` na máquina host como a montagem de saída e, em seguida, instancia um contêiner da imagem do contêiner de reconhecimento de Texto, salvando arquivos de log no formato JSON na montagem de saída.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
