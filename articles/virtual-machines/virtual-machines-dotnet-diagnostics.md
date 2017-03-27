---
title: "Como usar o diagnóstico do Azure em Máquinas Virtuais | Microsoft Docs"
description: "Usando o diagnóstico do Azure para coletar dados de Máquinas Virtuais do Azure para depuração, medição de desempenho, monitoramento, análise de tráfego e muito mais."
services: virtual-machines
documentationcenter: .net
author: davidmu1
manager: 
editor: 
ms.assetid: dfaabc7a-23e7-4af0-8369-f504d2915b3d
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/16/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 529c723a9e071b7cc388cf92423c1f74707cb831
ms.lasthandoff: 03/22/2017


---
# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Habilitando o diagnóstico em Máquinas Virtuais do Azure
Confira [Visão geral do Diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md) para obter informações preliminares sobre o Diagnóstico do Azure.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Como habilitar o Diagnostics em uma Máquina Virtual
Esse passo a passo descreve como instalar remotamente o Diagnostics em uma máquina virtual do Azure a partir de um computador de desenvolvedor. Você também aprende como implementar um aplicativo que executa nesta máquina virtual do Azure e emite dados de telemetria usando o .NET [EventSource Class][EventSource Class]. O Diagnóstico do Azure é usado para coletar a telemetria e armazená-la em uma conta de armazenamento do Azure.

