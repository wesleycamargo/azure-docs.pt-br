---
title: "Como configurar o computador para desenvolvimento dos serviços de mídia com o .NET"
description: "Conheça os pré-requisitos para os Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET. Saiba também como criar um aplicativo do Visual Studio."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 612b58db48e160cb1b4cfef1f8f4c2b203061064
ms.lasthandoff: 03/10/2017


---
# <a name="media-services-development-with-net"></a>Desenvolvimento de serviços de mídia com o .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico discute como começar a desenvolver aplicativos de serviços de mídia usando o .NET.

A biblioteca do **SDK do .NET dos Serviços de Mídia** permite que você programe em relação aos serviços de mídia usando o .NET. Para facilitar ainda mais o desenvolvimento com o .NET, a biblioteca de **Extensões do SDK do .NET dos Serviços de Mídia** é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e funções auxiliares simplificam o seu código .NET. As duas bibliotecas estão disponíveis por meio do **NuGet** e **GitHub**.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta de Serviços de Mídia em uma assinatura nova ou existente do Azure. Confira o tópico [Criar uma conta dos Serviços de Mídia](media-services-portal-create-account.md).
* Sistemas operacionais: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Framework 4.5.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio
Esta seção mostra como criar um projeto no Visual Studio e configurá-lo para o desenvolvimento de Serviços de Mídia.  Nesse caso, o projeto é um aplicativo de console C# do Windows, mas as mesmas etapas de configuração mostradas aqui se aplicam a outros tipos de projetos que podem ser criados para aplicativos de Serviços de Mídia (por exemplo, um aplicativo do Windows Forms ou um aplicativo Web ASP .NET).

Esta seção mostra como usar **NuGet** para adicionar o SDK do .NET dos Serviços de Mídia e outras bibliotecas dependentes.

Como alternativa, você pode obter os bits mais recentes do SDK do .NET dos Serviços de Mídia no GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) ou [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), criar a solução e adicionar as referências ao projeto do cliente. Todas as dependências necessárias são baixadas e extraídas automaticamente.

1. No Visual Studio, crie um novo aplicativo de console C#. Digite o **Nome**, o **Local** e o **Nome da solução** e clique em OK.
2. Compilar a solução.
3. Use o **NuGet** para instalar e adicionar **extensões do SDK do .NET dos Serviços de Mídia do Azure**. Instalar esse pacote também instala o **SDK do .NET dos Serviços de Mídia** e adiciona todas as outras dependências necessárias.
   
    Certifique-se de que você tenha a versão mais recente do NuGet instalada. Para obter mais informações e instruções de instalação, consulte [NuGet](http://nuget.codeplex.com/).
4. No Gerenciador de Soluções, clique com o botão direito no nome do projeto e escolha Gerenciar pacotes NuGet.
   
    A caixa de diálogo Gerenciar Pacotes NuGet será exibida.
5. Na Galeria Online, procure por extensões de serviços de mídia do Azure, escolha extensões do SDK do .NET dos Serviços de Mídia e, em seguida, clique no botão Instalar.
   
    O projeto é modificado e faz referência às extensões do SDK do .NET dos Serviços de Mídia, ao SDK do .NET dos Serviços de Mídia e a outros assemblies dependentes adicionados.
6. Para promover um ambiente de desenvolvimento mais limpo, considere a ativação da restauração de pacote do NuGet. Para obter mais informações, consulte [Restauração do pacote NuGet"](http://docs.nuget.org/consume/package-restore).
7. Adicionar uma referência ao assembly **System.Configuration** . Este assembly contém a classe System.Configuration.**ConfigurationManager** que é utilizada para acessar arquivos de configuração (por exemplo, App.config).
   
    Para adicionar referências usando a caixa de diálogo Gerenciar referências, clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções. Em seguida, selecione Adicionar e Referências.
   
    Aparece a caixa de diálogo Gerenciar referências.
8. Em assemblies do .NET Framework, localize e selecione o assembly System.Configuration e pressione OK.
9. Abra o arquivo App.config (adicione o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo.     
   Defina os valores para o nome e chave de conta de seus Serviços de Mídia do Azure, conforme mostrado no exemplo a seguir.
   
    Para localizar os valores de Nome e Chave, vá para o Portal do Azure e selecione sua conta. A janela Configurações aparece à direita. Na janela Configurações, selecione Chaves. Ao clicar no ícone ao lado de cada caixa de texto o valor é copiado para a área de transferência do sistema.

        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

1. Substitua as instruções **using** existentes no início do arquivo Program.cs pelo código a seguir.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia.    

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


