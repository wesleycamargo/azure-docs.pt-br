---
title: Como usar o diagnóstico do Azure (.NET) com os Serviços de Nuvem | Microsoft Docs
description: Usando o diagnóstico do Azure para coletar dados dos Serviços de Nuvem do Azure para depuração, medição de desempenho, monitoramento, análise de tráfego e muito mais.
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/25/2016
ms.author: robb

---
# Habilitando o Diagnóstico do Azure nos Serviços de Nuvem do Azure
Confira [Visão geral do Diagnóstico do Azure](../azure-diagnostics.md) para obter informações preliminares sobre o Diagnóstico do Azure.

## Como habilitar o Diagnostics em uma Função do Trabalho
Este passo a passo descreve como implementar uma função de trabalho do Azure que emite dados de telemetria usando o .NET EventSource Class. O Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-los em uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio permite automaticamente O Diagnóstico 1.0 como parte da solução em SDKs do Azure para .NET 2.4 e versões anteriores. As instruções a seguir descrevem o processo de criação da função de trabalho, desabilitando o Diagnóstico 1.0 por meio da solução e implantando o Diagnóstico 1.2 ou 1.3 para sua função de trabalho.

### Pré-requisitos
Esse artigo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma conta do Azure, será possível se inscrever para um [Teste Gratuito][Teste Gratuito]. Certifique-se de [Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior][Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior].

### Etapa 1: criar uma função de trabalho
1. Inicie o **Visual Studio 2013**.
2. Crie um novo projeto do **Serviço de Nuvem do Azure** no modelo de **Nuvem** que se destina ao .NET Framework 4.5. Atribua o nome "WadExample" ao projeto e clique em Ok.
3. Selecione **Função de Trabalho** e clique em Ok. O projeto será criado.
4. No **Gerenciador de Soluções**, clique duas vezes no arquivo de propriedades **WorkerRole1**.
5. Na guia **Configuração**, desmarque **Habilitar o diagnóstico** para desabilitar o Diagnostics 1.0 (SDK 2.4 do Azure mais recentes).
6. Compile sua solução para verificar que você não tem erros.

### Etapa 2: Instrumentalizar seu código
Substitua os conteúdos do WorkerRole.cs pelo código a seguir. A classe SampleEventSourceWriter, herdada da [classe EventSource][classe EventSource], implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

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
1. Implante sua função de trabalho para o Azure dentro do Visual Studio selecionando o projeto **WadExample** no Gerenciador de Soluções e, em seguida, **Publicar** no menu **Compilar**.
2. Escolha sua assinatura.
3. Na caixa de diálogo **Configurações do Microsoft Azure Publish**, selecione **Criar Novo...**.
4. Na caixa de diálogo **Criar Serviço de Nuvem e Conta de Armazenamento**, insira um **Nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5. Defina o **Ambiente** para **De Preparo**.
6. Modifique quaisquer outras **Configurações** conforme apropriado, e clique em **Publicar**.
7. Após a implantação ter sido concluída, verifique no portal clássico do Azure se seu serviço de nuvem está em estado de **Execução**.

### Etapa 4: Criar seu arquivo de configuração do Diagnostics e instalar a extensão
1. Baixe a definição de esquema do arquivo de configuração pública executando o seguinte comando do PowerShell: 2. (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
2. Adicione um arquivo XML ao seu projeto **WorkerRole1** ao clicar com o botão direito do mouse no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item…** -> **Itens Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml".
   
   ![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela de **Propriedades**. Clique na propriedade **Esquemas** na janela **Propriedades**. Clique em **…** na propriedade **Esquemas**. Clique no botão **Adicionar…** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
4. Substitua os conteíudos do arquivo de configuração WadExample.xml com o seguinte XML e salve o arquivo. Este arquivo de configuração define dois contadores de desempenho a serem coletados: um relacionado à utilização da CPU e um ao uso de memória. Após a configuração, defina os quatro eventos correspondentes aos métodos na classe SampleEventSourceWriter.

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

1. Abra o PowerShell do Azure.
2. Execute o script para instalar o Diagnostics na sua função de trabalho (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexample):

```
    $storage_name = "wadexample"
    $key = "<StorageAccountKey>"
    $config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
    $service_name="wadexample"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### Etapa 6: Examinar os dados de telemetria
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que o serviço de nuvem foi executado por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## Esquema de arquivo de configuração
O arquivo de configuração do Diagnóstico define valores que são usados para inicializar as definições de configurações do diagnóstico quando o agente de diagnóstico é iniciado. Veja a [referência de esquema mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para obter valores válidos e exemplos.

## Solucionar problemas
Caso tenha problemas, veja [Solucionando problemas do Diagnóstico do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## Próximas etapas
[Ver uma lista de artigos sobre o Diagnóstico do Azure relacionados a máquinas virtuais](../azure-diagnostics.md#cloud-services) para alterar os dados coletados, solucionar problemas ou saber mais sobre o diagnóstico em geral.

[classe EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Teste Gratuito]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure, versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->