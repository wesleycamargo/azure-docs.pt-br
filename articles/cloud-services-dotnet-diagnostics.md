<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Diagnostics" pageTitle="How to use diagnostics (.NET) - Azure feature guide" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Learn how to use diagnostic data in Azure for debugging, measuring performance, monitoring, traffic analysis, and more." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Habilitando o Diagnostics nos Serviços de nuvem do Azure e Máquinas virtuais

O Azure Diagnostics 1.2 permite coletar dados de diagnóstico de uma função de trabalho, função web ou de uma máquina virtual em execução no Azure. Este guia descreve como usar o Azure Diagnostics 1.2. Para obter orientações detalhadas adicionais sobre como criar um log e uma estratégia de rastreamento e usar diagnóstico e outras técnicas para solucionar problemas, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure].

## Sumário

-   [Visão geral][Visão geral]
-   [Como habilitar o Diagnostics em uma Função do Trabalho][Como habilitar o Diagnostics em uma Função do Trabalho]
-   [Como habilitar o Diagnostics em uma Máquina Virtual][Como habilitar o Diagnostics em uma Máquina Virtual]
-   [Arquivo de Configuração de Amostra e Esquema][Arquivo de Configuração de Amostra e Esquema]
-   [Solucionar problemas][Solucionar problemas]
-   [Perguntas frequentes][Perguntas frequentes]
-   [Comparando o Azure Diagnostics 1.0 e 1.2][Comparando o Azure Diagnostics 1.0 e 1.2]
-   [Recursos adicionais][Recursos adicionais]

## <a name="overview"></a><span class="short-header">Visão Geral</span>Visão Geral

O Azure Diagnostics 1.2 é uma extensão do Azure que permite coletar dados de telemetria de diagnóstico de uma função de trabalho, função web ou de uma máquina virtual em execução no Azure. Os dados de telemetria são armazenados na conta de armazenamento do Azure e podem ser usados para depuração e solução de problemas, medição de desempenho, monitoramento de uso de recursos, análise de tráfego e planejamento da capacidade e auditoria.

Se você usou a versão 1.0 do Diagnostics no passado, existem três diferenças notáveis comparado ao Diagnostics 1.2:

1.  O Diagnostics 1.2 pode ser implantado em máquinas virtuais, além de serviços de nuvem.
2.  O Diagnostics 1.0 é parte do SDK do Azure e é implantado quando você implanta seu serviço de nuvem. O Diagnostics 1.2 é uma extensão e é implantada separadamente.
3.  O Diagnostics 1.2 habilita a coleção dos eventos ETW e .NET EventSource.

Para uma comparação mais detalhada, consulte [Comparando o Azure Diagnostics 1.0 e 1.2][Comparando o Azure Diagnostics 1.0 e 1.2] ao final deste artigo.

O Diagnóstico do Azure pode coletar os seguintes tipos de telemetria:


<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
    <tr>
            <td style="width: 100px;"><strong>Fonte de dados</strong></td>
            <td><strong>Descri&ccedil;&atilde;o</strong></td>
    </tr>
    <tr>
        <td>Logs IIS</td>
        <td>Informa&ccedil;&otilde;es sobre sites IIS.</td>            
    </tr>
    <tr>
        <td>Logs de infraestrutura de diagn&oacute;stico do Azure</td>
        <td>Informa&ccedil;&otilde;es sobre o pr&oacute;prio Diagnostics.</td>            
    </tr>
    <tr>
        <td>Logs de solicita&ccedil;&atilde;o com falha IIS </td>
        <td>Informa&ccedil;&otilde;es sobre solicita&ccedil;&otilde;es falhas para um site IIS ou aplicativo.</td>            
    </tr>
    <tr>
        <td>Log de eventos do Windows</td>
        <td>Informa&ccedil;&otilde;es enviadas ao sistema de registro de evento do Windows.</td>            
    </tr>
    <tr>
        <td>Contadores de desempenho</td>
        <td>Sistema Operacional e contadores de desempenho personalizados.</td>            
    </tr>
    <tr>
        <td>Despejos de falhas</td>
        <td>Informa&ccedil;&otilde;es sobre o estado do processo no evento de uma falha do aplicativo.</td>            
    </tr>
    <tr>
        <td>Logs de erros personalizados</td>
        <td>Logs criados por seu aplicativo ou servi&ccedil;o.</td>            
    </tr>
    <tr>
        <td>.NET EventSource</td>
        <td>Os eventos de log gerados pelo seu c&oacute;digo usando o <a href="http://msdn.microsoft.com/pt-br/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">.NET EventSource Class</a>.</td>            
    </tr>
    <tr>
        <td>Manifesto com base no ETW</td>
        <td>Eventos ETW gerados por qualquer processo.</td>            
    </tr>
        
