---
title: "Proteger o conteúdo do HLS com o Apple FairPlay offline do Azure | Microsoft Docs"
description: "Este tópico fornece uma visão geral e mostra como usar os Serviços de Mídia do Azure para criptografar de forma dinâmica o seu conteúdo de HLS (HTTP Live Streaming) com o FairPlay da Apple no modo offline."
services: media-services
keywords: HBS, gerenciamento de direitos digitais, Streaming de FairPlay (FPS), Offline, iOS 10
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>Streaming de FairPlay Offline
Os serviços de mídia do Microsoft Azure fornecem um conjunto de [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) bem-projetados, abrangendo:
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Criptografia AES-128

A criptografia DRM/AES do conteúdo é executada dinamicamente após a solicitação de vários protocolos de streaming. Os serviços de distribuição de chaves de descriptografia de licença DRM/AES também são fornecidos pelos serviços de mídia do Azure.

Além de proteger o conteúdo de streaming online por meio de vários protocolos de streaming, o modo offline para conteúdo protegido também é um recurso que normalmente é solicitado. O suporte ao modo offline é necessário para os seguintes cenários:
1. Reprodução quando a conexão de Internet não estiver disponível, como durante viagens;
2. Alguns provedores de conteúdo podem não permitir a entrega de licença do DRM além das fronteiras do país. Se um usuário quiser ver o conteúdo durante uma viagem para o exterior, é necessário fazer o download offline.
3. Em alguns países, a disponibilidade de Internet e/ou a largura de banda ainda é limitada. Os usuários podem optar por fazer o download primeiro para que seja possível assistir o conteúdo em resolução alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, com mais frequência, o problema não é a disponibilidade da rede, mas a limitação da largura de banda. Provedores OTT/OVP solicitando suporte ao modo offline.

