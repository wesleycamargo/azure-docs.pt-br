<properties urlDisplayName="Visual Studio 14 CTP" pageTitle="Instalando o CTP2 "14" do SDK do Azure 2.4 para Visual Studio" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Instalando o CTP de "14" do SDK do Azure 2.4 para Visual Studio

Para instalar os CTPs "14" do SDK do Azure 2.4 para Visual Studio, siga as etapas a seguir. Este procedimento instala SDKs, ferramentas básicas e estendidas para CTPs "14" para desenvolvimento no Azure com Visual Studio, não sendo destinado a ser utilizado com nenhuma outra versão do Visual Studio.

**Observação**: O SDK do Azure 2.4 não é compatível com o CTP1 "14" do Visual Studio.

Para instalar o SDK do Azure 2.4 para .NET, siga as etapas a seguir:

1. Instale o mais recente [CTP "14" para Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=400776).

2. Instale cada componente do SDK do Azure utilizando os links na lista a seguir, na ordem exibida. Escolha a versão x86 ou x64 de cada um dos componentes a seguir.

       <ul>
        <li>Ferramentas de criação do Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Emulador de computação do Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>As bibliotecas de cliente do Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>O emulador de armazenamento: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Se você receber um aviso relacionado a bancos de dados SQL locais, instale o SQL Server LocalDB 11.0 por meio <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">deste local</a> para x86 ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">deste local</a> para x64.</li><li> Ferramentas do Azure para Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Problemas conhecidos

1. Se você instalar o CTP2 "14" para Visual Studio em um computador com o Visual Studio 2013 instalado, você não poderá iniciar os serviços móveis no CTP2 "14" do Visual Studio. Para contornar esse problema, acrescente uma referência  aos assemblies a seguir em seu projeto de serviços móveis:

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. A depuração remota para sites do Azure e Serviços Móveis não funciona no CTP2 "14" do Visual Studio.

## Notas de versão

Leia as [notas de versão](http://go.microsoft.com/fwlink/?LinkId=507517) para o SDK do Azure 2.4.
