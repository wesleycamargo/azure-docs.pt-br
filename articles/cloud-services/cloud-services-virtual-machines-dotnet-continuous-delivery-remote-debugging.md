<properties
	pageTitle="Habilitar a depuração remota com entrega contínua | Microsoft Azure"
	description="Saiba como habilitar a depuração remota ao utilizar a entrega contínua para implantar no Azure."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Habilitar a depuração remota ao utilizar a entrega contínua para publicar no Azure

Você pode habilitar a depuração remota no Azure, para os serviços de nuvem ou máquinas virtuais, ao utilizar a [entrega contínua](cloud-services-dotnet-continuous-delivery.md) para publicar no Azure seguindo as etapas abaixo.

## Habilitando a depuração remota para serviços de nuvem

1. No agente de compilação, configure o ambiente inicial para o Azure conforme descrito em [Compilação de linha de comando para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Como o tempo de execução de depuração remota (msvsmon.exe) é exigido para o pacote, instale as ferramentas [Remote Tools for Visual Studio 2015](http://www.microsoft.com/pt-BR/download/details.aspx?id=48155) (ou [Remote Tools for Microsoft Visual Studio 2013 Update 5](https://www.microsoft.com/pt-BR/download/details.aspx?id=48156) se estiver usando o Visual Studio 2013). Você pode, como alternativa, copiar os binários de depuração remota de um sistema que tiver o Visual Studio instalado.
3. Crie um certificado conforme descrito em [Visão geral sobre certificados para os Serviços de Nuvem do Azure](cloud-services-certs-create.md). Mantenha o .pfx e a impressão digital do certificado RDP e carregue o certificado no serviço de nuvem alvo.
4. Utilize as opções a seguir na linha de comando do MSBuild para compilar e criar o pacote com a depuração remota habilitada. (Substitua os caminhos reais até seus arquivos do sistema e de projeto pelos itens entre colchetes angulares.)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` é o caminho até a pasta contendo msvsmon.exe em Ferramentas Remotas para Visual Studio.

5. Publique no serviço de nuvem alvo usando o pacote e o arquivo .cscfg gerado na etapa anterior.
6. Importe o certificado (arquivo .pfx) para a máquina que tem o Visual Studio com o SDK do Azure para .NET instalado.

## Habilitando a depuração remota para máquinas virtuais

1. Crie uma máquina virtual do Azure. Consulte [Criar uma máquina virtual executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar e gerenciar máquinas virtuais do Azure no Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Na [página do portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), exiba o painel da máquina virtual para conferir a **IMPRESSÃO DIGITAL DO CERTIFICADO RDP** da máquina virtual. Esse valor é usado como o valor de `ServerThumbprint` na configuração da extensão.
3. Crie um certificado cliente conforme descrito em [Visão geral sobre certificados para os Serviços de Nuvem do Azure](cloud-services-certs-create.md) (mantenha o .pfx e a impressão digital do certificado RDP).
4. Instale e configure o Azure PowerShell (versão 0.7.4 ou posterior) conforme descrito em [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md)
5. Execute o script a seguir para habilitar a extensão RemoteDebug. Substitua os caminhos e dados pessoais pelos seus dados, como seu nome de inscrição, nome de serviço e impressão digital.

	>[AZURE.NOTE] Esse script é configurado para o Visual Studio 2015. Se você estiver usando o Visual Studio 2013, modifique as atribuições `$referenceName` e `$extensionName` abaixo para usar `RemoteDebugVS2013` (em vez de `RemoteDebugVS2015`).

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
	
	$referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
	$publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
	$extensionName = "RemoteDebugVS2015"
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

6. Importe o certificado (.pfx) para a máquina que tem o Visual Studio com o SDK do Azure para .NET instalado.

<!---HONumber=AcomDC_0413_2016-->