Este artigo aborda o suporte ao modo offline de Streaming de FairPlay (FPS), focalizando dispositivos que executam o iOS 10 ou posterior. Não há suporte para esse recurso para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de implementar o DRM offline para FairPlay em um dispositivo iOS 10 +, você deve primeiro:
1. Familiarizar-se com a proteção de conteúdo online para o FairPlay. Isso é abordado em detalhes nos artigos/exemplos a seguir:
- [Apple FairPlay Streaming para Serviços de Mídia do Azure normalmente disponível](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [Proteger o conteúdo do HLS com o Apple FairPlay ou Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [Uma amostra para o streaming de FPS online](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Obter o SDK de FPS da rede de Desenvolvedor da Apple. O SDK de FPS contém dois componentes:
- SDK de servidor de FPS, que contém KSM (módulo de segurança de chave), exemplos de cliente, uma especificação e um conjunto de vetores de teste;
- Pacote de implantação de FPS, que contém a função D, a especificação com instruções sobre como gerar o certificado FPS, chave privada específica do cliente e a chave de segredo do aplicativo (SOLICITAR). A Apple emite o pacote de implantação de FPS apenas para provedores de conteúdo licenciados.

## <a name="configuration-in-azure-media-services"></a>Configuração nos serviços de mídia do Azure
Para a configuração do modo offline de FPS via [SDK .NET dos Serviços de Mídia do Azure](https://www.nuget.org/packages/windowsazure.mediaservices), você precisa usar o SDK .NET dos Serviços de Mídia do Azure v. 4.0.0.4 ou posterior, que forneceu a API necessária para configurar o modo offline de FPS.
Como indicado nas suposições acima, assumimos que você tem o código de trabalho para a configuração de proteção de conteúdo de FPS de modo online. Uma vez que você tiver o código para configurar a proteção de conteúdo de modo online para FPS, você só precisa realizar as duas seguintes alterações.

## <a name="code-change-in-fairplay-configuration"></a>Alteração de código na configuração de FairPlay
Vamos definir uma lógica booleana “habilitar o modo offline”, chamada objDRMSettings.EnableOfflineMode que é verdadeira ao habilitar o cenário de DRm offline. Dependendo deste indicador, podemos fazer a seguinte alteração na configuração de FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Alteração de código na configuração de política de entrega de ativo
A segunda alteração é adicionar a terceira chave no dicionário Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
A terceira AssetDeliveryPolicyConfigurationKey precisa ser adicionada como abaixo: 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Após essta etapa, a política de entrega de ativos Dictionary<AssetDeliveryPolicyConfigurationKey, string> in FPS conterá as três seguintes entradas:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl ou AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl dependendo de fatores tais como FPS KSM/principal servidor usado e se queremos reutilizar a mesma Política de Entrega de Ativo em diversos ativos
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Agora, a sua conta de serviços de mídia está configurada para licenças de FairPlay entregues offline.

## <a name="sample-ios-player"></a>Exemplo de Player iOS
Primeiro, devemos observar que o suporte ao modo offline de FPS está disponível apenas no iOS 10 e posterior. Devemos obter o SDK do Servidor de FPS (v3.0 ou posterior) que contém o documento e exemplo para o modo offline de FPS. Especificamente, o SDK do servidor de FPS (v3.0 ou posterior) contém os dois seguintes itens relacionados ao modo offline:
1. Documento: Reprodução Offline com Streaming de FairPlay e HTTP Live Streaming. Apple, 14/9/2016. No SDK do servidor de FPS v4.0, este documento foi mesclado no documento de streaming de FPS principal.
2. Código de exemplo: exemplo HLSCatalog para o modo offline de FPS em \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. No aplicativo de exemplo HLSCatalog, os arquivos de código a seguir são particularmente para a implementação dos recursos de modo offline:
- Arquivo de código AssetPersistenceManager.swift: AssetPersistenceManager é a classe principal neste exemplo que demonstra
    - Como gerenciar streams de download de HLS, como APIs para iniciar e cancelar o download, excluir ativos existentes do dispositivo do usuário;
    - Como monitorar o andamento do download.
- Arquivos de código AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController é a principal interface deste exemplo. Ele fornece uma lista de ativos. O exemplo pode reproduzir, fazer download, excluir ou cancelar um download. 

Encontre abaixo as etapas detalhadas para configurar um player iOS em execução. Vamos supor que você inicialize a partir do exemplo HLSCatalog no SDK do Servidor de FPS v 4.0.1.  Precisamos fazer as seguintes alterações de código:

Em HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` utilizando o seguinte código: Deixe que drmUr seja uma variável atribuída ao URL de streaming de HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Em HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestApplicationCertificate()`. Essa implementação depende se você inseriu o certificado (apenas a chave pública) com o dispositivo ou hospedou o certificado na web. Encontre abaixo uma implementação usando o certificado do aplicativo hospedado usado em nossos exemplos de teste. Permitir que certUrl seja uma variável que contenha a URL do certificado do aplicativo.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Para o teste integrado final, tanto o URL do vídeo quanto o URL do certificado do aplicativo serão fornecidos na seção de "Teste integrado".

Em HLSCatalog\Shared\Resources\Streams.plist, adicione o seu URL de vídeo de teste e, para a ID de chave de conteúdo, podemos simplesmente usar a URL de aquisição de licença de FairPlay com o protocolo skd como o valor único.

![Streams do App de iOS FairPlay Offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Para a URL de vídeo de teste, a URL de aquisição de licenças de FairPlay e URL de certificado do aplicativo, você pode usar as suas próprias se já as tiver configuradas, ou você puder continuar para a próxima seção que contém exemplos de teste.

## <a name="integrated-test"></a>Teste integrado
Três exemplos de teste foram configurados nos serviços de mídia do Azure abrangendo os três cenários a seguir:
1.  FPS protegido com faixa de vídeo, áudio e áudio alternativo;
2.  FPS protegido com faixa de vídeo, áudio, mas sem áudio alternativo;
3.  FPS protegido com vídeo somente, sem áudio.

Esses exemplos podem ser encontrados neste [site de demonstração](http://aka.ms/poc#22), com o certificado correspondente do aplicativo hospedado em um aplicativo web do Azure.
Observamos que com o exemplo v3 ou v4 de SDK do servidor de FPS, se uma lista de reprodução mestre contiver áudio alternativo, durante o modo offline, ela toca somente o áudio. Portanto, precisamos da faixa de áudio alternativo. Em outras palavras, entre os três exemplos acima, (2) e (3) funcionam no modo online e offline. Mas (1) executará o áudio somente durante o modo offline, enquanto streaming online funcionará sem problemas.

## <a name="faq"></a>Perguntas frequentes
Algumas perguntas frequentes sobre resolução de problemas:
- **Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?** Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando a faixa de áudio alternativa está presente (que é o caso para HLS) durante o modo offline, tanto iOS 10 e iOS 11 apresentarão por padrão uma faixa de áudio alternativa. Para compensar esse comportamento para o modo offline de FPS, é preciso remover a faixa de áudio alternativa da transmissão. Para fazer isso por parte dos Serviços de Mídia do Azure, podemos simplesmente adicionar o filtro de manifestação dinâmica “audio-only=false”. Em outras palavras, uma URL de HLS terminaria com .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Por que ele ainda toca áudio somente sem vídeo durante o modo offline após eu ter adicionado audio-only=false?** Dependendo do projeto da chave de cache de CDN, o conteúdo pode ser armazenado em cache. Você precisa limpar o cache.
- **O modo de FPS offline também tem suporte em iOS 11 além do iOS 10?** Sim, o modo offline de FPS tem suporte para iOS 10 e iOS 11.
- **Por que eu não consigo encontrar o documento de Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS?** Desde o SDK do Servidor de FPS v 4, este documento foi mesclado no documento do guia de programação de streaming de FairPlay.
- **O que o último parâmetro significa na seguinte API para o modo de FPS offline?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

A documentação para essa API pode ser encontrada [aqui](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). O parâmetro representa a duração do aluguel offline com hora como a unidade.
- **O que é a estrutura de arquivos offline/baixados em dispositivos iOS?** A estrutura do arquivo baixado em um dispositivo iOS tem a aparência abaixo (captura de tela). `_keys` a pasta armazena licenças de FPS baixadas, uma armazena arquivo para cada host de serviço de licença. `.movpkg` a pasta armazena conteúdo de áudio e vídeo. A primeira pasta com o nome que termina com um traço seguido por um numero contém conteúdo de vídeo. O valor numérico é o "PeakBandwidth" das representações de vídeo. A segunda pasta com o nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta denominada "Dados" contém a lista de reprodução mestre do conteúdo de FPS. Boot.XML fornece uma descrição completa do conteúdo da pasta `.movpkg` (veja abaixo um exemplo de arquivo boot.xml).

![Estrutura do Arquivo de Aplicativo iOS de Exemplo do FairPlay Offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Um arquivo de exemplo boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>Resumo
Neste documento, nós fornecemos informações e etapas detalhadas para implementar o modo offline de FPS, incluindo:
1. Configuração de proteção de conteúdo de serviços de mídia do Azure através da API .NET AMS. Isso configura a criptografia dinâmica de FairPlay e a entrega de licenças de FairPlay em AMS.
2. Player de iOS com base no exemplo do SDK do servidor de FPS da Apple. Isso pode configurar um player iOS que pode reproduzir conteúdo de FPS em modo de streaming online ou offline.
3. Vídeos de FPS de exemplo para testar o streaming em modo offline e online.
4. Perguntas Frequentes sobre o modo offline de FPS.
