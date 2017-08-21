---
title: "Solução de problemas do Diagnóstico do Azure | Microsoft Docs"
description: "Solucionar problemas ao usar o diagnóstico do Azure no Service Fabric, Serviços de Nuvem e Máquinas Virtuais do Azure."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: df53e92b877b4790bb700f176a1988d265ec4678
ms.contentlocale: pt-br
ms.lasthandoff: 07/13/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Solução de problemas do Diagnóstico do Azure
Informações sobre solução de problemas relevantes ao uso do Diagnóstico do Azure. Para obter mais informações sobre o Diagnóstico do Azure, confira [Visão geral do Diagnóstico do Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componentes lógicos
**Iniciador do plug-in de diagnóstico (DiagnosticsPluginLauncher.exe)**: inicia a extensão de Diagnóstico do Microsoft Azure. Serve como o processo do ponto de entrada.

**Plug-in de diagnóstico (DiagnosticsPlugin.exe)**: processo principal que é iniciado pelo iniciador acima e configura o Agente de monitoramento, inicia ele e gerencia seu tempo de vida. 

**Agente de monitoramento (processos do MonAgent\*.exe)**: esses processos fazem a maior parte do trabalho; ou seja, monitoramento, coleta e transferência de dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de log/artefato
Estes são os caminhos para alguns logs e artefatos importantes. Vamos manter as referências a eles em todo o restante do documento:
### <a name="cloud-services"></a>Serviços de Nuvem
| Artefato | Caminho |
| --- | --- |
| **Arquivo de configuração de Diagnóstico do Microsoft Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Arquivos de log** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Armazenamento local para dados de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Arquivo de configuração do Agente de monitoramento** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão do Diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Caminho do utilitário de coleta de log** | %SystemDrive%\Packages\GuestAgent\ |
| **Arquivo de log MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas Virtuais
| Artefato | Caminho |
| --- | --- |
| **Arquivo de configuração de Diagnóstico do Microsoft Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Arquivos de log** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **Armazenamento local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Arquivo de configuração do Agente de monitoramento** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Arquivo de status** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Pacote de extensão do Diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Caminho do utilitário de coleta de log** | C:\WindowsAzure\Packages |
| **Arquivo de log MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics não está iniciando
Analise os arquivos **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** do local dos arquivos de log para obter informações sobre porque o diagnóstico não pode ser iniciado. 

Se esses logs indicam `Monitoring Agent not reporting success after launch`, isso significa que houve uma falha ao iniciar MonAgentHost.exe. Examine os logs em busca disso no local indicado para `MonAgentHost log file` na seção acima.

A última linha dos arquivos de log contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se você encontrar um código de saída **negativo**, consulte a [tabela de códigos de saída](#azure-diagnostics-plugin-exit-codes) em [Referências](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnósticos não estão registrados no Armazenamento do Azure
Determine se nenhum dado é exibido ou apenas alguns dos dados não estão sendo exibidos.

### <a name="diagnostics-infrastructure-logs"></a>Logs de infraestrutura de diagnósticos
Logs da Infraestrutura de diagnóstico é onde o diagnóstico do Azure registra quaisquer erros que são encontrados. Verifique se você habilitou ([como?](#how-to-check-diagnostics-extension-configuration)) a captura de logs da Infraestrutura de diagnóstico em sua configuração e procure rapidamente erros relevantes que aparecem na tabela `DiagnosticInfrastructureLogsTable` em sua conta de armazenamento configurada.

### <a name="no-data-is-showing-up"></a>Nenhum dado está sendo exibido
A causa mais comum da completa ausência de dados de evento é uma informação de conta de armazenamento definida incorretamente.

Solução: corrija sua configuração do Diagnóstico e o reinstale.

Se a conta de armazenamento está configurada corretamente na área de trabalho remota do computador, verifique se DiagnosticsPlugin.exe e MonAgentCore.exe estão em execução. Se eles não estiverem em execução, siga [Diagnóstico do Azure não está iniciando](#azure-diagnostics-is-not-starting). Se estiver executando os processos, vá para [Os dados estão sendo capturados localmente](#is-data-getting-captured-locally) e siga este guia.

### <a name="part-of-the-data-is-missing"></a>Parte dos dados está ausente
Se você estiver obtendo alguns dados, mas não outros. Isso significa que a coleta de dados/transferência de pipeline está definida corretamente. Siga as subseções aqui para restringir qual é o problema:
#### <a name="is-collection-configured"></a>A coleção foi configurada: 
A Configuração de diagnóstico contém a parte que instrui para um determinado tipo de dados a ser coletado. [Examine a configuração](#how-to-check-diagnostics-extension-configuration) para certificar-se de que você não está procurando dados que foram configurados para a coleta.
#### <a name="is-the-host-generating-data"></a>O host está gerando dados:
- **Contadores de desempenho**: abra o perfmon e verifique o contador.
- **Logs de rastreamento**: a área de trabalho remota na VM e adicione um TextWriterTraceListener ao arquivo de configuração do aplicativo.  Consulte http://msdn.microsoft.com/pt-br/library/sk36c28t.aspx para configurar o ouvinte de texto.  Verifique se o elemento `<trace>` tem `<trace autoflush="true">`.<br />
Se você não vir os logs de rastreamento sendo gerados, execute [Mais sobre logs de rastreamento ausentes](#more-about-trace-logs-missing).
- **Rastreamentos do ETW**: a área de trabalho remota para a VM e instale o PerfView.  No PerfView, execute Arquivo -> Comando de usuário -> Escutar etwprovder1,etwprovider2,etc.  Observe que o comando Escutar diferencia letras maiúsculas de minúsculas e não pode haver espaços entre a lista separada por vírgulas dos provedores do ETW.  Se o comando falhar na execução, você pode clicar no botão de “Log” no canto inferior direito da ferramenta Perfview para ver o que tentou executar e qual foi o resultado.  Supondo que a entrada está correta, em seguida, uma nova janela será exibida e em alguns segundos você começará a ver os rastreamentos do ETW.
- **Logs de evento**: área de trabalho remota na VM. Abra `Event Viewer` e verifique se os eventos existem.
#### <a name="is-data-getting-captured-locally"></a>Os dados estão sendo capturados localmente:
Em seguida, certifique-se de que os dados estão sendo capturados localmente.
Os dados são armazenados localmente em arquivos `*.tsf` no [armazenamento local para dados de diagnóstico](#log-artifacts-path). Diferentes tipos de logs coletados em diferentes arquivos `.tsf`. Os nomes são semelhantes aos nomes de tabela no armazenamento do Azure. Por exemplo `Performance Counters` é coletado no `PerformanceCountersTable.tsf`, Logs de eventos coletados no `WindowsEventLogsTable.tsf`. Use as instruções na seção [Extração de log local](#local-log-extraction) para abrir os arquivos de coleta local e verifique se você os vê sendo coletados no disco.

Se não vir logs sendo coletados localmente e já tiver verificado que o host está gerando dados, você provavelmente tem um problema de configuração. Analise a configuração com cuidado para a seção apropriada. Também analise a configuração gerada para MonitoringAgent [MaConfig.xml](#log-artifacts-path) e verifique se há alguma seção que descreve a origem do log relevante e se ele não foi perdido na conversão entre a configuração de diagnóstico do Azure e a configuração do agente de monitoramento.
#### <a name="is-data-getting-transferred"></a>Os dados estão sendo transferidos:
Se você verificou se os dados estão sendo capturados localmente, mas ainda não estiver visível na sua conta de armazenamento: 
- Primeiramente, certifique-se de que você forneceu uma conta de armazenamento correta e se não tiver substituído as chaves etc para a conta de armazenamento determinada. Para serviços de nuvem, às vezes, podemos ver que as pessoas não atualizam seus `useDevelopmentStorage=true`.
- Se a conta de armazenamento fornecida está correta. Certifique-se de que você não tem algumas restrições de rede que não permitem que os componentes alcancem os pontos de extremidade de armazenamento público. Uma maneira de fazer isso é tornar a área de trabalho remota no computador e tente gravar algo na mesma conta de armazenamento por conta própria.
- Por fim, você pode tentar e ver quais falhas estão sendo relatadas pelo Agente de monitoramento. O Agente de monitoramento grava logs no `maeventtable.tsf` localizado no [armazenamento local para dados de diagnóstico](#log-artifacts-path). Siga as instruções na seção [Extração de log local](#local-log-extraction) para abrir este arquivo e experimentar e descobrir se há `errors` indicando falhas para ler os arquivos locais ou de gravação para o armazenamento.

### <a name="capturing--archiving-logs"></a>Capturando/arquivando logs
Você seguiu as etapas acima, mas não foi possível descobrir o que estava errado e está pensando sobre como entrar em contato com o suporte. A primeira coisa que eles podem solicitar a você é para coletar logs do seu computador. Você pode poupar tempo fazendo isso você mesmo. Execute o utilitário `CollectGuestLogs.exe` no [Caminho do utilitário de coleta de log](#log-artifacts-path) e ele gerará um arquivo zip com todos os logs relevantes do Azure na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Tabelas dos dados de diagnósticos não encontradas
As tabelas no armazenamento do Azure que contêm eventos do ETW são nomeadas usando o código a seguir:

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Aqui está um exemplo:

```XML
        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Isso gera quatro tabelas:

| Evento | Nome da tabela |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5("prov1")+"1" |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a Configuração da extensão de diagnóstico
A maneira mais fácil de verificar sua configuração de extensão é navegar até http://resources.azure.com, vá até a máquina virtual ou serviço de nuvem no qual a extensão do Diagnóstico do Azure (IaaSDiagnostics/PaaDiagnostics) em questão está.

Como alternativa, torne a área de trabalho remota no computador e analise o arquivo de Configuração do diagnóstico do Azure descrito na seção apropriada [aqui](#log-artifacts-path).

Em ambos os casos, pesquise por **Microsoft.Azure.Diagnostics** e, em seguida, pelo campo **xmlCfg** ou **WadCfg**. 

No caso de máquinas virtuais, se o campo WadCfg estiver presente, isso significa que a configuração está em JSON. Se o campo xmlCfg estiver presente, isso significará que a configuração está em XML e é codificada em base64. Você precisa [decodificá-la](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnóstico.

Para a função do Serviço de nuvem, se você escolher a configuração do disco, os dados são base64 codificados, então você precisará [decodificá-los](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnóstico.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída do plug-in do Diagnóstico do Microsoft Azure
O plug-in retorna os seguintes códigos de saída:

| Código de Saída | Descrição |
| --- | --- |
| 0 |Sucesso. |
| -1 |Erro genérico. |
| -2 |Não foi possível carregar o arquivo rcf.<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM. |
| -3 |Não é possível carregar o arquivo de configuração do Diagnóstico.<p><p>Solução: causado por um arquivo de configuração que não passa pela validação do esquema. A solução é fornecer um arquivo de configuração que cumpre com o esquema. |
| -4 |Outra instância do agente de monitoramento do Diagnostics já está usando o diretório de recurso local.<p><p>Solução: especifique um valor diferente para **LocalResourceDirectory**. |
| -6 |O iniciador de plug-in do agente de convidado tentou iniciar o Diagnóstico com uma linha de comando inválida.<p><p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM. |
| -10 |O plug-in de Diagnostics saiu com uma exceção sem tratamento. |
| -11 |O agente convidado não pôde criar o processo responsável por iniciar e monitorar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos.<p> |
| -101 |Argumentos inválidos ao chamar o plug-in de Diagnóstico.<p><p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM. |
| -102 |O processo do plug-in não consegue inicializar-se sozinho.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -103 |O processo do plug-in não consegue inicializar-se sozinho. Especificamente, não consegue criar o objeto do agente.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -104 |Não foi possível carregar o arquivo rcf fornecido pelo agente convidado.<p><p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM. |
| -105 |O plug-in de Diagnóstico não consegue abrir o arquivo de configuração do Diagnóstico.<p><p>Esse erro interno deve acontecer apenas se plug-in do Diagnóstico é invocado manual e incorretamente na VM. |
| -106 |Não é possível ler o arquivo de configuração do Diagnóstico.<p><p>Solução: causado por um arquivo de configuração que não passa pela validação do esquema. Então a solução é fornecer um arquivo de configuração que cumpre com o esquema. Consulte [Como verificar a Configuração da extensão de diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |O diretório de recursos que passa para o agente de monitoramento é inválido.<p><p>Este erro interno deve acontecer apenas se o agente de monitoramento é invocado manualmente, incorretamente, na VM.</p> |
| -108 |Não é possível converter o arquivo de configuração de Diagnóstico para o arquivo de configuração do agente de monitoramento.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -110 |Erro de configuração de diagnóstico geral.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -111 |Não foi possível iniciar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de log local
O agente de monitoramento coleta logs e artefatos como arquivos `.tsf`. O arquivo `.tsf` não está legível, mas você pode convertê-lo em um `.csv` da seguinte maneira: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo arquivo chamado `<relevantLogFile>.csv` será criado no mesmo caminho correspondente do arquivo `.tsf`.

**OBSERVAÇÃO**: você só precisa executar este utilitário contra o arquivo tsf principal (por exemplo, PerformanceCountersTable.tsf). Os arquivos complementares (por exemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002. tsf, etc.) serão processados automaticamente.

### <a name="more-about-trace-logs-missing"></a>Mais informações sobre Logs de rastreamento ausentes

**Observação:** isso principalmente se aplica aos serviços de nuvem apenas, a menos que você tenha configurado o DiagnosticsMonitorTraceListener em um aplicativo em execução na sua VM IaaS. 

- Certifique-se de que o DiagnosticMonitorTraceListener está configurado no web.config ou app.config.  Ele é configurado por padrão em projetos do serviço de nuvem, mas alguns clientes comentam sobre ele, o que fará com que as instruções de rastreamento não sejam coletadas pelo diagnóstico. 
- Se os logs não estiverem sendo gravados a partir do OnStart ou do método Executar, certifique-se de que o DiagnosticMonitorTraceListener está no app.config.  Por padrão, ele está no web.config, mas ele se aplica somente a execução de códigos dentro do w3wp.exe; portanto, você precisa dele no app.config para capturar a execução de rastreamentos no WaIISHost.exe.
- Certifique-se de que você está usando Diagnostics.Trace.TraceXXX em vez de Diagnostics.Debug.WriteXXX.  As instruções de depuração serão removidas a partir de uma compilação de versão.
- Certifique-se de que o código compilado, na verdade, tem as linhas Diagnostics.Trace (use Reflector, ildasm ou ILSpy para verificar).  Os comandos Diagnostics.Trace são removidos do binário compilado, a menos que você use o símbolo de compilação condicional TRACE.  Se estiver usando msbuild para compilar o projeto, então esse é um problema comum.

## <a name="known-issues-and-mitigations"></a>Problemas e mitigações conhecidos
Aqui está uma lista dos problemas conhecidos com mitigações conhecidas:

**1. Dependência do .NET 4.5:**

WAD tem uma dependência de tempo de execução no .NET framework 4.5 ou superior. No momento da gravação dele, todos os computadores provisionados para os serviços de nuvem, bem como todas as imagens das máquinas virtuais oficiais do Azure, têm o .NET 4.5 ou superior instalado. No entanto, ainda é possível recair em uma situação na qual você tente executar WAD em um computador que não tem o .NET 4.5 ou superior. Isso acontece quando você cria seu computador fora de uma imagem antiga ou instantâneo; ou traz seu próprio disco personalizado.

Isso geralmente se manifesta como um código de saída **255** ao executar o DiagnosticsPluginLauncher.exe. A falha ocorre devido à exceção sem tratamento: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigação:** instala o .NET 4.5 ou superior no seu computador.

**2. Dados de Contadores de desempenho estão disponíveis no armazenamento, mas não são mostrados no portal**

A experiência do portal de máquinas virtuais mostra determinados contadores de desempenho por padrão. Se você não os vir e souber que os dados estão sendo gerados porque ele está disponível no armazenamento. Verificação:
- Se os dados no armazenamento possuem nomes do contador em inglês. Se os nomes do contador não estão em inglês, o gráfico de métrica do portal não poderá reconhecê-los.
- Se você estiver usando caracteres curinga (\*) em seus nomes do contador de desempenho, o portal não poderá correlacionar o contador coletado e configurado.

**Mitigação**: altera o idioma do computador para inglês para as contas do sistema. Painel de Controle -> Região -> Administrativos -> Configurações de Cópia -> desmarque "Bem-vindo às contas de tela e do sistema" para que o idioma personalizado não seja aplicado à conta do sistema. Verifique também se você não usa caracteres curinga, se quiser que o portal seja sua experiência de consumo principal.
