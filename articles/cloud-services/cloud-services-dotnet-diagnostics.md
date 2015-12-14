<properties 
	pageTitle="Como usar os diagnósticos (.NET)| Microsoft Azure" 
	description="Saiba como usar dados de diagnóstico no Azure para depuração, medição do desempenho, monitoramento, análise de tráfego e mais." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/25/2015" 
	ms.author="robb"/>



# Habilitando o Diagnostics nos Serviços de nuvem do Azure e Máquinas virtuais

O Diagnóstico do Azure 1.2 e 1.3 permite coletar dados de diagnóstico de uma função de trabalho, função Web ou máquina virtual em execução no Azure. Este guia descreve como usar o Diagnóstico do Azure 1.2 e 1.3. Para obter orientações detalhadas adicionais sobre como criar um log e uma estratégia de rastreamento e usar diagnóstico e outras técnicas para solucionar problemas, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][].

## Visão geral

O Diagnóstico do Azure 1.2 e 1.3 são extensões do Azure que permitem que você colete dados de telemetria de diagnóstico de uma função de trabalho, função Web ou a máquina virtual em execução no Azure. Os dados de telemetria são armazenados na conta de armazenamento do Azure e podem ser usados para depuração e solução de problemas, medição de desempenho, monitoramento de uso de recursos, análise de tráfego e planejamento da capacidade e auditoria.

O Diagnóstico do Azure 1.2 é para uso com SDKs do Azure para .NET 2.4 e anterior. O Diagnóstico do Azure 1.3 é para uso com SDKs do Azure para .NET 2.5 e posterior.

Se você usou a versão 1.0 do Diagnóstico no passado, existem três diferenças notáveis comparado ao Diagnóstico 1.2 e 1.3:

1.	O Diagnóstico 1.2 e 1.3 pode ser implantado em máquinas virtuais, além de serviços de nuvem.
2.	O Diagnostics 1.0 é parte do SDK do Azure e é implantado quando você implanta seu serviço de nuvem. O Diagnóstico 1.2 e 1.3 é uma extensão e é implantado separadamente a partir de sua implantação do serviço de nuvem.
3.	O Diagnóstico 1.2 e 1.3 habilita a coleta de eventos EventSource do ETW e .NET.

Para obter uma comparação mais detalhada, consulte [Comparando versões de diagnóstico do Azure][] no final deste artigo.

O Diagnóstico do Azure pode coletar os seguintes tipos de telemetria:

Fonte de dados|Descrição
---|---
Logs IIS|Informações sobre sites IIS.
Logs de infraestrutura de diagnóstico do Azure|Informações sobre o próprio Diagnostics.
Logs de solicitação com falha IIS|Informações sobre solicitações falhas para um site IIS ou aplicativo.
Log de eventos do Windows|Informações enviadas ao sistema de registro de evento do Windows.
Contadores de desempenho|Sistema Operacional e contadores de desempenho personalizados.
Despejos de falhas|Informações sobre o estado do processo no evento de uma falha do aplicativo.
Logs de erros personalizados|Logs criados por seu aplicativo ou serviço.
NET EventSource |Os eventos gerados pelo seu código usando a <a href="http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe do EventSource</a> de .NET
Manifesto com base no ETW|Eventos ETW gerados por qualquer processo.

## Como habilitar o Diagnostics em uma Função do Trabalho

Este passo a passo descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando o .NET EventSource Class. O Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-los em uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio permite automaticamente O Diagnóstico 1.0 como parte da solução em SDKs do Azure para .NET 2.4 e versões anteriores. As instruções a seguir descrevem o processo de criação da função de trabalho, desabilitando o Diagnóstico 1.0 por meio da solução e implantando o Diagnóstico 1.2 ou 1.3 para sua função de trabalho.

### Pré-requisitos
Esse artigo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma conta do Azure, será possível se inscrever para um [Teste Gratuito][]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][].

### Etapa 1: criar uma função de trabalho
1.	Inicie o **Visual Studio 2013**.
2.	Crie um novo projeto do **Serviço de Nuvem do Azure** no modelo de **Nuvem** que se destina ao .NET Framework 4.5. Atribua o nome "WadExample" ao projeto e clique em Ok.
3.	Selecione **Função de Trabalho** e clique em Ok. O projeto será criado. 
4.	No **Gerenciador de Soluções**, clique duas vezes no arquivo de propriedades **WorkerRole1**.
5.	Na guia **Configuração**, desmarque **Habilitar o diagnóstico** para desabilitar o Diagnostics 1.0 (SDK 2.4 do Azure mais recentes).
6.	Compile sua solução para verificar que você não tem erros.