</tbody>
</table>


## <a name="worker-role"></a><span class="short-header">Habilitar o Diagnostics em uma Função de Trabalho</span>Como habilitar o Diagnostics em uma Função do Trabalho

Este passo a passo descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando o .NET EventSource Class. O Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-los em uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio habilita automaticamente o Diagnostics 1.0 como parte da solução. As instruções a seguir descrevem o processo para a criação da função de trabalho, desabilitando o Diagnostics 1.0 a partir da solução, e implantando o Diagnostics 1.2 para sua função de trabalho.

### Pré-requisitos

Esse artigo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma conta do Azure, será possível se inscrever para um [Teste Gratuito][Teste Gratuito]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior].

### Etapa 1: Criar uma Função de trabalho:

1.  Inicie o **Visual Studio 2013**.
2.  Crie um novo projeto do **Serviço de Nuvem do Windows Azure** a partir do modelo de **Nuvem** que se destina ao .NET Framework 4.5. Nomeie o projeto “WadExampleVM”.
3.  Selecione **Função de Trabalho**.
4.  No **Gerenciador de Soluções**, clique duas vezes no arquivo de propriedades **WorkerRole1**.
5.  Na guia **Configuração**, desmarque **Habilitar Diagnostics** para desabilitar o Diagnostics 1.0.
6.  Compile sua solução para verificar que você não tem erros.

### Etapa 2: Instrumentalize seu código

Substitua os conteúdos do WorkerRole.cs pelo código a seguir. A classe SampleEventSourceWriter, herdada da [Classe EventSource][.NET EventSource Class] implementa quatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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

### Etapa 3: Implante sua Função de Trabalho

1.  Implante sua função de trabalho para o Azure a partir do Visual Studio ao selecionar o projeto **WadExample** e, em seguida, **Publicar** a partir do menu **Compilar**.
2.  Escolha sua assinatura.
3.  Na caixa de diálogo **Configurações do Microsoft Azure Publish**, selecione **<create new…>**.
4.  Na caixa de diálogo **Criar Serviço de Nuvem e Conta de Conta de Armazenamento**, insira um **Nome** (por exemplo, “WadExample”) e selecione uma região ou grupo de afinidade.
5.  Defina o **Ambiente** para **De Preparo**.
6.  Modifique quaisquer outras **Configurações** conforme apropriado, e clique em **Publicar**.
7.  Após a implantação ter sido concluída, verifique no Portal do Azure que seu serviço de nuvem está em um estado de **Execução**.

### Etapa 4: Criar seu arquivo de configuração do Diagnostics e instale a extensão

1.  Baixe a definição do esquema do arquivo de configuração pública ao executar o seguinte comando PowerShell:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

3.  Adicione um arquivo XML ao seu projeto **WorkerRole1** ao clicar com o botão direito do mouse no projeto **WorkerRole1** e selecione **Adicionar** -\> **Novo Item…** -\> **Itens Visual C#** -\> **Dados** -\> **Arquivo XML**. Nomeie o arquivo como “WadExample.xml”.

    ![CloudServices\_diag\_add\_xml][CloudServices\_diag\_add\_xml]

