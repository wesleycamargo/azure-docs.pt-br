<properties urlDisplayName="Diagnostics" pageTitle="Como usar o diagnóstico (.NET) - Guia de recursos do Azure" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Saiba como usar dados de diagnóstico no Azure para depuração, medição do desempenho, monitoramento, análise de tráfego e mais." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="raynew" solutions="" manager="johndaw" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="raynew" />



# Habilitando o Diagnóstico nos Serviços de Nuvem e Máquinas Virtuais do Azure

O Diagnóstico do Azure 1.2 permite coletar dados de diagnóstico de uma função de trabalho, função web ou máquina virtual em execução no Azure. Este guia descreve como usar o Diagnóstico do Azure 1.2. Para obter orientações detalhadas adicionais sobre como criar um log e uma estratégia de rastreamento e como usar o diagnóstico e outras técnicas para solucionar problemas, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][].

## Sumário

-   [Visão geral][]
-   [Como habilitar o Diagnóstico em uma Função de trabalho][]
-   [Como habilitar o Diagnóstico em uma Máquina Virtual][]
-   [Arquivo de configuração de amostra e esquema][]
-   [Solução de problemas][]
-   [Perguntas frequentes][]
-   [Comparando o Diagnóstico do Azure 1.0 e 1.2][]
-   [Recursos adicionais][]

<h2><a name="overview"></a>Overview</h2>

O Diagnóstico Azure 1.2 é uma extensão do Azure que permite coletar dados de telemetria de diagnóstico de uma função de trabalho, função web ou de uma máquina virtual em execução no Azure. Os dados de telemetria são armazenados na conta de armazenamento do Azure e podem ser usados para depuração e solução de problemas, medição de desempenho, monitoramento de uso de recursos, análise de tráfego e planejamento da capacidade e auditoria. 

Se você usou a versão 1.0 do Diagnóstico no passado, existem três diferenças notáveis comparado ao Diagnóstico 1.2:

1.	O Diagnóstico 1.2 pode ser implantado em máquinas virtuais, além de serviços de nuvem.
2.	O Diagnóstico 1.0 é parte do SDK do Azure e é implantado quando você implanta seu serviço de nuvem. O Diagnóstico 1.2 é uma extensão e é implantada separadamente.
3.	O Diagnóstico 1.2 habilita a coleta de eventos ETW e .NET EventSource.

Para ver uma comparação mais detalhada, consulte [Comparando o Diagnóstico do Azure 1.0 e 1.2][] no final deste artigo.

O Diagnóstico do Azure pode coletar os seguintes tipos de telemetria:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Fonte de dados</strong></td>
			<td><strong>Descrição</strong></td>
	</tr>
	<tr>
		<td>Logs IIS</td>
		<td>Informações sobre sites IIS.</td>            
	</tr>
	<tr>
		<td>Logs de infraestrutura do Diagnóstico do Azure</td>
		<td>Informações sobre o próprio Diagnóstico.</td>            
	</tr>
	<tr>
		<td>Logs de solicitação com falha IIS	</td>
		<td>Informações sobre solicitações com falha para um site ou aplicativo IIS.</td>            
	</tr>
	<tr>
		<td>Log de eventos do Windows</td>
		<td>Informações enviadas ao sistema de registro de eventos do Windows.</td>            
	</tr>
	<tr>
		<td>Contadores de desempenho</td>
		<td>Contadores de desempenho personalizados e do Sistema operacional.</td>            
	</tr>
	<tr>
		<td>Despejos de memória</td>
		<td>Informações sobre o estado do processo em caso de falha do aplicativo.</td>            
	</tr>
	<tr>
		<td>Logs de erros personalizados</td>
		<td>Logs criados por seu aplicativo ou serviço.</td>            
	</tr>
	<tr>
		<td>.NET EventSource</td>
		<td>Eventos gerados pelo seu código usando o .NET <a href="http://msdn.microsoft.com/pt-br/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">Classe EventSource</a>.</td>            
	</tr>
	<tr>
		<td>ETW baseado em manifesto</td>
		<td>Eventos ETW gerados por qualquer processo.</td>            
	</tr>
		
