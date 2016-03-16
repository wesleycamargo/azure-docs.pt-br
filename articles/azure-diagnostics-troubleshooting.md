<properties
	pageTitle="Solucionando problemas do Diagnóstico do Azure"
	description="Solucionar problemas ao usar o diagnóstico do Azure nos Serviços de Nuvem do Azure, Máquinas Virtuais e"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Solução de problemas do Diagnóstico do Azure
Informações sobre solução de problemas relevantes ao uso do Diagnóstico do Azure. Para obter mais informações sobre o Diagnóstico do Azure, confira [Visão geral do Diagnóstico do Azure](azure-diagnostics.md#cloud-services).

## Azure Diagnostics não está iniciando
O Diagnostics é composto por dois componentes: um plug-in de agente convidado e o agente de monitoramento.

Em uma função do Serviço de Nuvem, os arquivos de log para o plug-in do agente convidado estão localizados no arquivo:

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

Em uma Máquina Virtual do Azure, os arquivos de log para o plug-in do agente convidado estão localizados no arquivo:

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

Os seguintes códigos de erro são devolvidos pelo plug-in:

Código de Saída|Descrição
---|---
0|Sucesso.
-1|Erro genérico.
-2|Não foi possível carregar o arquivo rcf.<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM.
-3|Não é possível carregar o arquivo de configuração do Diagnóstico.<p><p>Solução: esse é o resultado de um arquivo de configuração não ser aprovado na validação de esquema. A solução é fornecer um arquivo de configuração que cumpre com o esquema.
-4|Outra instância do agente de monitoramento do Diagnostics já está usando o diretório de recurso local.<p><p>Solução: especifique um valor diferente para **LocalResourceDirectory**.
-6|O iniciador de plug-in do agente de convidado tentou iniciar o Diagnóstico com uma linha de comando inválida.<p><p>Esse é um erro interno que deve acontecer somente se o iniciador de plug-in do agente convidado for invocado manualmente e incorretamente na máquina virtual.
-10|O plug-in de Diagnostics saiu com uma exceção sem tratamento.
-11|O agente convidado não pôde criar o processo responsável por iniciar e monitorar o agente de monitoramento.<p><p>Solução: verifique se os recursos de sistema suficientes estão disponíveis para iniciar novos processos.<p>
-101|Argumentos inválidos ao chamar o plug-in de Diagnóstico.<p><p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM.
-102|O processo do plug-in não consegue inicializar-se sozinho.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos.
-103|O processo de plug-in é incapaz de iniciar sozinho. Especificamente, não consegue criar o objeto do agente.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos.
-104|Não foi possível carregar o arquivo rcf fornecido pelo agente convidado.<p><p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manual e incorretamente na VM.
-105|O plug-in de Diagnóstico não consegue abrir o arquivo de configuração do Diagnóstico.<p><p>Este é um erro interno que deve acontecer apenas se plug-in do Diagnóstico é invocado manual e incorretamente na VM.
-106|Não é possível ler o arquivo de configuração do Diagnóstico.<p><p>Solução: esse é o resultado de um arquivo de configuração não ser aprovado na validação de esquema. Então a solução é fornecer um arquivo de configuração que cumpre com o esquema. Você pode localizar o XML que é fornecido para a extensão Diagnostics na pasta *%SystemDrive%\\WindowsAzure\\Config* no VM. Abra o arquivo XML apropriado e pesquise por **Microsoft.Azure.Diagnostics** e, em seguida, por campo **xmlCfg**. Os dados são base64 codificados, então você precisará [decodificá-los](http://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo Diagnostics.<p>
-107|A passagem do diretório de recursos para o agente de monitoramento é inválida.<p><p>Este é um erro interno que deve acontecer apenas se o agente de monitoramento é invocado manual e incorretamente na VM.</p>
-108 |Não é possível converter o arquivo de configuração de Diagnóstico para o arquivo de configuração do agente de monitoramento.<p><p>Esse é um erro interno que deve acontecer somente se o plug-in de diagnóstico é invocado manualmente com um arquivo de configuração inválido.
-110|Erro de configuração de diagnóstico geral.<p><p>Esse é um erro interno que deve acontecer somente se o plug-in de diagnóstico é manualmente invocado com um arquivo de configuração inválido.
-111|Não foi possível iniciar o agente de monitoramento.<p><p>Solução: verifique se há recursos de sistema suficientes disponíveis para iniciar novos processos.
-112|Erro geral


## Os dados de diagnósticos não estão registrados no Armazenamento do Azure
O diagnóstico do Azure armazena todos os dados no Armazenamento do Azure.

A causa mais comum da ausência de dados de evento é uma informação de conta de armazenamento definida incorretamente.

Solução: corrija seu arquivo de configuração do Diagnostics e reinstale o Diagnostics. Se o problema persistir após a reinstalação da extensão do diagnóstico, talvez você tenha de depurar mais examinando quaisquer erros do agente de monitoramento. Antes dos dados de eventos serem carregados em sua conta de armazenamento, eles são armazenados em LocalResourceDirectory.

Para a Função do Serviço de Nuvem, o LocalResourceDirectory é:

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Para as Máquinas Virtuais, o LocalResourceDirectory é:

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se não existirem arquivos na pasta LocalResourceDirectory, o agente de monitoramento não poderá ser iniciado. Normalmente, isso é causado por um arquivo de configuração inválido, um evento que deve ser relatado em CommandExecution.log.

Se o Agente de Monitoramento estiver dados de eventos com êxito, você verá arquivos .tsf para cada evento definido em seu arquivo de configuração. O Agente de Monitoramento registra os erros no arquivo MaEventTable.tsf. Para inspecionar o conteúdo desse arquivo, é possível usar o aplicativo tabel2csv para converter o arquivo .tsf em um arquivo de valores separados por vírgula (.csv):

Em uma Função do Serviço de Nuvem:

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* em uma Função do Serviço de Nuvem é, geralmente, D:

Em uma Máquina Virtual:

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Os comandos acima geram o arquivo de log *maeventtable.csv*, que pode ser aberto e verificado quanto a mensagens de falha.


## Tabelas dos dados de diagnósticos não encontradas
As tabelas no armazenamento do Azure que contêm dados de diagnósticos do Azure são nomeadas usando o código abaixo:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Veja um exemplo:

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

Isso gerará 4 tabelas:

Evento|Nome da tabela
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5("prov1")+"1"
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-->