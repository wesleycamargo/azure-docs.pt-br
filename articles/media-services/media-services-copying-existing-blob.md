---
title: "Copiar blobs de uma conta de armazenamento para um ativo dos Serviços de Mídia do Azure | Microsoft Docs"
description: "Este tópico mostra como copiar um blob existente para um Ativo dos Serviços de Mídia. O exemplo usa Extensões do SDK do .NET dos Serviços de Mídia do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Copiar blobs existentes para um ativo dos Serviços de Mídia
Este tópico mostra como copiar os blobs de uma conta de armazenamento para um novo ativo dos Serviços de Mídia do Microsoft Azure usando as [Extensões do SDK do .NET dos Serviços de Mídia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Esse método de extensão funciona com:

- Ativos regulares.
- Ativos de arquivamento dinâmico (formato FragBlob).
- Ativos de origem e de destino que pertencem a contas de Serviços de Mídia diferentes (mesmo entre data centers diferentes). No entanto, essa ação pode incorrer em encargos. Para obter mais informações sobre preços, consulte o [Transferências de Dados](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelos serviços de mídia sem o uso de APIs de serviços de mídia.
> 

## <a name="download-sample"></a>Baixar exemplo
Você pode seguir as etapas deste artigo ou baixar um exemplo que contém o código descrito [neste](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/) artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Duas contas dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Confira o tópico [Criar uma conta dos Serviços de Mídia](media-services-portal-create-account.md).
* Sistemas operacionais: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Framework 4.5.
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou posterior.

## <a name="set-up-your-project"></a>Configurar o seu projeto
Nesta seção você irá criar e configurar um projeto de aplicativo de console em C#.

1. Use o Visual Studio para criar uma nova solução que inclua o projeto de Aplicativo de Console em C#. 
2. Digite CopyExistingBlobsIntoAsset para o nome e, em seguida, clique em OK.
3. Use o [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar e adicionar as Extensões do SDK do .NET dos Serviços de Mídia do Azure (windowsazure.mediaservices.extensions). Instalar esse pacote também instala os SDK do .NET dos Serviços de Mídia e adiciona todas as outras dependências necessárias.
4. Adicione outras referências que são necessárias para este projeto: System.Configuration.
6. Adicione a seção appSettings ao arquivo .config e atualize os valores com base em suas contas de Serviços de Mídia, na conta de armazenamento de destino e na ID do ativo de origem. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Copiar blobs de uma conta de armazenamento para um ativo dos Serviços de Mídia

O código a seguir usa o método da extensão **IAsset.Copy** para copiar todos os arquivos do ativo de origem para o ativo de destino usando uma única extensão. Há uma sobrecarga adicional com suporte assíncrono.

Substitua o código da classe Program.cs pelo código a seguir:

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


