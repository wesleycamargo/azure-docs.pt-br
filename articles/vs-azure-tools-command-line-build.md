---
title: Build de linha de comando do Azure | Microsoft Docs
description: Compilação de linha de comando do Azure
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2016
ms.author: tarcher

---
# Compilação de linha de comando do Azure
## Visão geral
Você pode criar um pacote para implantação do Azure executando o MSBuild a um prompt de comando. Você pode configurar e definir compilações para depuração, preparo e produção, além de automatizar parte do processo de compilação.

## Microsoft Build Engine (MSBuild)
Ao usar o Microsoft Build Engine (MSBuild) você pode criar produtos nos ambientes de laboratório de compilação em que o Visual Studio não está instalado. O MSBuild usa um formato XML para arquivos de projeto extensíveis e com suporte total da Microsoft. Nesse formato de arquivo, é possível descrever quais itens devem ser compilados para uma ou mais plataformas e configurações.

Você também pode executar o MSBuild a um prompt de comando, e este tópico descreve essa abordagem. Ao configurar propriedades a um prompt de comando, você pode compilar configurações específicas de um projeto. Da mesma forma, você também pode definir os destinos que o comando MSBuild criará. Para obter mais informações sobre parâmetros de linha de comando e sobre o MSBuild, consulte [Referência de linha de comando do MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## Instalação
Como o procedimento a seguir descreve, você deve instalar o software e as ferramentas necessárias no servidor de compilação antes de criar um pacote do Azure usando o MSBuild:

1. Instale o .NET Framework 4 ou posterior, que inclui o MSBuild.
2. Instale as [Ferramentas de criação do Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (procure MicrosoftAzureAuthoringTools-x64.msi or MicrosoftAzureAuthoringTools-x86.msi).
3. Instale as [Bibliotecas do Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (procure MicrosoftAzureLibsForNet-x64.msi ou MicrosoftAzureLibs-x86.msi).
4. Copie o arquivo Microsoft.WebApplication.targets de uma instalação do Visual Studio em outro computador.
   
    O arquivo está localizado no diretório C:\\Program Files (x86)\\MSBuild\\Microsoft\\Visual Studio\\v12.0\\WebApplications (v11.0 para Visual Studio 2012) e você deve copiá-lo no mesmo diretório no servidor de compilação.
5. Instale as [Ferramentas do Azure para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).
   
    Procure WindowsAzureTools.vs120.exe para compilar projetos do Visual Studio 2013.

## Parâmetros do MSBuild
A maneira mais simples de criar um pacote é executar o MSBuild com a opção `/t:Publish`. Por padrão, esse comando cria um diretório relacionado à pasta raiz do projeto, como ProjectDir\\bin\\Configuration\\app.publish. Ao criar um projeto do Azure, você gera dois arquivos, o arquivo do pacote propriamente dito e o arquivo de configuração que o acompanha:

* Project.cspkg
* ServiceConfiguration.TargetProfile.cscfg

Por padrão, cada projeto do Azure inclui um arquivo de configuração de serviço para compilações locais (depuração) e outro para compilações de nuvem (de preparo ou produção), mas você pode adicionar ou remover arquivos de configuração de serviço conforme necessário. Quando compila um pacote dentro do Visual Studio, você é questionado quanto ao arquivo de configuração de serviço a ser incluído com o pacote. Quando você compila um pacote usando o MSBuild, o arquivo de configuração de serviço local é incluído por padrão. Para incluir um arquivo de configuração de serviço diferente, defina a propriedade `TargetProfile` do comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se você quiser usar um diretório alternativo para o pacote armazenado e arquivos de configuração, defina o caminho usando a opção `/p:PublishDir=Directory`, incluindo o separador de barra invertida à direita.

## Implantação
Depois que o pacote é compilado, você pode implantá-lo no Azure. Para obter um tutorial que demonstre esse processo, consulte o site do Azure. Para obter informações sobre como automatizar esse processo, consulte [Entrega contínua de serviços de nuvem no Azure](cloud-services/cloud-services-dotnet-continuous-delivery.md).

<!---HONumber=AcomDC_0817_2016-->