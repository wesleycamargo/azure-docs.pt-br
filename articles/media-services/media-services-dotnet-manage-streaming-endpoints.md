---

title: Gerenciar pontos de extremidade de streaming com o SDK .NET. | Microsoft Docs
description: "Este tópico mostra como gerenciar pontos de extremidade de streaming usando o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>Gerenciar pontos de extremidade de streaming com o SDK .NET

>[!NOTE]
>Certifique-se de examinar o tópico [Visão geral](media-services-streaming-endpoints-overview.md). Além disso, revise [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

O código deste tópico mostra como realizar as seguintes tarefas usando o SDK do .NET dos Serviços de Mídia do Azure:

- Examine o ponto de extremidade de streaming padrão.
- Crie ou adicione um novo ponto de extremidade de streaming.

    Você talvez queira ter vários pontos de extremidade de streaming se planeja ter diferentes CDNs ou um CDN e acesso direto.

    > [!NOTE]
    > Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.
    
- Atualize o ponto de extremidade de streaming.
    
    Certifique-se de chamar a função Update().

- Exclua o ponto de extremidade de streaming.

    >[!NOTE]
    >O ponto de extremidade de streaming não pode ser excluído.

Para obter informações sobre como dimensionar o ponto de extremidade de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) tópico.


###<a name="set-up-the-visual-studio-project"></a>Configurar o projeto do Visual Studio

1. No Visual Studio 2015, crie um novo aplicativo de console C#.  
2. Compilar a solução.
3. Use o **NuGet** para instalar o [pacote do SDK do .NET dos Serviços de Mídia do Azure mais recentes](https://www.nuget.org/packages/windowsazure.mediaservices/).   
4. Adicione a seção appSettings ao arquivo .config e atualize os valores de chave e o nome dos Serviços de Mídia. 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>Adicionar o código que gerencia os pontos de extremidade de streaming
    
Substitua o código de Program.cs pelo código a seguir:

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


