<properties urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Habilitar a depura&ccedil;&atilde;o remota com entrega cont&iacute;nua" metaKeywords="" description="Saiba como habilitar a depura&ccedil;&atilde;o remota ao utilizar a entrega cont&iacute;nua para implantar no Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Habilitar a depura&ccedil;&atilde;o remota ao utilizar a entrega cont&iacute;nua para publicar no Azure" authors="kempb" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="kempb" />

# Habilitar a depuração remota ao utilizar a entrega contínua para publicar no Azure

Você pode habilitar a depuração remota no Azure ao utilizar a [entrega contínua][entrega contínua] para publicar no Azure seguindo as etapas abaixo.

Neste tópico:

[Habilitando a depuração remota para serviços de nuvem][Habilitando a depuração remota para serviços de nuvem]

[Habilitando a depuração remota para máquinas virtuais][Habilitando a depuração remota para máquinas virtuais]

## <a name="cloudservice"></a>Habilitando a depuração remota para serviços de nuvem

1.  No agente de compilação, configure o ambiente inicial para o Azure conforme descrito em [Compilação de linha de comando para Azure][Compilação de linha de comando para Azure].
2.  Como o tempo de execução de depuração remota (msvsmon.exe) é exigido para o pacote, instale as ferramentas [Remote Tools for Visual Studio 2013][Remote Tools for Visual Studio 2013] (ou [Remote Tools for Visual Studio 2012 Update 4][Remote Tools for Visual Studio 2012 Update 4] se estiver usando o Visual Studio 2012). Você pode, como alternativa, copiar os binários de depuração remota de um sistema que tiver o Visual Studio instalado.
3.  Crie um certificado conforme descrito em [Criar um certificado de serviço para o Azure][Criar um certificado de serviço para o Azure]. Mantenha o .pfx e a impressão digital do certificado RDP e carregue o certificado no serviço de nuvem alvo.
4.  Utilize as opções a seguir na linha de comando do MSBuild para compilar e criar o pacote com a depuração remota habilitada. (Atualize os caminhos para seus arquivos de sistema e projeto.)

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Publique no serviço de nuvem alvo usando o pacote e o arquivo .cscfg gerado na etapa anterior.
6.  Importe o certificado (arquivo .pfx) para a máquina que tem o Visual Studio com o SDK 2.4 do Azure instalado.

## <a name="virtualmachine"></a>Habilitando a depuração remota para máquinas virtuais

1.  Crie uma máquina virtual do Azure. Consulte [Criar uma máquina virtual executando o Windows Server][Criar uma máquina virtual executando o Windows Server] ou [Criando máquinas virtuais do Azure no Visual Studio][Criando máquinas virtuais do Azure no Visual Studio].
2.  Na [página do Portal do Azure][página do Portal do Azure], consulte o painel da máquina virtual para ver a “Impressão digital do certificado RDP” da máquina virtual. Ele é usado como o valor de ServerThumbprint na configuração da extensão.
3.  Crie um certificado cliente conforme descrito em [Criar um certificado de serviço para o Azure][Criar um certificado de serviço para o Azure] (mantenha o .pfx e a impressão digital do certificado RDP).
4.  Instale o [PowerShell do Azure][PowerShell do Azure] (versão 0.7.4 ou posterior) a partir do Centro de Download da Microsoft.
5.  Execute o script a seguir para habilitar a extensão RemoteDebug. Substitua os dados pessoais pelos seus dados, como seu nome de inscrição, nome de serviço e impressão digital. (OBSERVAÇÃO: Este script é configurado para o Visual Studio 2013. Se você estiver usando o Visual Studio 2012, use "RemoteDebugVS2013" para ReferenceName e ExtensionName.)

        Add-AzureAccount

        Select-AzureSubscription "My Microsoft Subscription"

        $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

        $endpoints = @(
        ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
        ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
        )

        foreach($endpoint in $endpoints)
        {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
        }

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

        $vm | Set-AzureVMExtension `
        -ReferenceName $referenceName `
        -Publisher $publisher `
        -ExtensionName $extensionName `
        -Version $version `
        -PublicConfiguration $publicConfiguration

        foreach($extension in $vm.VM.ResourceExtensionReferences)
        {   
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
        $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
        break
        }
        }

        $vm | Update-AzureVM 

6.  Importe o certificado (.pfx) para a máquina que tem o Visual Studio com o SDK do Azure para .NET 2.4 instalado.

  [entrega contínua]: http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Habilitando a depuração remota para serviços de nuvem]: #cloudservice
  [Habilitando a depuração remota para máquinas virtuais]: #virtualmachine
  [Compilação de linha de comando para Azure]: http://msdn.microsoft.com/pt-br/library/hh535755.aspx
  [Remote Tools for Visual Studio 2013]: http://www.microsoft.com/pt-br/download/details.aspx?id=40781
  [Remote Tools for Visual Studio 2012 Update 4]: http://www.microsoft.com/pt-br/download/details.aspx?id=38184
  [Criar um certificado de serviço para o Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Criar uma máquina virtual executando o Windows Server]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/
  [Criando máquinas virtuais do Azure no Visual Studio]: http://msdn.microsoft.com/pt-br/library/azure/dn569263.aspx
  [página do Portal do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [PowerShell do Azure]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