4.  Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela de **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em **…** na propriedade **Esquemas**. Clique no botão **Adicionar…** e navegue para o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
5.  Substitua os conteíudos do arquivo de configuração WadExample.xml com o seguinte XML e salve o arquivo. Este arquivo de configuração define alguns contadores de desempenho para coletar: um para o uso de CPU e um para o uso de memória. Após a configuração, defina os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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

### Etapa 5: Instale o Diagnostics em sua Função de Trabalho

Os cmdlets do PowerShell para gerenciamento de Diagnostics em uma função de web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.  Abra o PowerShell do Windows Azure.
2.  Execute o script para instalar o Diagnostics na sua função de trabalho (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexample):

        $storage_name = "wadexample"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
        $service_name="wadexample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging

### Etapa 6: Olhe nos seus dados de telemetria

No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o serviço de nuvem foi executado por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.
 ![CloudServices\_diag\_tables][CloudServices\_diag\_tables]

## <a name="virtual-machine"></a><span class="short-header">Habilitar o Diagnostics em uma máquina virtual</span>Como habilitar o Diagnostics em uma máquina virtual

Esse passo a passo descreve como instalar remotamente o Diagnostics em uma máquina virtual do Azure a partir de um computador de desenvolvedor. Você também aprende como implementar um aplicativo que executa nesta máquina virtual do Azure e emite dados de telemetria usando o .NET [EventSource Class][.NET EventSource Class]. O Diagnóstico do Azure é usado para coletar a telemetria e armazená-la em uma conta de armazenamento do Azure.

### Pré-requisitos

Esse passo a passo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma conta do Azure, será possível se inscrever para um [Teste Gratuito][Teste Gratuito]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior].

### Etapa 1: Criar uma máquina virtual

1.  No computador do desenvolvedor, inicie o Visual Studio 2013.
2.  No **Gerenciador de Servidores** do Visual Studio, clique com o botão direito do mouse em **Windows Azure**, em seguida selecione **Máquina Virtual** -\> **Criar Máquina Virtual**.
3.  Selecione sua assinatura do Azure na caixa de diálogo **Escolher uma Assinatura** e clique em **Avançar**.
4.  Selecione o **Windows Server 2012 R2 Datacenter** na caixa de diálogo **Selecionar uma imagem da máquina virtual** e clique em **Avançar**.
5.  Nas **Configurações Básicas da Máquina Virtual**, defina o nome da máquina virtual para “wadexample”. Defina o nome de usuário e senha do Administrador clique em **Avançar**.
6.  Na caixa de diáloto **Configurações de Serviço de Nuvem**, crie um novo serviço de nuvem chamado “wadexampleVM”. Crie uma nova conta de Armazenamento chamada “wadexample” e clique em **Avançar**.
7.  Clique em **Criar**.

### Etapa 2: Criar seu aplicativo

1.  No computador do desenvolvedor, inicie o Visual Studio 2013.
2.  Crie um novo aplicativo de console do Visual C# que se destina ao .NET Framework 4.5. Nomeie o projeto “WadExampleVM”.
    ![CloudServices\_diag\_new\_project][CloudServices\_diag\_new\_project]
3.  Substitua os conteúdos do Program.cs pelo código a seguir. A classe **SampleEventSourceWriter** implementa quatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método WriteEvent define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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

4.  Salve o arquivo e selecione **Compilar Solução** a partir do menu **Compilar** para compilar seu código.

### Etapa 3: Implante seu aplicativo

1.  Clique com o botão direito do mouse no projeto **WadExampleVM** no **Gerenciador de Gerenciador de Soluções** e selecione **Abrir Pasta** no Explorador de Arquivos.
2.  Navegue para a pasta *bin\\Debug* e copie todos os arquivos (WadExampleVM.\*)
3.  No **Gerenciador de Servidores**, clique com o botão direito do mouse na máquina virtual e selecione **Conectar usando a Área de Trabalho Remota**.
4.  Uma vez conectado ao VM, crie uma pasta com o nome de WadExampleVM e cole seus arquivos do aplicativo na pasta.
5.  Inicie o aplicativo WadExampleVM.exe. Você deve ver uma janela de console em branco.