### Etapa 2: Instrumentalizar seu código
Substitua os conteúdos do WorkerRole.cs pelo código a seguir. A classe SampleEventSourceWriter, herdada da [classe EventSource][], implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
	{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### Etapa 3: Implantar sua Função de Trabalho
1.	Implante sua função de trabalho para o Azure dentro do Visual Studio selecionando o projeto **WadExample** no Gerenciador de Soluções e, em seguida, **Publicar** no menu **Compilar**.
2.	Escolha sua assinatura.
3.	Na caixa de diálogo **Configurações do Microsoft Azure Publish**, selecione **Criar Novo...**.
4.	Na caixa de diálogo **Criar Serviço de Nuvem e Conta de Armazenamento**, insira um **Nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5.	Defina o **Ambiente** para **De Preparo**.
6.	Modifique quaisquer outras **Configurações** conforme apropriado, e clique em **Publicar**.
7.	Após a implantação ter sido concluída, verifique no portal clássico do Azure se seu serviço de nuvem está em estado de **Execução**.

### Etapa 4: Criar seu arquivo de configuração do Diagnostics e instalar a extensão
1.	Baixe a definição do esquema do arquivo de configuração pública ao executar o seguinte comando PowerShell:
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Adicione um arquivo XML ao seu projeto **WorkerRole1** ao clicar com o botão direito do mouse no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item…** -> **Itens Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml".

	![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela de **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em **…** na propriedade **Esquemas**. Clique no botão **Adicionar…** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4.	Substitua os conteíudos do arquivo de configuração WadExample.xml com o seguinte XML e salve o arquivo. Este arquivo de configuração define dois contadores de desempenho a serem coletados: um relacionado à utilização da CPU e um ao uso de memória. Após a configuração, defina os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Etapa 5: instalar o Diagnostics em sua Função de Trabalho
Os cmdlets do PowerShell para gerenciar o diagnóstico em uma função web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.	Abra o PowerShell do Azure.
2.	Execute o script para instalar o Diagnostics na sua função de trabalho (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexample):

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1


### Etapa 6: Examinar os dados de telemetria
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o serviço de nuvem foi executado por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## Como habilitar o Diagnostics em uma Máquina Virtual

Esse passo a passo descreve como instalar remotamente o Diagnostics em uma máquina virtual do Azure a partir de um computador de desenvolvedor. Você também aprende como implementar um aplicativo que executa nesta máquina virtual do Azure e emite dados de telemetria usando o .NET [EventSource Class][]. O Diagnóstico do Azure é usado para coletar a telemetria e armazená-la em uma conta de armazenamento do Azure.

### Pré-requisitos
Esse passo a passo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma conta do Azure, será possível se inscrever para um [Teste Gratuito][]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][].

### Etapa 1: Criar uma máquina virtual
1.	No computador do desenvolvedor, inicie o Visual Studio 2013.
2.	No **Gerenciador de Servidores** do Visual Studio, expanda o **Azure**, clique com botão direito do mouse em **Máquinas Virtuais** e, em seguida, selecione **Criar Máquina Virtual**.
3.	Selecione sua assinatura do Azure na caixa de diálogo **Escolher uma Assinatura** e clique em **Avançar**.
4.	Selecione **Windows Server 2012 R2 Datacenter, novembro de 2014** na caixa de diálogo **Selecionar uma Imagem da Máquina Virtual** e clique em **Avançar**.
5.	Nas **Configurações Básicas da Máquina Virtual**, defina o nome da máquina virtual para "wadexample". Defina o nome de usuário e senha do Administrador clique em **Avançar**.
6.	Na caixa de diálogo **Configurações de Serviço de Nuvem**, crie um novo serviço de nuvem chamado "wadexampleVM". Crie uma nova conta de Armazenamento chamada "wadexample" e clique em **Avançar**.
7.	Clique em **Criar**.

### Etapa 2: Criar o seu aplicativo
1.	No computador do desenvolvedor, inicie o Visual Studio 2013.
2.	Crie um novo aplicativo de console do Visual C# que se destina ao .NET Framework 4.5. Atribua o nome "WadExampleVM" ao projeto. ![CloudServices\_diag\_new\_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Substitua os conteúdos do Program.cs pelo código a seguir. A classe **SampleEventSourceWriter** implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método WriteEvent define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");
            
            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	Salve o arquivo e selecione **Compilar Solução** a partir do menu **Compilar** para compilar seu código.


### Etapa 3: Implantar seu aplicativo
1.	Clique com o botão direito do mouse no projeto **WadExampleVM** no **Gerenciador de Soluções** e selecione **Abrir Pasta no Explorador de Arquivos**.
2.	Navegue para a pasta *bin\\Debug* e copie todos os arquivos (WadExampleVM.*)
3.	No **Gerenciador de Servidores**, clique com o botão direito do mouse na máquina virtual e selecione **Conectar usando a Área de Trabalho Remota**.
4.	Após conectado ao VM, crie uma pasta com o nome de WadExampleVM e cole seus arquivos do aplicativo na pasta.
5.	Inicie o aplicativo WadExampleVM.exe. Você verá uma janela de console em branco.

### Etapa 4: Criar sua configuração do Diagnostics e instalar a extensão
1.	Baixe a definição do esquema do arquivo de configuração pública para seu computador de desenvolvimento ao executar o seguinte comando PowerShell:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Abra um novo arquivo XML no Visua Studio, seja em um projeto que você já abriu, ou em uma instância do Visual Studio com nenhum projeto aberto. No Visual Studio, selecione **Adicionar** -> **Novo Item…** -> **Itens do Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml"
3.	Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela de **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em **…** na propriedade **Esquemas**. Clique no botão **Adicionar…** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4.	Substitua os conteíudos do arquivo de configuração WadExample.xml com o seguinte XML e salve o arquivo. Este arquivo de configuração define dois contadores de desempenho a serem coletados: um relacionado à utilização da CPU e um ao uso de memória. Após a configuração, defina os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Etapa 5: instalar remotamente o Diagnostics na sua Máquina Virtual do Azure
Os cmdlets do PowerShell para gerenciar Diagnostics em uma máquina virtual são: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1.	No computador do desenvolvedor, abra o PowerShell do Azure.
2.	Execute o script para instalar remotamente o Diagnostics no seu VM (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexamplevm):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### Etapa 6: Examinar os dados de telemetria
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o VM foi executado por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada. ![CloudServices\_diag\_wadexamplevm\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

## Esquema de arquivo de configuração

O arquivo de configuração do Diagnostics define valores que são usados para inicializar as definições de configurações do diagnóstico quando o monitor de diagnóstico inicia. Um exemplo de arquivo de configuração e documentação detalhada sobre seu esquema está localizado aqui: [Esquema de configuração do Azure Diagnostics 1.2][].

## Solucionar problemas

### Azure Diagnostics não está iniciando
O Diagnostics é composto por dois componentes: um plug-in de agente convidado e o agente de monitoramento. Os arquivos de log para o plug-in do agente convidado estão localizados no arquivo:

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\\CommandExecution.log

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


### Os dados do Diagnostics não estão conectados ao armazenamentodata
A causa mais comum da ausência de dados de evento é uma informação de conta de armazenamento definida incorretamente.

Solução: corrija seu arquivo de configuração do Diagnostics e reinstale o Diagnostics. Antes dos dados de eventos serem carregados em sua conta de armazenamento, eles são armazenados na pasta. Consulte acima para obter detalhes sobre **LocalResourceDirectory**.

Se não existirem arquivos nesta pasta, o agente de monitoramento é incapaz de iniciar. Normalmente, isto é causado por um arquivo de configuração inválido e deve ser relatado em CommandExecution.log. Se o Agente de Monitoramento estiver dados de eventos com êxito, você verá arquivos .tsf para cada evento definido em seu arquivo de configuração.

O Agente de Monitoramento registra quaisquer erros que ocorrerem no arquivo MaEventTable.tsf. Para inspecionar o conteúdo deste arquivo, execute o comando a seguir:

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.3.0.0\Monitor\x64\table2csv maeventtable.tsf

A ferramenta gera um arquivo com o nome de maeventtable.csv, que você pode abrir e inspecionar os logs, para verificar as falhas.


## Perguntas frequentes
A seguir estão algumas perguntas frequentes e suas respostas:

**P.** Como eu atualizo a minha solução do Visual Studio a partir do Azure Diagnostics 1.0 para Azure Diagnostics 1.1?

**A.** Atualizar a solução do Visual Studio do Diagnostics 1.0 para o Diagnostics 1.1 (ou posterior) é um processo manual: - Desabilite o diagnóstico em sua solução do Visual Studio para impedir que o Diagnostics 1.0 seja implantado com a sua função - Se o código usar o ouvinte de rastreamento, você precisará modificar o código para usar o .NET EventSource. Diagnóstico 1.1 e posteriores não dão suporte para o ouvinte de rastreamento. -Modifique seu processo de implantação para instalar a extensão do Diagnostics 1.1

**P.** Se eu já tiver instalado a extensão do Diagnóstico 1.1 em minha função ou VM, como eu atualizo para o Diagnóstico 1.2 ou 1.3?

**A.** Se você tiver especificado "–Versão "1.*” quando você instalou o Diagnostics 1.1, a próxima vez que sua função reiniciar ou o VM reiniciar, será atualizado automaticamente para a versão mais recente correspondendo à versão regular "1.*" Se tiver especificado "–Versão "1.1"" quando instalou o Diagnostics 1.1, você pode atualizar para uma versão mais recente executando novamente o cmdlet Set- e especificando a versão que deseja instalar.

**P.** Como as tabelas são nomeadas?

**A.** As tabelas são nomeadas de acordo com o seguinte:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Aqui está um exemplo:

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

## Comparando as versões de diagnóstico do Azure

A tabela a seguir compara os recursos com suporte pelas versões de Diagnóstico do Azure 1.0 e 1.1/1.2/1.3:

Tipos de função suportados|Diagnostics 1.0|Diagnóstico 1.1/1.2/1.3
---|---
Função Web|Sim|Sim
Função do funcionário|Sim|Sim
IaaS|Não|Sim

Configuração e implantação|Diagnostics 1.0|Diagnóstico 1.1/1.2/1.3
---|---|---
Integração com Visual Studio - integrado na experiência de desenvolvimento da web/trabalhador do Azure.|Sim|Não
Scripts do PowerShell - Scripts para gerenciar a instalação e configuração dos Diagnostics na função.|Sim|Sim

Fonte de dados|Coleção padrão|Formatar|Descrição|Diagnostics 1.0|Diagnostics 1.1/1.2|Diagnostics 1.3
---|---|---|---|---|---|---
Logs System.Diagnostics.Trace|Sim|Tabela|As mensagens de rastreamento de logs enviadas a partir de seu código ao ouvinte de rastreamento (um ouvinte de rastreamento deve ser adicionado ao arquivo web.config ou app.config). Os dados em log serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADLogsTable.|Sim|Não (Use EventSource)|Sim
Logs IIS|Sim|Blob|Informações de logs sobre sites IIS. Os dados em log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar.|Sim|Sim|Sim
Logs de infraestrutura de diagnóstico do Azure|Sim|Tabela|As informações de logs sobre a infraestrutura de diagnóstico, o módulo RemoteAccess e o módulo RemoteForwarder. Os dados de log serão transferidos no intervalo de scheduledTransferPeriodtransfer para a tabela de armazenamento WADDiagnosticInfrastructureLogsTable.|Sim|Sim|Sim
Logs de solicitação com falha IIS|Não|Blob|Informações de logs sobre solicitações falhas para um site IIS ou aplicativo. Você deve também habilitar definindo opções de rastreamento em system.WebServer, em Web.config. Os dados em log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar.|Sim|Sim|Sim
Log de eventos do Windows|Não|Tabela|Informações de logs sobre quão bem o sistema operacional, o aplicativo ou o driver está sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes são adicionados, os dados do contador de desempenho serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.|Sim|Sim|Sim
Contadores de desempenho|Não|Tabela|Informações de logs sobre quão bem o sistema operacional, o aplicativo ou o driver está sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes são adicionados, os dados do contador de desempenho serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.|Sim|Sim|Sim
Despejos de falhas|Não|Blob|Informações de logs sobre o estado do sistema operacional no evento de uma falha do sistema. Os despejos da mini-falha são coletados localmente. Os despejos completos podem ser habilitados. Os dados em log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar. Pelo fato do ASP.NET manipular mais exceções, isso geralmente é útil somente para uma função de trabalho ou um VM.|Sim|Sim|Sim
Logs de erros personalizados|Não|Blob|Ao usar os recursos de armazenamento local, os dados personalizados podem ser registrados e transferidos imediatamente para o contêiner que você especificar.|Sim|Sim|Sim
EventSource|Não|Tabela|Os eventos de log gerados pelo seu código usando a classe .NET EventSource.|Não|Sim|Sim
Manifesto com base no ETW|Não|Tabela|Eventos ETW gerados por qualquer processo.|Não|Sim|Sim


## Recursos adicionais

- [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][]
- [Coletar dados do log usando o Diagnóstico do Azure][]
- [Depurando um aplicativo do Azure][]
- [Configurando o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais][]

  

[Overview]: #overview
[How to Enable Diagnostics in a Worker Role]: #worker-role
[How to Enable Diagnostics in a Virtual Machine]: #virtual-machine
[Sample Configuration File and Schema]: #configuration-file-schema
[Troubleshooting]: #troubleshooting
[Frequently Asked Questions]: #faq
[Comparando versões de diagnóstico do Azure]: #comparing
[Additional Resources]: #additional
[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
[classe EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Configurando o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais]: http://msdn.microsoft.com/library/windowsazure/dn186185.aspx
[Depurando um aplicativo do Azure]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Coletar dados do log usando o Diagnóstico do Azure]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/library/windowsazure/hh771389.aspx
[Teste Gratuito]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[Esquema de configuração do Azure Diagnostics 1.2]: http://msdn.microsoft.com/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495168.aspx
 

<!---HONumber=AcomDC_1203_2015-->