---
title: "Automatizar a implantação do seu aplicativo do Azure com as ferramentas de linha de comando | Microsoft Docs"
description: "Descobrir informações sobre como você pode implantar seu aplicativo do Azure pela linha de comando"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 2cc0c3a1afd42d1c04e8a220b66bcc6179d7ff33
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>Automatizar a implantação do seu aplicativo do Azure com as ferramentas de linha de comando
Você pode automatizar a implantação dos seus aplicativos do Azure com as ferramentas de linha de comando. Este artigo lista as ferramentas disponíveis e os links úteis que mostram como usá-los no fluxo de trabalho de implantação. 

## <a name="msbuild"></a>Automatizar a implantação com o MSBuild
Se você usar o [IDE do Visual Studio](#vs) para desenvolvimento, poderá usar o [MSBuild](http://msbuildbook.com/) para automatizar qualquer tarefa que possa ser executada no seu IDE. Você pode configurar o MSBuild para usar a [Implantação da Web](#webdeploy) ou o [FTP/FTPS](#ftp) para copiar arquivos. A Implantação da Web também pode automatizar várias outras tarefas relacionadas à implantação tarefas, como a implantação de bancos de dados.

Para obter mais informações sobre a implantação de linha de comando usando o MSBuild, consulte os recursos a seguir:

* [Implantação da Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). O décimo de uma série de tutoriais sobre implantação no Azure usando o Visual Studio. Mostra como usar a linha de comando para implantar após a configuração de perfis de publicação no Visual Studio.
* [Por dentro do Microsoft Build Engine: usando o MSBuild e o Team Foundation Build](http://msbuildbook.com/). Livro impresso que contém capítulos sobre como usar o MSBuild para implantação.

## <a name="powershell"></a>Automatizar a implantação com o Windows PowerShell
Você pode executar funções de implantação do MSBuild ou de FTP a partir do [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Se fizer isso, você também poderá usar uma coleção de cmdlets do Windows PowerShell que facilitam chamar a API de gerenciamento do REST do Azure.

Para saber mais, consulte os recursos a seguir:

* [Provisionar e implantar microsserviços previsíveis no Azure](app-service-deploy-complex-application-predictably.md)
* [Compilando aplicativos de nuvem do mundo real com o Azure - automatizar tudo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de livro eletrônico que explica como o aplicativo de amostra mostrado no livro eletrônico usa scripts do Windows PowerShell para criar um ambiente de teste do Azure e implantá-lo. Consulte a seção [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obter links para a documentação adicional do PowerShell do Azure.
* [Usando scripts do Windows PowerShell para publicar em ambientes de desenvolvimento e de teste](../vs-azure-tools-publishing-using-powershell-scripts.md). Como usar os scripts de implantação do Windows PowerShell gerados pelo Visual Studio.

## <a name="api"></a>Automatizar a implantação com a API de gerenciamento do .NET
Você pode escrever um código C# para executar as funções do MSBuild ou do FTP para implantação. Se fizer isso, você poderá acessar a API de gerenciamento do REST do Azure para executar as funções de gerenciamento de site.

Para obter mais informações, consulte o recurso a seguir:

* [Automatizando tudo com as bibliotecas de gerenciamento do Azure e o .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introdução à API de gerenciamento do .NET e links para mais documentação.

## <a name="cli"></a>Implantar da CLI (Interface de Linha de Comando) do Azure
É possível utilizar a linha de comando em máquinas Windows, Mac ou Linux para implantar utilizando o FTP. Se fizer isso, você também poderá acessar a API de gerenciamento do REST do Azure usando a CLI do Azure.

Para obter mais informações, consulte o recurso a seguir:

* <seg>
  [Ferramentas de linha de comando do Azure](https://azure.microsoft.com/downloads/).</seg> Página de portal em Azure.com que fornece informações da ferramenta de linha de comando.

## <a name="webdeploy"></a>Implantar por meio da linha de comando de Implantação da Web
[Implantação da Web](http://www.iis.net/downloads/microsoft/web-deploy) é o software da Microsoft para a implantação do IIS que não só fornece recursos de sincronização de arquivos inteligentes, mas também pode executar ou coordenar várias outras tarefas relacionadas à implantação que não poderão ser automatizadas quando você usar o FTP. Por exemplo, a Implantação da Web pode implantar um novo banco de dados ou atualizações de banco de dados junto com seu aplicativo Web. A Implantação da Web também pode minimizar o tempo necessário para atualizar um site existente, pois ele pode copiar apenas os arquivos alterados de forma inteligente. O Microsoft Visual Studio e o Team Foundation Server têm suporte para a Implantação da Web interna, mas você também pode usar a Implantação da Web diretamente da linha de comando para automatizar a implantação. Os comandos de Implantação da Web são muito avançados, mas a curva de aprendizado pode ser acentuada.

## <a name="more-resources"></a>Mais recursos
Outra opção de implantação à automação da linha de comando é usar um serviço baseado em nuvem, como o [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obter mais informações, consulte [Implantar aplicativos ASP.NET em Sites do Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

Para obter mais informações sobre ferramentas de linha de comando, confira os seguintes recursos:

* [Aplicativos Web simples: implantação](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre uma ferramenta que ele escreveu para facilitar o uso da Implantação da Web.
* [Ferramenta de Implantação de Web](http://technet.microsoft.com/library/dd568996). Documentação oficial no site do Microsoft TechNet. Desatualizada, mas ainda um bom ponto de partida.
* [Usando a Implantação da Web](http://www.iis.net/learn/publish/using-web-deploy). Documentação oficial no site do Microsoft IIS.NET. Também desatualizada, mas ainda um bom ponto de partida.
* [Implantação da Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). O MSBuild é o mecanismo de compilação usado pelo Visual Studio e também pode ser usado na linha de comando para implantar aplicativos Web em "Aplicativos Web". Este tutorial faz parte de uma série que trata principalmente da implantação do Visual Studio.


