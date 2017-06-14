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
ms.date: 03/28/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 81f9a5a8e5ce8389c12a40eb02dd554fc140e009
ms.contentlocale: pt-br
ms.lasthandoff: 03/22/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Solução de problemas do Diagnóstico do Azure
Informações sobre solução de problemas relevantes ao uso do Diagnóstico do Azure. Para obter mais informações sobre o Diagnóstico do Azure, confira [Visão geral do Diagnóstico do Azure](azure-diagnostics.md).

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics não está iniciando
O Diagnostics é composto por dois componentes: um plug-in de agente convidado e o agente de monitoramento. Você pode verificar os arquivos de log **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** para obter informações sobre porque o diagnóstico não pode ser iniciado.  

Em uma função do Serviço de Nuvem, os arquivos de log para o plug-in do agente convidado estão localizados em:

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\
```

Em uma Máquina Virtual do Azure, os arquivos de log para o plug-in do agente convidado estão localizados em:

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.7.4.0\Logs\
```

A última linha dos arquivos de log contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

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
| -106 |Não é possível ler o arquivo de configuração do Diagnóstico.<p><p>Solução: causado por um arquivo de configuração que não passa pela validação do esquema. Então a solução é fornecer um arquivo de configuração que cumpre com o esquema. Para a máquina virtual do Azure, você pode localizar a configuração que é fornecida para a extensão de Diagnóstico na pasta *%SystemDrive%\WindowsAzure\Config* na VM. Abra o arquivo XML apropriado e pesquise por **Microsoft.Azure.Diagnostics** e, em seguida, pelo campo **xmlCfg** ou **WadCfg**. Se o campo WadCfg estiver presente, isso significará que a configuração está em JSON. Se o campo xmlCfg estiver presente, isso significará que a configuração está em XML e é codificada em base64. Você precisa decodificá-la para ver o XML que foi carregado pelo Diagnóstico. Para a função de Serviço de Nuvem, você pode encontrar a configuração que é fornecida para a extensão do Diagnóstico em C:\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\config.txt na VM. Os dados são base64 codificados, então você precisará [decodificá-los](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnóstico.<p> |
| -107 |O diretório de recursos que passa para o agente de monitoramento é inválido.<p><p>Este erro interno deve acontecer apenas se o agente de monitoramento é invocado manualmente, incorretamente, na VM.</p> |
| -108 |Não é possível converter o arquivo de configuração de Diagnóstico para o arquivo de configuração do agente de monitoramento.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -110 |Erro de configuração de diagnóstico geral.<p><p>Esse erro interno deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido. |
| -111 |Não foi possível iniciar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos. |
| -112 |Erro geral |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnósticos não estão registrados no Armazenamento do Azure
O diagnóstico do Azure armazena todos os dados no Armazenamento do Azure.

A causa mais comum da ausência de dados de evento é uma informação de conta de armazenamento definida incorretamente.

Solução: corrija seu arquivo de configuração do Diagnostics e reinstale o Diagnostics.
Se o problema persistir após a reinstalação da extensão do diagnóstico, talvez você tenha de depurar mais examinando quaisquer erros do agente de monitoramento. Antes dos dados de eventos serem carregados em sua conta de armazenamento, eles são armazenados em LocalResourceDirectory.
Esse caminho de diretório varia.

Para funções de Serviço de Nuvem:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Para máquinas virtuais:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se não existirem arquivos na pasta LocalResourceDirectory, o agente de monitoramento não poderá ser iniciado. Normalmente, isso é causado por um arquivo de configuração inválido, um evento que deve ser relatado em CommandExecution.log.

Se o Agente de Monitoramento estiver coletando dados de eventos com êxito, você verá arquivos .tsf para cada evento definido em seu arquivo de configuração. O Agente de Monitoramento registra os erros no arquivo MaEventTable.tsf. Para inspecionar o conteúdo desse arquivo, é possível usar o aplicativo tabel2csv para converter o arquivo .tsf em um arquivo de valores separados por vírgula (.csv):

Em uma Função do Serviço de Nuvem:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* em uma Função do Serviço de Nuvem é, geralmente, D:

Em uma Máquina Virtual:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Os comandos anteriores geram o arquivo de log *maeventtable.csv*, que pode ser aberto e verificado quanto a mensagens de falha.    

## <a name="diagnostics-data-tables-not-found"></a>Tabelas dos dados de diagnósticos não encontradas
As tabelas no armazenamento do Azure que contêm dados de diagnóstico do Azure são nomeadas usando o código a seguir:

```csharp
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
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
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