### <a name="pre-requisites"></a>Pré-requisitos
Esse passo a passo assume que você tem uma assinatura do Azure e está usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma assinatura do Azure, será possível se inscrever para uma [Avaliação Gratuita][Free Trial]. Certifique-se de [Instalar e configurar o Azure PowerShell, versão 0.8.7 ou posterior][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-virtual-machine"></a>Etapa 1: Criar uma máquina virtual
1. No computador do desenvolvedor, inicie o Visual Studio 2013.
2. No **Gerenciador de Servidores** do Visual Studio, expanda o **Azure**, clique com botão direito do mouse em **Máquinas Virtuais** e, em seguida, selecione **Criar Máquina Virtual**.
3. Selecione sua assinatura do Azure na caixa de diálogo **Escolha uma assinatura** e clique em **Avançar**.
4. Selecione **Windows Server 2012 R2 Datacenter, novembro de 2014** na caixa de diálogo **Selecionar uma Imagem da Máquina Virtual** e clique em **Avançar**.
5. Nas **Configurações Básicas da Máquina Virtual**, defina o nome da máquina virtual para "wadexample". Defina o nome de usuário e senha do Administrador clique em **Avançar**.
6. Na caixa de diálogo **Configurações de Serviço de Nuvem** , crie um novo serviço de nuvem chamado "wadexampleVM". Crie uma nova conta de Armazenamento chamada "wadexample" e clique em **Avançar**.
7. Clique em **Criar**.

### <a name="step-2-create-your-application"></a>Etapa 2: Criar o seu aplicativo
1. No computador do desenvolvedor, inicie o Visual Studio 2013.
2. Crie um novo aplicativo de console do Visual C# que se destina ao .NET Framework 4.5. Atribua o nome “WadExampleVM” ao projeto.
   ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3. Substitua os conteúdos do Program.cs pelo código a seguir. A classe **SampleEventSourceWriter** implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método WriteEvent define a ID para o respectivo evento. O método Executar implementa um loop infinito que chama cada um dos métodos de registro implementados na classe **SampleEventSourceWriter** a cada 10 segundos.

    ```csharp
     using System;
     using System.Diagnostics;
     using System.Diagnostics.Tracing;
     using System.Threading;

     namespace WadExampleVM
     {
       sealed class SampleEventSourceWriter : EventSource {
         public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
         public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); } // Cast enums to int for efficient logging.
         public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
         public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
         public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }
       }

       enum MyColor {
         Red,
         Blue,
         Green
       }

       [Flags]
       enum MyFlags {
         Flag1 = 1,
         Flag2 = 2,
         Flag3 = 4
       }

       class Program
       {
         static void Main(string[] args) {
         Trace.TraceInformation("My application entry point called");

         int value = 0;

         while (true) {
             Thread.Sleep(10000);
             Trace.TraceInformation("Working");

             // Emit several events every time we go through the loop
             for (int i = 0; i < 6; i++) {
                 SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
             }

             for (int i = 0; i < 3; i++) {
                 SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                 SampleEventSourceWriter.Log.SetOther(true, 123456789);
             }

             if (value == int.MaxValue) value = 0;
             SampleEventSourceWriter.Log.HighFreq(value++);
         }

        }
      }
     }
     ```
4. Salve o arquivo e selecione **Compilar Solução** no menu **Compilar** para compilar seu código.

### <a name="step-3-deploy-your-application"></a>Etapa 3: Implantar seu aplicativo
1. Clique com o botão direito do mouse no projeto **WadExampleVM** no **Gerenciador de Soluções** e selecione **Abrir Pasta no Explorador de Arquivos**.
2. Navegue para a pasta *bin\Debug* e copie todos os arquivos (WadExampleVM.*)
3. No **Gerenciador de Servidores**, clique com o botão direito do mouse na máquina virtual e selecione **Conectar usando a Área de Trabalho Remota**.
4. Após conectado ao VM, crie uma pasta com o nome de WadExampleVM e cole seus arquivos do aplicativo na pasta.
5. Inicie o aplicativo WadExampleVM.exe. Você verá uma janela de console em branco.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Etapa 4: Criar sua configuração do Diagnostics e instalar a extensão
1. Baixe a definição do esquema do arquivo de configuração pública para seu computador de desenvolvimento ao executar o seguinte comando PowerShell:

     (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
2. Abra um novo arquivo XML no Visua Studio, seja em um projeto que você já abriu, ou em uma instância do Visual Studio com nenhum projeto aberto. No Visual Studio, selecione **Adicionar** -> **Novo Item…** -> **Itens do Visual C#** -> **Dados** -> **Arquivo XML**. Nomeie o arquivo como "WadExample.xml"
3. Associe o WadConfig.xsd com o arquivo de configuração. Certifique-se de que a janela do editor WadExample.xml é uma janela ativa. Pressione **F4** para abrir a janela **Propriedades**. Clique na propriedade **Schemas** da janela **Propriedades**. Clique em**...** in the **Esquemas** . Clique em **Adicionar…** e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **OK**.
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Etapa 5: instalar remotamente o Diagnostics na sua Máquina Virtual do Azure
Os cmdlets do PowerShell para gerenciar Diagnostics em uma máquina virtual são: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1. No computador do desenvolvedor, abra o PowerShell do Azure.
2. Execute o script para instalar remotamente o Diagnostics no seu VM (substitua o *StorageAccountKey* com a chave de conta de armazenamento para sua conta de armazenamento wadexamplevm):

     $storage_name = "wadexamplevm"   $key = "<StorageAccountKey>"   $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"   $service_name="wadexamplevm"   $vm_name="WadExample"   $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key   $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name   $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext   $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### <a name="step-6-look-at-your-telemetry-data"></a>Etapa 6: Examinar os dados de telemetria
No **Gerenciador de Servidores** do Visual Studio, navegue até a conta de armazenamento wadexample. Depois que a VM estiver em execução por cerca de 5 minutos, você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foi coletada.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Esquema de arquivo de configuração
O arquivo de configuração do Diagnóstico define valores que são usados para inicializar as definições de configurações do diagnóstico quando o agente de diagnóstico é iniciado. Veja a [referência de esquema mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para obter valores válidos e exemplos.

## <a name="troubleshooting"></a>Solucionar problemas
Veja [Solucionando problemas do Diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Ver uma lista de artigos sobre o Diagnóstico do Azure relacionados a máquinas virtuais](../monitoring-and-diagnostics/azure-diagnostics.md#virtual-machines-using-azure-diagnostics) para alterar os dados coletados, solucionar problemas ou saber mais sobre o diagnóstico em geral.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