### Etapa 4: Crie sua configuração do Diagnostics e instale o Extension

1.  Baixe a definição do esquema do arquivo de configuração pública para seu computador de desenvolvimento ao executar o seguinte comando PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.  Abra um novo arquivo XML no Visua Studio, seja em um projeto que você já abriu, ou em uma instância do Visual Studio com nenhum projeto aberto. No Visual Studio, selecione **Adicionar** -\> **Novo Item…** -\> **Itens do Visual C#** -\> **Dados** -\> **Arquivo XML**. Nomeie o arquivo como “WadExample.xml”
3.  Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela de **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em **…** na propriedade **Esquemas**. Clique no botão **Adicionar…** e navegue para o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4.  Substitua os conteíudos do arquivo de configuração WadExample.xml com o seguinte XML e salve o arquivo. Este arquivo de configuração define alguns contadores de desempenho para coletar: um para o uso de CPU e um para o uso de memória. Após a configuração, defina os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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

### Etapa 5: Instale remotamente o Diagnostics na sua Máquina Virtual do Azure

Os cmdlets do PowerShell para gerenciamento de Diagnostics em um VM são: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1.  No computador do desenvolvedor, abra o PowerShell do Windows Azure.
2.  Execute o script para instalar remotamente o Diagnostics no seu VM (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### Etapa 6: Olhe nos seus dados de telemetria

No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o VM foi executado por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.
 ![CloudServices\_diag\_wadexamplevm\_tables][CloudServices\_diag\_wadexamplevm\_tables]

## <a name="configuration-file-schema"></a><span class="short-header">Arquivo de configuração da amostra e esquema</span>Esquema do arquivo de configuração

O arquivo de configuração do Diagnostics define valores que são usados para inicializar as definições de configurações do diagnóstico quando o monitor de diagnóstico inicia. Um arquivo de configuração de amostra e documentação detalhada no seu esquema são localizados aqui: [Esquema de Configuração do Azure Diagnostics 1.2][Esquema de Configuração do Azure Diagnostics 1.2].

## <a name="troubleshooting"></a><span class="short-header">Solucionar problemas</span>Solucionar problemas

### Azure Diagnostics não está iniciando

O Diagnostics é comprimido de dois componentes: Um plug-in do agente convidado e o agente de monitoramento. Os arquivos de log para o plug-in do agente convidado estão localizados no arquivo:

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<diagnosticsversion>*\\CommandExecution.log

Os seguintes códigos de erro são devolvidos pelo plug-in:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Código de Saída</strong></td>
<td align="left"><strong>Descrição</strong></td>
</tr>
<tr class="even">
<td align="left">0</td>
<td align="left">Sucesso.</td>
</tr>
<tr class="odd">
<td align="left">-1</td>
<td align="left">Erro genérico.</td>
</tr>
<tr class="even">
<td align="left">-2</td>
<td align="left"><p>Incapaz de carregar o arquivo rcf.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="odd">
<td align="left">-3</td>
<td align="left"><p>Não pode carregar o arquivo de configuração de Diagnostics.</p>
<p>Solução: Este é o resultado de um arquivo de configuração que não passa pela validação do esquema. A solução é fornecer um arquivo de configuração que cumpre com o esquema.</p></td>
</tr>
<tr class="even">
<td align="left">-4</td>
<td align="left"><p>Outra instância do agente de monitoramento do Diagnostics já está usando o diretório de recurso local.</p>
<p>Solução: Especifique um valor diferente para <strong>LocalResourceDirectory</strong>.</p></td>
</tr>
<tr class="odd">
<td align="left">-6</td>
<td align="left"><p>O iniciador do plug-in do agente convidado tentou iniciar o Diagnostics com uma linha de comando inválida.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="even">
<td align="left">-10</td>
<td align="left">O plug-in de Diagnostics saiu com uma exceção sem tratamento.</td>
</tr>
<tr class="odd">
<td align="left">-11</td>
<td align="left"><p>O agente convidado foi incapaz de criar o processo responsável pelo lançamento e monitoramento do agente de monitoramento.</p>
<p>Solução: Verifique se os recursos do sistema suficiente estão disponíveis para iniciar novos processos.</p></td>
</tr>
<tr class="even">
<td align="left">-101</td>
<td align="left"><p>Argumentos inválidos ao chamar o plug-in de Diagnostics.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="odd">
<td align="left">-102</td>
<td align="left"><p>O processo de plug-in é incapaz de iniciar sozinho.</p>
<p>Solução: Verifique se os recursos do sistema suficiente estão disponíveis para iniciar novos processos.</p></td>
</tr>
<tr class="even">
<td align="left">-103</td>
<td align="left"><p>O processo de plug-in é incapaz de iniciar sozinho. Especificamente, é incapaz de criar o objeto do agente.</p>
<p>Solução: Verifique se os recursos do sistema suficiente estão disponíveis para iniciar novos processos.</p></td>
</tr>
<tr class="odd">
<td align="left">-104</td>
<td align="left"><p>Incapaz de carregar o arquivo rcf fornecido pelo agente convidado.</p>
<p>Este é um erro interno que deve acontecer apenas se o iniciador do plug-in do agente convidado é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="even">
<td align="left">-105</td>
<td align="left"><p>O plug-in do Diagnostics não pode abrir o arquivo de configuração de Diagnostics.</p>
<p>Este é um erro interno que deve acontecer apenas se plug-in de Diagnostics é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="odd">
<td align="left">-106</td>
<td align="left"><p>Não pode ler o arquivo de configuração de Diagnostics.</p>
<p>Solução: Este é o resultado de um arquivo de configuração que não passa pela validação do esquema. Então a solução é fornecer um arquivo de configuração que cumpre com o esquema. Você pode localizar o XML que é fornecido para a extensão Diagnostics na pasta <em>%SystemDrive%\WindowsAzure\Config</em> no VM. Abra o arquivo XML apropriado e pesquise por <strong>Microsoft.Azure.Diagnostics</strong> e, em seguida, por campo <strong>xmlCfg</strong>. Os dados são base64 codificados, então você precisará <a href="http://www.bing.com/search?q=base64+decoder">decodificá-los</a> para ver o XML que foi carregado pelo Diagnostics.</p></td>
</tr>
<tr class="even">
<td align="left">-107</td>
<td align="left"><p>O diretório de recursos que passa para o agente de monitoramento é inválido.</p>
<p>Este é um erro interno que deve acontecer apenas se o agente de monitoramento é invocado manualmente, incorretamente, no VM.</p></td>
</tr>
<tr class="odd">
<td align="left">-108</td>
<td align="left"><p>Incapaz de converter o arquivo de configuração do Diagnostics no arquivo de configuração do agente de monitoramento.</p>
<p>Este é um erro interno que deve acontecer apenas se o plug-in Diagnostics é invocado manualmente com um arquivo de configuração inválido.</p></td>
</tr>
<tr class="even">
<td align="left">-110</td>
<td align="left"><p>Erro de configuração do Diagnostics geral.</p>
<p>Este é um erro interno que deve acontecer apenas se o plug-in Diagnostics é invocado manualmente com um arquivo de configuração inválido.</p></td>
</tr>
<tr class="odd">
<td align="left">-111</td>
<td align="left"><p>Incapaz de iniciar o agente de monitoramento.</p>
<p>Solução: Verifique se os recursos do sistema suficiente estão disponíveis.</p></td>
</tr>
<tr class="even">
<td align="left">-112</td>
<td align="left">Erro geral</td>
</tr>
</tbody>
</table>

### Os dados do Diagnostics não estão conectados ao armazenamentodata

A causa mais comum de dados de evento em falta é uma informação de conta de armazenamento definida incorretamente.

Solução: Corrija seu arquivo de configuração do Diagnostics e reinstale o Diagnostics.
Antes dos dados de evento ser carregado em sua conta de armazenamento, eles são armazenados na pasta. Consulte acima para obter detalhes sobre **LocalResourceDirectory**.

Se não existirem arquivos nesta pasta, o agente de monitoramento é incapaz de iniciar. Isso é geralmente causado por um arquivo de configuração inválido e deve ser reportado no CommandExecution.log. Se o Agente de Monitoramento está coletando com sucesso os dados do evento, você verá os arquivos .tsf para cada evento definido em seu arquivo de configuração.

O Agente de Monitoramento registra quaisquer erros que experimente no arquivo MaEventTable.tsf. Para inspecionar os conteúdos deste arquivo, execute o seguinte comando:

        %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics.2.0.0\Monitor\x64\table2csv maeventtable.tsf

A ferramenta gera um arquivo com o nome de maeventtable.csv, que você pode abrir e inspecionar os logs, para verificar as falhas.

## <a name="faq"></a><span class="short-header">FAQ</span>Perguntas frequentes

A seguir estão algumas perguntas frequentes e suas respostas:

**P.** Como eu atualizo a minha solução do Visual Studio a partir do Azure Diagnostics 1.0 para Azure Diagnostics 1.1?

**R.** Atualizar a sua solução do Visual Studio a partir do Diagnostics 1.0 para Diagnostics 1.1 (ou posterior) é um processo manual:

-   Desative o Diagnostics na sua solução do Visual Studio para evitar que o Diagnostics 1.0 seja implantado com sua função
-   Se o seu código usa o Ouvinte de Rastreamento, você precisará modificar seu código para usar o .NET EventSource. Diagnostics 1.1 e posterior não suportam o Ouvinte de Rastreamento.
-   Modifique seu processo de implantação para instalar o Diagnostics 1.1 Extension

**P.** Se eu já tiver o Diagnostics 1.1 Extension em minha função ou VM, como eu atualizeo para Diagnostics 1.2?

**R.** Se vocêespecificou “–Versão “1.*” quando você instalou o Diagnostics 1.1, a próxima vez que sua função reiniciar ou o VM reiniciar, será atualizado automaticamente para a versão mais recente correspondendo à versão regular “1.*” If “–Versão “1.1” quando você instalou o Diagnostics 1.1, você pode atualizar uma versão mais recente sendo executada novamente no Set- cmdlet e especificando a versão que deseja instalar.

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



<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
    <tr>
            <td style="width: 100px;"><strong>Evento</strong></td>
            <td><strong>Nome da tabela</strong></td>            
    </tr>
    <tr>
            <td>provider=&rdquo;prov1&rdquo; &lt;Event id=&rdquo;1&rdquo; /&gt;</td>
            <td>WADEvent+MD5(&ldquo;prov1&rdquo;)+&rdquo;1&rdquo;</td>          
    </tr>
    <tr>
            <td>provider=&rdquo;prov1&rdquo; &lt;Event id=&rdquo;2&rdquo; eventDestination=&rdquo;dest1&rdquo; /&gt;</td>
            <td>WADdest1</td>           
    </tr>
    <tr>
            <td>provider=&rdquo;prov1&rdquo; &lt;DefaultEvents /&gt;</td>
            <td>WADDefault+MD5(&ldquo;prov1&rdquo;)</td>            
    </tr>
    <tr>
            <td>provider=&rdquo;prov2&rdquo; &lt;DefaultEvents eventDestination=&rdquo;dest2&rdquo; /&gt;</td>
            <td>WADdest2</td>           
    </tr>
    

</table>
</tbody>

## <a name="comparing"></a><span class="short-header">Azure Diagnostics 1.0 vs 1.2</span>Comparando o Azure Diagnostics 1.0 e 1.2

A tabela seguinte compara os recursos suportados pelo Azure Diagnostics versões 1.0 e 1.1/1.2:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
    <tr>
            <td style="width: 100px;"><strong>Tipos de fun&ccedil;&atilde;o suportados</strong></td>
            <td><strong>Diagnostics 1.0</strong></td>
            <td><strong>Diagnostics 1.1/1.2</strong></td>
    </tr>

    <tr>
            <td>Fun&ccedil;&atilde;o Web</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Fun&ccedil;&atilde;o do funcion&aacute;rio</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>IaaS</td>
            <td>No</td>
            <td>Sim</td>
    </tr>
</tbody>
</table>
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
    <tr>
            <td style="width: 100px;"><strong>Configura&ccedil;&atilde;o e implanta&ccedil;&atilde;o</strong></td>
            <td><strong>Diagnostics 1.0</strong></td>
            <td><strong>Diagnostics 1.1/1.2</strong></td>
    </tr>

    <tr>
            <td>Integra&ccedil;&atilde;o com Visual Studio - integrado na experi&ecirc;ncia de desenvolvimento da web/trabalhador do Azure.</td>
            <td>Sim</td>
            <td>No</td>
    </tr>
    <tr>
            <td>Scripts do PowerShell - Scripts para gerenciar a instala&ccedil;&atilde;o e configura&ccedil;&atilde;o dos Diagnostics na fun&ccedil;&atilde;o.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    
</tbody>
</table>
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
    <tr>
            <td style="width: 100px;"><strong>Fonte de dados</strong></td>
            <td><strong>Cole&ccedil;&atilde;o padr&atilde;o</strong></td>
            <td><strong>Formatar</strong></td>
            <td><strong>Descri&ccedil;&atilde;o</strong></td>
            <td><strong>Diagnostics 1.0</strong></td>
            <td><strong>Diagnostics 1.1/1.2</strong></td>
    </tr>
    <tr>
            <td>Logs System.Diagnostics.Trace</td>
            <td>Sim</td>
            <td>Tabela</td>
            <td>As mensagens de rastreamento de logs enviadas a partir de seu c&oacute;digo ao ouvinte de rastreamento (um ouvinte de rastreamento deve ser adicionado ao arquivo web.config ou app.config). Os dados em log ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod para a tabela de armazenamento WADLogsTable.</td>
            <td>Sim</td>
            <td>N&atilde;o (Use EventSource)</td>
    </tr>
    <tr>
            <td>Logs IIS</td>
            <td>Sim</td>
            <td>Blob</td>
            <td>Informa&ccedil;&otilde;es de logs sobre sites IIS. Os dados em log ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod ao cont&ecirc;iner que voc&ecirc; especificar.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Logs de infraestrutura de diagn&oacute;stico do Azure</td>
            <td>Sim</td>
            <td>Tabela</td>
            <td>As informa&ccedil;&otilde;es de logs sobre a infraestrutura de diagn&oacute;stico, o m&oacute;dulo RemoteAccess e o m&oacute;dulo RemoteForwarder. Os dados de log ser&atilde;o transferidos no intervalo de scheduledTransferPeriodtransfer para a tabela de armazenamento WADDiagnosticInfrastructureLogsTable.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Logs de solicita&ccedil;&atilde;o com falha IIS</td>
            <td>No</td>
            <td>Blob</td>
            <td>Informa&ccedil;&otilde;es de logs sobre solicita&ccedil;&otilde;es falhas para um site IIS ou aplicativo. Voc&ecirc; tamb&eacute;m deve habilitar atrav&eacute;s das op&ccedil;&otilde;es de rastreamento de configura&ccedil;&otilde;es em system.WebServer nos dados de log Web.config. Os dados em log ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod ao cont&ecirc;iner que voc&ecirc; especificar.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Log de eventos do Windows</td>
            <td>No</td>
            <td>Tabela</td>
            <td>Informa&ccedil;&otilde;es de logs sobre qu&atilde;o bem o sistema operacional, o aplicativo ou o driver est&aacute; sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes s&atilde;o adicionados, os dados do contador de desempenho ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Contadores de desempenho</td>
            <td>No</td>
            <td>Tabela</td>
            <td>Informa&ccedil;&otilde;es de logs sobre qu&atilde;o bem o sistema operacional, o aplicativo ou o driver est&aacute; sendo executado. Os contadores de desempenho devem ser especificados explicitamente. Quando estes s&atilde;o adicionados, os dados do contador de desempenho ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod para a tabela de armazenamento WADPerformanceCountersTable.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Despejos de falhas</td>
            <td>No</td>
            <td>Blob</td>
            <td>Informa&ccedil;&otilde;es de logs sobre o estado do sistema operacional no evento de uma falha do sistema. Os despejos da mini-falha s&atilde;o coletados localmente. Os despejos completos podem ser habilitados. Os dados em log ser&atilde;o transferidos no intervalo de transfer&ecirc;ncia scheduledTransferPeriod ao cont&ecirc;iner que voc&ecirc; especificar. Pelo fato do ASP.NET manipular mais exce&ccedil;&otilde;es, isso geralmente &eacute; &uacute;til somente para uma fun&ccedil;&atilde;o de trabalho ou um VM.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Logs de erros personalizados</td>
            <td>N&atilde;o</td>
            <td>Blob</td>
            <td>Ao usar os recursos de armazenamento local, os dados personalizados podem ser registrados e transferidos imediatamente para o cont&ecirc;iner que voc&ecirc; especificar.</td>
            <td>Sim</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>EventSource</td>
            <td>N&atilde;o</td>
            <td>Tabela</td>
            <td>Os eventos de log gerados pelo seu c&oacute;digo usando a classe .NET EventSource.</td>
            <td>N&atilde;o</td>
            <td>Sim</td>
    </tr>
    <tr>
            <td>Manifesto com base no ETW</td>
            <td>N&atilde;o</td>
            <td>Tabela</td>
            <td>Eventos ETW gerados por qualquer processo.</td>
            <td>N&atilde;o</td>
            <td>Sim</td>
    </tr>
</tbody>
</table>



## <a name="additional"></a><span class="short-header">Recursos adicionais</span>Recursos adicionais

-   [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]
-   [Coletar dados do log usando o Diagnóstico do Azure (a página pode estar em inglês)][Coletar dados do log usando o Diagnóstico do Azure (a página pode estar em inglês)]
-   [Depurando um aplicativo do Azure][Depurando um aplicativo do Azure]
-   [Configurando o Diagnóstico do Azure (a página pode estar em inglês)][Configurando o Diagnóstico do Azure (a página pode estar em inglês)]

  [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh771389.aspx
  [Visão geral]: #overview
  [Como habilitar o Diagnostics em uma Função do Trabalho]: #worker-role
  [Como habilitar o Diagnostics em uma Máquina Virtual]: #virtual-machine
  [Arquivo de Configuração de Amostra e Esquema]: #configuration-file-schema
  [Solucionar problemas]: #troubleshooting
  [Perguntas frequentes]: #faq
  [Comparando o Azure Diagnostics 1.0 e 1.2]: #comparing
  [Recursos adicionais]: #additional
  [.NET EventSource Class]: http://msdn.microsoft.com/pt-br/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  [Teste Gratuito]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
  [CloudServices\_diag\_add\_xml]: ./media/cloud-services-dotnet-diagnostics/AddXmlFile.png
  [CloudServices\_diag\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleTables.png
  [CloudServices\_diag\_new\_project]: ./media/cloud-services-dotnet-diagnostics/NewProject.png
  [CloudServices\_diag\_wadexamplevm\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png
  [Esquema de Configuração do Azure Diagnostics 1.2]: http://msdn.microsoft.com/pt-br/library/azure/dn782207.aspx
  [decodificá-los]: http://www.bing.com/search?q=base64+decoder
  [Coletar dados do log usando o Diagnóstico do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433048.aspx
  [Depurando um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405479.aspx
  [Configurando o Diagnóstico do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn186185.aspx