</tbody>
</table>

<h2><a name="worker-role"></a>Como habilitar o Diagnóstico em uma Função de trabalho</h2>

Este passo a passo descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando a Classe EventSource .NET. O Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-los em uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio habilita automaticamente o Diagnóstico 1.0 como parte da solução. As instruções a seguir descrevem o processo de criação da função de trabalho, desabilitando o Diagnóstico 1.0 por meio da solução e implantando o Diagnóstico 1.2 para sua função de trabalho.

<h3>Pré-requisitos</h3>
Esse artigo presume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma assinatura do Azure, será possível se inscrever para uma [Avaliação gratuita][]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][].

<h3>Etapa 1: Criar uma Função de trabalho</h3>
1.	Inicie o **Visual Studio 2013**.
2.	Crie um novo projeto do **Serviço de Nuvem do Microsoft Azure** por meio do modelo de **Nuvem** que se destina ao .NET Framework 4.5.Nomeie o projeto "WadExample".
3.	Selecione **Função de Trabalho**.
4.	No **Gerenciador de Soluções**, clique duas vezes no arquivo de propriedades **WorkerRole1**.
5.	Na guia **Configuração**, desmarque **Habilitar o Diagnóstico** para desabilitar o Diagnóstico 1.0.
6.	Compile sua solução para certificar-se de que não haja erros.

<h3>Etapa 2: Instrumentalizar seu código</h3>
Substitua os conteúdos do WorkerRole.cs pelo código a seguir. A classe SampleEventSourceWriter, herdada da [Classe EventSource][] implementa quatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a ID para o respectivo evento. O método Run implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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


<h3>Etapa 3: Implantar sua Função de trabalho</h3>
1.	Implante sua função de trabalho para o Azure por meio do Visual Studio ao selecionar o projeto **WadExample** e, em seguida, **Publicar** no menu **Compilar**.
2.	Escolha sua assinatura.
3.	Na caixa de diálogo **Configurações de Publicação do Microsoft Azure**, selecione **<Criar Novo...>**.
4.	Na caixa de diálogo **Criar Serviço de Nuvem e Conta de Armazenamento**, insira um **Nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5.	Defina o **Ambiente** como **De Preparo**.
6.	Modifique quaisquer outras **Configurações** conforme apropriado, e clique em **Publicar**.
7.	Após a implantação ter sido concluída, verifique no Portal do Azure se seu serviço de nuvem está em estado de **Execução**.

