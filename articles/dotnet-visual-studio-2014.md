<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

## Instalando o CTP de "14” do SDK do Azure 2.4 para Visual Studio

Para instalar os CTPs de "14” do SDK do Azure 2.4 para Visual Studio, siga as etapas a seguir. Este procedimento instala SDKs, ferramentas básicas e estendidas para CTPs de "14" para desenvolvimento no Azure com Visual Studio, não sendo destinado a ser utilizado com nenhuma outra versão do Visual Studio.

**Observação**: O SDK do Azure 2.4 não é compatível com o CTP1 "14" do Visual Studio.

Para instalar o SDK do Azure 2.4 para .NET, siga as etapas a seguir:

1.  Instale o mais recente [CTP "14" para Visual Studio][CTP "14" para Visual Studio].

2.  Instale cada componente do SDK do Azure utilizando os links na lista a seguir, na ordem exibida. Escolha a versão x86 ou x64 de cada um dos componentes a seguir.

    -   Ferramentas de criação do Azure: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] ou [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    -   Emulador de computação do Azure: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] ou [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    -   As bibliotecas de cliente do Azure: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] ou [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].
    -   O emulador de armazenamento: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. Se você receber um aviso relacionado a bancos de dados SQL locais, instale o SQL Server LocalDB 11.0 por meio [deste local][deste local] para x86 ou [deste local][1] para x64.
    -   Ferramentas do Azure para Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Problemas conhecidos

1.  Se você instalar o CTP2 "14" do Visual Studio em uma máquina com o Visual Studio 2013 instalado, você não poderá iniciar os serviços móveis no CTP2 "14" do Visual Studio. Para contornar esse problema, acrescente uma referência aos assemblies a seguir em seu projeto de serviços móveis:

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  A depuração remota para sites do Azure e Serviços Móveis não funciona no CTP2 "14" do Visual Studio.

## Notas de versão

Leia as [notas de versão][notas de versão] para o SDK do Azure 2.4.

  [CTP "14" para Visual Studio]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [deste local]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [notas de versão]: http://go.microsoft.com/fwlink/?LinkId=507517
