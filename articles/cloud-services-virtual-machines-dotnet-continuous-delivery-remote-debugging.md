<properties 
	pageTitle="Habilitar a depuração remota com entrega contínua" 
	description="Saiba como habilitar a depuração remota ao utilizar a entrega contínua para implantar no Azure." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>
# Habilitar a depuração remota ao utilizar a entrega contínua para publicar no Azure

Você pode habilitar a depuração remota no Azure ao utilizar a [entrega contínua](cloud-services-dotnet-continuous-delivery.md) para publicar no Azure seguindo as etapas abaixo.

Neste tópico:

[Habilitando a depuração remota para serviços de nuvem](#cloudservice)

[Habilitando a depuração remota para máquinas virtuais](#virtualmachine)

<h2> <a name="cloudservice"></a>Habilitando a depuração remota para serviços de nuvem</h2>

1. No agente de compilação, configure o ambiente inicial para o Azure conforme descrito em [Compilação de linha de comando para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Como o tempo de execução de depuração remota (msvsmon.exe) é exigido para o pacote, instale as ferramentas [Remote Tools for Visual Studio 2013](http://www.microsoft.com/download/details.aspx?id=40781) (ou [Remote Tools for Visual Studio 2012 Update 4](http://www.microsoft.com/download/details.aspx?id=38184) se estiver usando o Visual Studio 2012). Você pode, como alternativa, copiar os binários de depuração remota de um sistema que tiver o Visual Studio instalado.
3. Crie um certificado conforme descrito em [Criar um certificado de serviço para o Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx). Mantenha o .pfx e a impressão digital do certificado RDP e carregue o certificado no serviço de nuvem alvo.
4. Utilize as opções a seguir na linha de comando do MSBuild para compilar e criar o pacote com a depuração remota habilitada. (Atualize os caminhos para seus arquivos de sistema e projeto.)

	/TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5. Publique no serviço de nuvem alvo usando o pacote e o arquivo .cscfg gerado na etapa anterior.
6. Importe o certificado (arquivo .pfx) para a máquina que tem o Visual Studio com o SDK 2.4 do Azure instalado.

<h2> <a name="virtualmachine"></a>Habilitando a depuração remota para máquinas virtuais</h2>

1. Crie uma máquina virtual do Azure. Consulte [Criar uma máquina virtual executando o Windows Server](virtual-machines-windows-tutorial.md) ou [Criando máquinas virtuais do Azure no Visual Studio](http://msdn.microsoft.com/library/azure/dn569263.aspx).
2. Na [página do Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), consulte o painel da máquina virtual para ver a “Impressão digital do certificado RDP” da máquina virtual. Ele é usado como o valor de ServerThumbprint na configuração da extensão.
3. Crie um certificado cliente conforme descrito em [Criar um certificado de serviço para o Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx) (mantenha o .pfx e a impressão digital do certificado RDP).
4. Instale o [PowerShell do Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (versão 0.7.4 ou posterior) a partir do Centro de Download da Microsoft.
5. Execute o script a seguir para habilitar a extensão RemoteDebug. Substitua os dados pessoais pelos seus dados, como seu nome de inscrição, nome de serviço e impressão digital. (OBSERVAÇÃO: este script está configurado para Visual Studio 2013. Se você estiver usando o Visual Studio 2012, use "RemoteDebugVS2013" para ReferenceName e ExtensionName.)

	<pre>
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
$publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

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
</pre>
    
6. Importe o certificado (.pfx) para a máquina que tem o Visual Studio com o SDK do Azure para .NET 2.4 instalado.
<!--HONumber=54-->