<h3>Etapa 4: Criar seu arquivo de configuração do Diagnóstico e instalar a extensão</h3>
1.	Baixe a definição do esquema do arquivo de configuração pública ao executar o seguinte comando PowerShell:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Adicione um arquivo XML ao seu projeto **WorkerRole1** clicando com o botão direito no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item...** -> **Itens Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml".

	![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml seja uma janela ativa. Pressione **F4** para abrir a janela **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em ...**** na propriedade **Esquemas**. Clique no botão **Adicionar...** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4.	Substitua os conteúdos do arquivo de configuração WadExample.xml pelo seguinte XML e salve o arquivo. Este arquivo de configuração define dois contadores de desempenho a serem coletados: um relacionado à utilização da CPU e um ao uso de memória. Em seguida, a configuração define os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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

<h3>Etapa 5: Instalar o Diagnóstico em sua Função de trabalho</h3>
Os cmdlets do PowerShell para gerenciamento do Diagnóstico em uma função Web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.	Abra o PowerShell do Microsoft Azure.
2.	Execute o script para instalar o Diagnóstico em sua função de trabalho (substitua *StorageAccountKey* pela chave de conta de armazenamento de sua conta de armazenamento wadexample):

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging


<h3>Etapa 6: Olhar seus dados de telemetria</h3>
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Após o serviço de nuvem estar em execução por 5 minutos, você deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**.Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.
	![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

<h2><a name="virtual-machine"></a>Como habilitar o Diagnóstico em uma Máquina Virtual</h2>

Esse passo a passo descreve como instalar remotamente o Diagnóstico em uma máquina virtual do Azure por meio de um computador de desenvolvedor. Você também aprende como implementar um aplicativo que é executado nesta máquina virtual do Azure e emite dados de telemetria usando a [Classe EventSource][] .NET.O Diagnóstico do Azure é usado para coletar a telemetria e armazená-la em uma conta de armazenamento do Azure.

<h3>Pré-requisitos</h3>
Esse passo a passo presume que você tenha uma assinatura do Azure e esteja usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma assinatura do Azure, será possível se inscrever para uma [Avaliação gratuita][]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][].

<h3>Etapa 1: Criar uma Máquina Virtual</h3>
1.	No computador do desenvolvedor, inicie o Visual Studio 2013.
2.	No **Gerenciador de Servidores** do Visual Studio, clique com o botão direito em **Windows Azure** e selecione **Máquina Virtual** -> **Criar Máquina Virtual**.
3.	Selecione sua assinatura do Azure na caixa de diálogo **Escolher uma Assinatura** e clique em **Avançar**.
4.	Selecione **Windows Server 2012 R2 Datacenter** na caixa de diálogo **Selecionar uma Imagem da Máquina Virtual** e clique em **Avançar**.
5.	Nas **Configurações Básicas da Máquina Virtual**, defina o nome da máquina virtual como "wadexample". Defina o nome de usuário e senha do Administrador clique em **Avançar**.
6.	Na caixa de diálogo **Configurações de Serviço de Nuvem**, crie um novo serviço de nuvem chamado "wadexampleVM". Crie uma nova conta de Armazenamento chamada "wadexample" e clique em **Avançar**.
7.	Clique em **Criar**.

<h3>Etapa 2: Criar seu aplicativo</h3>
1.	No computador do desenvolvedor, inicie o Visual Studio 2013.
2.	Crie um novo aplicativo de console do Visual C# que se destina ao .NET Framework 4.5. Nomeie o projeto "WadExampleVM".
	![CloudServices_diag_new_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Substitua os conteúdos do Program.cs pelo código a seguir.  A classe **SampleEventSourceWriter** implementa quatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**.O primeiro parâmetro para o método WriteEvent define a ID para o respectivo evento.O método Run implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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


4.	Salve o arquivo e selecione **Compilar Solução** no menu **Compilar** para compilar seu código.


<h3>Etapa 3: Implantar seu aplicativo</h3>
1.	Clique com o botão direito do mouse no projeto **WadExampleVM** no **Gerenciador de Soluções** e selecione **Abrir Pasta** no Explorador de Arquivos.
2.	Navegue para a pasta *bin\Debug* e copie todos os arquivos (WadExampleVM.*)
3.	No **Gerenciador de Servidores**, clique com o botão direito do mouse na máquina virtual e selecione **Conectar usando a Área de Trabalho Remota**.
4.	Após conectado ao VM, crie uma pasta com o nome de WadExampleVM e cole seus arquivos do aplicativo na pasta.
5.	Inicie o aplicativo WadExampleVM.exe. Você verá uma janela de console em branco.

<h3>Etapa 4: Criar sua configuração do Diagnóstico e instale a Extensão</h3>
1.	Baixe a definição do esquema do arquivo de configuração pública para seu computador de desenvolvimento ao executar o seguinte comando PowerShell:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Abra um novo arquivo XML no Visual Studio, seja em um projeto que você já abriu ou em uma instância do Visual Studio com nenhum projeto aberto. No Visual Studio, selecione **Adicionar** -> **Novo Item...** -> **Itens Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml"
3.	Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml seja uma janela ativa. Pressione **F4** para abrir a janela **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em ...**** na propriedade **Esquemas**. Clique no botão **Adicionar...** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4.	Substitua os conteúdos do arquivo de configuração WadExample.xml pelo seguinte XML e salve o arquivo. Este arquivo de configuração define dois contadores de desempenho a serem coletados: um relacionado à utilização da CPU e um ao uso de memória. Em seguida, a configuração define os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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


<h3>Etapa 5: Instalar remotamente o Diagnóstico na sua Máquina Virtual do Azure </h3>
Os cmdlets do PowerShell para gerenciamento do Diagnóstico em uma VM são: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1.	No computador do desenvolvedor, abra o PowerShell do Microsoft Azure.
2.	Execute o script para instalar remotamente o Diagnóstico na sua VM (substitua *StorageAccountKey* pela chave de conta de armazenamento de sua conta de armazenamento wadexamplevm):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


<h3>Etapa 6: Olhar seus dados de telemetria</h3>
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Após a VM estar em execução por 5 minutos, você deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**.Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.
	![CloudServices_diag_wadexamplevm_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

<h2><a name="configuration-file-schema"></a>Esquema de arquivo de configuração</h2>

O arquivo de configuração do Diagnóstico define valores que são usados para inicializar as definições de configurações do diagnóstico quando o monitor de diagnóstico inicia. Um arquivo de configuração de amostra e documentação detalhada de seu esquema são localizados aqui: [Esquema de Configuração do Diagnóstico do Azure 1.2][].

<h2><a name="troubleshooting"></a>Solução de problemas</h2>

<h3>O Diagnóstico do Azure não inicia</h3>
O Diagnóstico é composto por dois componentes: Um plug-in de agente convidado e o agente de monitoramento. Os arquivos de log do plug-in de agente convidado estão localizados no arquivo: 

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>*\CommandExecution.log

Os seguintes códigos de erro são retornados pelo plug-in:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Código de saída</strong></td>
			<td><strong>Descrição</strong></td>
	</tr>
    <tr>
		<td>0</td>
		<td>Sucesso.</td>            
	</tr>
    <tr>
		<td>-1</td>
        <td>Erro genérico.</td>		            
	</tr>
    <tr>
		<td>-2</td>
        <td><p>Não é possível carregar o arquivo rcf.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in de agente convidado for invocado manualmente, incorretamente, na VM.</p></td>		            
	</tr>
    <tr>
		<td>-3</td>
        <td><p>Não é possível carregar o arquivo de configuração do Diagnóstico.</p>
<p>Solução: Este é o resultado de um arquivo de configuração que não passa pela validação do esquema. A solução é fornecer um arquivo de configuração que esteja em conformidade com o esquema.</p></td>		            
	</tr>
    <tr>
		<td>-4</td>
        <td><p>Outra instância do agente de monitoramento do Diagnóstico já está usando o diretório de recurso local.</p>
<p>Solução: Especifique um valor diferente para <strong>LocalResourceDirectory</strong>.</p></td>		            
	</tr>
    <tr>
		<td>-6</td>
        <td><p>O iniciador do plug-in do agente convidado tentou iniciar o Diagnóstico com uma linha de comando inválida.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in de agente convidado for invocado manualmente, incorretamente, na VM.</p></td>		            
	</tr>
    <tr>
		<td>-10</td>
        <td>O plug-in do Diagnóstico saiu com uma exceção sem tratamento.</td>		            
	</tr>
    <tr>
		<td>-11</td>
        <td><p>O agente convidado foi incapaz de criar o processo responsável pelo lançamento e monitoramento do agente de monitoramento.</p>

<p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos.</p></td>		            
	</tr>
    <tr>
		<td>-101</td>
        <td><p>Argumentos inválidos ao chamar o plug-in do Diagnóstico.</p>

<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in de agente convidado for invocado manualmente, incorretamente, na VM.</p></td>		            
	</tr>
    <tr>
		<td>-102</td>
        <td><p>O processo de plug-in não é capaz de iniciar sozinho.</p> 

<p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos.</p></td>		            
	</tr>
    <tr>
		<td>-103</td>
        <td><p>O processo de plug-in não é capaz de iniciar sozinho. Especificamente, não é capaz de criar o objeto do agente.</p>

<p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos.</p></td>		            
	</tr>
    <tr>
		<td>-104</td>
        <td><p>Incapaz de carregar o arquivo rcf fornecido pelo agente convidado.</p>

<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in de agente convidado for invocado manualmente, incorretamente, na VM.</p></td>		            
	</tr>
    <tr>
		<td>-105</td>
        <td><p>O plug-in do Diagnóstico não pode abrir o arquivo de configuração do Diagnóstico.</p>

<p>Este é um erro interno que deve acontecer apenas se plug-in do Diagnóstico for invocado manualmente, incorretamente, no VM.</p></td>		            
	</tr>
    <tr>
		<td>-106</td>
        <td><p>Não é capaz de ler o arquivo de configuração do Diagnóstico.</p>

<p>Solução: Este é o resultado de um arquivo de configuração que não passa pela validação do esquema. A solução é fornecer um arquivo de configuração que esteja em conformidade com o esquema. Você pode localizar o XML que é fornecido para a extensão do Diagnóstico na pasta <i>%SystemDrive%\WindowsAzure\Config</i> na VM. Abra o arquivo XML adequado e pesquise por <strong>Microsoft.Azure.Diagnostics</strong>e, em seguida, pelo campo <strong>xmlCfg</strong> . Os dados são codificados com base64, portanto será necessário <a href="http://www.bing.com/search?q=base64+decoder">decodificá-los</a> para ver o XML que foi carregado pelo Diagnóstico.</p></td>		            
	</tr>
    <tr>
		<td>-107</td>
        <td><p>O diretório de recursos que passa para o agente de monitoramento é inválido.</p>

<p>Este é um erro interno que deve acontecer apenas se o agente de monitoramento for invocado manualmente, incorretamente, no VM.</p></td>		            
	</tr>
    <tr>
		<td>-108	</td>
        <td><p>Incapaz de converter o arquivo de configuração do Diagnóstico no arquivo de configuração do agente de monitoramento.</p>

<p>Este é um erro interno que deve acontecer apenas se o plug-in do Diagnóstico for invocado manualmente com um arquivo de configuração inválido.</p></td>		            
	</tr>
    <tr>
		<td>-110</td>
        <td><p>Erro de configuração geral do Diagnóstico.</p>

<p>Este é um erro interno que deve acontecer apenas se o plug-in do Diagnóstico for invocado manualmente com um arquivo de configuração inválido.</p></td>		            
	</tr>
    <tr>
		<td>-111</td>
        <td><p>Incapaz de iniciar o agente de monitoramento.</p>

<p>Solução: Verifique se há recursos do sistema suficientes disponíveis.</p></td>		            
	</tr>
    <tr>
		<td>-112</td>
        <td>Erro geral</td>		            
	</tr>    
</tbody>
</table>

<h3>Os dados do Diagnóstico não estão conectados ao armazenamento</h3>
A causa mais comum da ausência de dados de evento é uma informação de conta de armazenamento definida incorretamente. 

Solução: Corrija seu arquivo de configuração do Diagnóstico e reinstale o Diagnóstico.
Antes dos dados de eventos serem carregados em sua conta de armazenamento, eles são armazenados na pasta. Consulte acima para obter detalhes sobre o **LocalResourceDirectory**.

Se não existirem arquivos nesta pasta, o agente de monitoramento será incapaz de iniciar. Normalmente, isto é causado por um arquivo de configuração inválido e deve ser relatado em CommandExecution.log. Se o Agente de Monitoramento estiver dados de eventos com êxito, você verá arquivos .tsf para cada evento definido em seu arquivo de configuração.

O Agente de Monitoramento registra quaisquer erros que ocorrerem no arquivo MaEventTable.tsf. Para inspecionar o conteúdo deste arquivo, execute o comando a seguir:

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.2.0.0\Monitor\x64\table2csv maeventtable.tsf

A ferramenta gera um arquivo com o nome de maeventtable.csv, que você pode abrir e inspecionar os logs para verificar as falhas.


<h2><a name="faq"></a>Perguntas frequentes</h2>
A seguir estão algumas perguntas frequentes e suas respostas:

**P.** Como eu atualizo a minha solução do Visual Studio do Diagnóstico do Azure 1.0 para o Diagnóstico do Azure 1.1?

** R.** Atualizar sua solução do Visual Studio do Diagnóstico 1.0 para o Diagnóstico 1.1 (ou posterior) é um processo manual:
- Desabilite o Diagnóstico na sua solução do Visual Studio para evitar que o Diagnóstico 1.0 seja implantado com sua função
- Se o seu código usar o Ouvinte de Rastreamento, você precisará modificar seu código para usar o .NET EventSource. O Diagnóstico 1.1 e posterior não dá suporte ao Ouvinte de Rastreamento.
- Modifique seu processo de implantação para instalar a extensão do Diagnóstico 1.1

**P.** Se eu já tiver instalado a extensão do Diagnóstico 1.1 em minha função ou VM, como eu atualizo para o Diagnóstico 1.2?

**R.** Se você tiver especificado a "-Versão "1.*"" quando instalou o Diagnóstico 1.1, na próxima vez em que sua função for reiniciada ou que a VM for reiniciada, ela será atualizada automaticamente para a versão mais recente que corresponder à expressão regular "1.*"Se tiver especificado a "-Versão "1.1"" quando instalou o Diagnóstico 1.1, você pode atualizar para uma versão mais recente executando novamente o cmdlet Set- e especificando a versão que deseja instalar.

**P.** Como as tabelas são nomeadas?

**R.** As tabelas são nomeadas de acordo com o seguinte:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Este é um exemplo:

		<EtwEventSourceProviderConfiguration provider="prov1">
		  <Event id="1" />
		  <Event id="2" eventDestination="dest1" />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider="prov2">
		  <DefaultEvents eventDestination="dest2" />
		</EtwEventSourceProviderConfiguration>

Isso gerará 4 tabelas:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Evento</strong></td>
			<td><strong>Nome da tabela</strong></td>			
	</tr>
	<tr>
			<td>provider="prov1" <Event id="1" /></td>
			<td>WADEvent+MD5("prov1")+"1"</td>			
	</tr>
	<tr>
			<td>provider="prov1" <Event id="2" eventDestination="dest1" /></td>
			<td>WADdest1</td>			
	</tr>
	<tr>
			<td>provider="prov1" <DefaultEvents /></td>
			<td>WADDefault+MD5("prov1")</td>			
	</tr>
	<tr>
			<td>provider="prov2" <DefaultEvents eventDestination="dest2" /></td>
			<td>WADdest2</td>			
	</tr>
	

</table>
</tbody>

<h2><a name="comparing"></a>Comparando o Diagnóstico do Azure 1.0 e 1.2</h2>

A tabela a seguir compara os recursos com suporte pelo Diagnóstico do Azure versões 1.0 e 1.1/1.2:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Tipos de função com suporte</strong></td>
			<td><strong>Diagnóstico 1.0</strong></td>
			<td><strong>Diagnóstico 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Função web</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Função de trabalho</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>Não</td>
			<td>Sim</td>
	</tr>
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Configuração e implantação</strong></td>
			<td><strong>Diagnóstico 1.0</strong></td>
			<td><strong>Diagnóstico 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Integração com Visual Studio - integrado na experiência de desenvolvimento da web/de trabalho do Azure.</td>
			<td>Sim</td>
			<td>Não</td>
	</tr>
	<tr>
			<td>Scripts do PowerShell - scripts para gerenciar a instalação e configuração do Diagnóstico na função.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Fonte de dados</strong></td>
			<td><strong>Coleção padrão</strong></td>
			<td><strong>Formato</strong></td>
			<td><strong>Descrição</strong></td>
			<td><strong>Diagnóstico 1.0</strong></td>
			<td><strong>Diagnóstico 1.1/1.2</strong></td>
	</tr>
	<tr>
			<td>Logs System.Diagnostics.Trace</td>
			<td>Sim</td>
			<td>Tabela</td>
			<td>As mensagens de rastreamento de logs enviadas por meio de seu código ao ouvinte de rastreamento (um ouvinte de rastreamento deve ser adicionado ao arquivo web.config ou app.config). Os dados de logs serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADLogsTable.</td>
			<td>Sim</td>
			<td>Não (Use EventSource)</td>
	</tr>
	<tr>
			<td>Logs IIS</td>
			<td>Sim</td>
			<td>Blob</td>
			<td>Informações de logs sobre sites IIS. Os dados de log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Logs de infraestrutura do Diagnóstico do Azure</td>
			<td>Sim</td>
			<td>Tabela</td>
			<td>As informações de logs sobre a infraestrutura de diagnóstico, o módulo RemoteAccess e o módulo RemoteForwarder. Os dados de log serão transferidos no intervalo de scheduledTransferPeriodtransfer para a tabela de armazenamento WADDiagnosticInfrastructureLogsTable.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Logs de solicitação com falha IIS</td>
			<td>Não</td>
			<td>Blob</td>
			<td>Informações de logs sobre solicitações falhas para um site IIS ou aplicativo. Você deve também habilitar definindo opções de rastreamento em system.WebServer, em Web.config. Os dados de log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar.</td>
			<td>Sim</td>
			<td>Yes</td>
	</tr>
	<tr>
			<td>Log de eventos do Windows</td>
			<td>Não</td>
			<td>Tabela</td>
			<td>Informações de logs sobre quão bem o sistema operacional, o aplicativo ou o driver está sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes são adicionados, os dados do contador de desempenho serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Contadores de desempenho</td>
			<td>Não</td>
			<td>Tabela</td>
			<td>Informações de logs sobre quão bem o sistema operacional, o aplicativo ou o driver está sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes são adicionados, os dados do contador de desempenho serão transferidos no intervalo de transferência scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Despejos de memória</td>
			<td>Não</td>
			<td>Blob</td>
			<td>Informações de logs sobre o estado do sistema operacional em caso de uma falha do sistema. Mini despejos de memória são coletados localmente. Despejos completos podem ser habilitados. Os dados de log serão transferidos no intervalo de transferência scheduledTransferPeriod ao contêiner que você especificar. Pelo fato do ASP.NET lidar com mais exceções, isso geralmente é útil somente para uma função de trabalho ou uma VM.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>Logs de erros personalizados</td>
			<td>Não</td>
			<td>Blob</td>
			<td>Ao usar os recursos de armazenamento local, os dados personalizados podem ser registrados e transferidos imediatamente para o contêiner que você especificar.</td>
			<td>Sim</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>Não</td>
			<td>Tabela</td>
			<td>Os eventos de log gerados pelo seu código usando a classe .NET EventSource.</td>
			<td>Não</td>
			<td>Sim</td>
	</tr>
	<tr>
			<td>ETW baseado em manifesto</td>
			<td>Não</td>
			<td>Tabela</td>
			<td>Eventos ETW gerados por qualquer processo.</td>
			<td>Não</td>
			<td>Sim</td>
	</tr>
</tbody>
</table>

<h2><a name="additional"></a>Recursos adicionais</h2>

- [Práticas recomendadas para solução de problemas no desenvolvimento de aplicativos do Azure][]
- [Coletar dados do log usando o Diagnóstico do Azure][]
- [Depurando um aplicativo do Azure][]
- [Configurando o Diagnóstico do Azure][]

  

[Visão geral]: #overview
[Como habilitar o Diagnóstico em uma Função de trabalho]: #worker-role
[Como habilitar o Diagnóstico em uma Máquina Virtual]: #virtual-machine
[Arquivo de configuração de amostra e esquema]: #configuration-file-schema
[Solução de problemas]: #troubleshooting
[Perguntas frequentes]: #faq
[Comparando o Diagnóstico do Azure 1.0 e 1.2]: #comparing
[Recursos adicionais]: #additional
[Classe EventSource]: http://msdn.microsoft.com/pt-br/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Configurando o Diagnóstico do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn186185.aspx
[Depurando um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405479.aspx   
[Coletar dados do log usando o Diagnóstico do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433048.aspx
[Práticas recomendadas para solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh771389.aspx
[Avaliação gratuita]: http://azure.microsoft.com/pt-br/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
[Esquema de configuração do Diagnóstico do Azure 1.2]: http://msdn.microsoft.com/pt-br/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/pt-br/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/pt-br/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/pt-br/library/dn495168.aspx

<!--HONumber=35.1-->
