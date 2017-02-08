---
title: "Proteger o conteúdo do HLS com o Apple FairPlay e/ou Microsoft PlayReady | Microsoft Docs"
description: "Este tópico fornece uma visão geral e mostra como usar os Serviços de Mídia do Azure para criptografar de forma dinâmica o seu conteúdo de HLS (HTTP Live Streaming) com o Apple FairPlay. Ele também mostra como usar o serviço de distribuição de licença dos Serviços de Mídia para entregar licenças do FairPlay aos clientes."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 09c4c9d43d6e29872ed6484b27334c8e9aa428b5


---
# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Proteger o conteúdo do HLS com o Apple FairPlay e/ou Microsoft PlayReady
Os Serviços de Mídia do Azure permitem que você criptografe seu conteúdo de HLS (HTTP Live Streaming) de maneira dinâmica, usando os seguintes formatos:  

* **Chave de limpeza do envelope AES-128**

    A parte inteira é criptografada usando o modo **AES-128 CBC** . A descriptografia da transmissão tem suporte pelo iOS e pelo player OSX de forma nativa. Para obter mais informações, consulte [este artigo](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Os exemplos de áudio e vídeo individuais são criptografadas usando o modo **AES-128 CBC** . **FPS** (FairPlay Streaming) é integrado aos sistemas operacionais de dispositivos, com suporte nativo no iOS e na Apple TV. O Safari no OS X habilita o FPS usando o suporte à interface de EME (Extensões de Mídia Criptografada).
* **Microsoft PlayReady**

A imagem a seguir mostra o fluxo de trabalho da **criptografia dinâmica do HLS + FairPlay e/ou PlayReady** .

![Proteja com o FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tópico demonstra como usar os Serviços de Mídia do Azure para criptografar de forma dinâmica o conteúdo de HLS com o FairPlay da Apple. Ele também mostra como usar o serviço de distribuição de licença dos Serviços de Mídia para entregar licenças do FairPlay aos clientes.

> [!NOTE]
> Se quiser criptografar o conteúdo do HLS com o PlayReady, você precisará criar uma chave de conteúdo comum e associá-la ao seu ativo. Você também precisa configurar a política de autorização da chave de conteúdo, como descrito no tópico [sando a criptografia comum e dinâmica do PlayReady](media-services-protect-with-drm.md) .
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

* O seguinte é necessário ao usar o AMS para fornecer o HLS criptografado com o FairPlay e para entregar as licenças do FairPlay.

  * Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, confira [Criar Conta](media-services-portal-create-account.md).
  * Inscreva-se no [Programa de Desenvolvimento da Apple](https://developer.apple.com/).
  * A Apple exige que o proprietário do conteúdo obtenha o [pacote de implantação](https://developer.apple.com/contact/fps/). Informe a solicitação na qual você já implementou o KSM (Módulo de Segurança de Chave) com os Serviços de Mídia do Azure e que você está solicitando o pacote FPS final. Haverá instruções no pacote FPS final para gerar a certificação e obter o ASK, que você usará para configurar o FairPlay.
  * SDK do .NET dos Serviços de Mídia do Azure na versão **3.6.0** ou posterior.
* Os seguintes itens devem ser definidos no lado de distribuição de chaves do AMS:

  * **Certificado de Aplicativo (AC)** – o arquivo.pfx que contém a chave privada. Esse arquivo é criado pelo cliente e criptografado com uma senha pelo mesmo cliente.

       Ao configurar a política de distribuição de chaves, o cliente deverá fornecer essa senha e o .pfx no formato base64.

      As etapas a seguir descrevem como gerar um certificado pfx para FairPlay.

    1. Instale o OpenSSL de https://slproweb.com/products/Win32OpenSSL.html

        Vá para a pasta onde se encontram o certificado FairPlay e outros arquivos enviados pela Apple.
    2. Linha de comando para converter o cer em pem:

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
    3. Linha de comando para converter o pem em pfx com a chave privada (a senha para o arquivo pfx será solicitada pelo OpenSSL).

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
  * **Senha do Certificado de Aplicativo** – a senha do cliente para criar o arquivo.pfx.
  * **ID da senha do Certificado de Aplicativo** – o cliente deverá carregar a senha de maneira semelhante a como carrega outras chaves do AMS e usando o valor de enumeração **ContentKeyType.FairPlayPfxPassword**. Como resultado, o cliente obterá a ID do AMS. Isso é o que ele precisa para usar na opção de política de distribuição de chaves.
  * **iv** – um valor aleatório de 16 bytes que deve coincidir com o iv na política de distribuição de ativos. O cliente gera o IV e o coloca em dois locais: na política de distribuição de ativos e na opção de política de distribuição de chaves.
  * **ASK** – a ASK (Chave Secreta do Aplicativo) é recebida quando você gera a certificação usando o portal do desenvolvedor da Apple. Cada equipe de desenvolvimento receberá uma ASK exclusiva. Salve uma cópia da ASK e armazene-a em um local seguro. Você precisará configurar a ASK como FairPlayAsk nos Serviços de Mídia do Azure posteriormente.
  * **ID da ASK** – é obtida quando o cliente carrega a ASK no AMS. O cliente deve carregar a ASK usando o valor de enumeração **ContentKeyType.FairPlayASk** . Como resultado, a ID do AMS retorna, e ela que deve ser usada ao configurar a opção de política de distribuição de chaves.
* Os seguintes itens devem ser definidos pelo lado do cliente FPS:

  * **Certificado de Aplicativo (AC)** – o arquivo .cer/.der contendo a chave pública que o sistema operacional usa para criptografar uma carga. O AMS precisa dessa informação porque ela é necessária para o player. O serviço de distribuição de chaves descriptografa-o usando a chave privada correspondente.
* Para reproduzir uma transmissão criptografada do FairPlay, você precisa obter a ASK real primeiro e, em seguida, gerar um certificado real. Este processo cria todas as três partes:

  * .der,
  * .pfx e
  * a senha para o .pfx.
* Clientes compatíveis com o HLS com criptografia **AES-128 CBC** : Safari no OS X, Apple TV, iOS.

## <a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Etapas para configurar a criptografia dinâmica do FairPlay e os serviços de distribuição de licenças
A seguir estão as etapas gerais que você precisará executar ao proteger seus ativos com o FairPlay, usando o serviço de distribuição de licenças dos Serviços de Mídia e também a criptografia dinâmica.

1. Criar um ativo e carregar arquivos no ativo.
2. Codificar o ativo contendo o arquivo para o conjunto de MP4 de taxa de bits adaptável.
3. Criar uma chave de conteúdo e associá-la ao ativo codificado.  
4. Configurar a política de autorização da chave de conteúdo. Ao criar a política de autorização de chave de conteúdo, você precisará especificar o seguinte:

   * o método de entrega (nesse caso, o FairPlay),
   * a configuração de opções de política do FairPlay. Para obter detalhes sobre como configurar o FairPlay, confira o método ConfigureFairPlayPolicyOptions() no exemplo abaixo.

     > [!NOTE]
     > Normalmente, convém configurar as opções de política do FairPlay apenas uma vez, visto que você terá apenas um conjunto de certificação e ASK.
     >
     >
   * restrições (abertas ou token),
   * e informações específicas sobre o tipo de distribuição de chave que define como a chave é entregue ao cliente.
5. Configurar política de entrega de ativos. A configuração da política de entrega inclui:

   * protocolo de entrega (HLS),
   * o tipo de criptografia dinâmica (criptografia CBC comum),
   * URL de aquisição de licença.

     > [!NOTE]
     > Se você quiser oferecer uma transmissão que seja criptografada com o FairPlay + outro DRM, você precisará configurar políticas de entrega separadas
     >
     > * Um IAssetDeliveryPolicy para configurar o DASH com CENC (PlayReady + WideVine) e o Smooth com o PlayReady.
     > * Outro IAssetDeliveryPolicy para configurar o FairPlay para o HLS
     >
     >
6. Criar um localizador OnDemand para obter uma URL de streaming.

## <a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Usando a entrega de chaves do FairPlay por aplicativos de player/cliente
Os clientes podem desenvolver aplicativos de player usando o SDK do iOS. Para conseguir reproduzir conteúdo do FairPlay, os clientes precisam implementar o protocolo de troca de licenças. O protocolo de troca de licença não é especificado pela Apple. Depende de cada aplicativo o modo de enviar solicitações de distribuição de chaves. Os serviços de entrega de chaves do FairPlay do AMS espera que o SPC seja recebido como um mensagem de postagem codificada como www-form-url da seguinte forma:

    spc=<Base64 encoded SPC>

> [!NOTE]
> O Azure Media Player não dá suporte para a reprodução do FairPlay pronto para uso. Os clientes precisam obter o player de exemplo da conta de desenvolvedor da Apple para obter a reprodução do FairPlay no MAC OSX.
>
>

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, você deve usar uma marcação de criptografia na URL de streaming: (formato='m3u8-aapl' criptografia='xxx').

As seguintes considerações se aplicam:

* Pode ser especificado apenas zero ou um tipo de criptografia.
* O tipo de criptografia não precisa ser especificado na url se apenas uma criptografia foi aplicada no ativo.
* O tipo de criptografia diferencia as letras maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:  
  * **cenc**: criptografia comum (Playready ou Widevine)
  * **cbcs-aapl**: Fairplay
  * **cbc**: criptografia de envelope AES.

## <a name="net-example"></a>Exemplo de .NET
O exemplo a seguir demonstra a funcionalidade que foi introduzida no SDK dos Serviços de Mídia do Azure para o .Net -Versão 3.6.0 (a capacidade de usar os Serviços de Mídia do Azure para distribuir o conteúdo criptografado com o FairPlay). O seguinte comando do pacote do Nuget foi usado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Crie um projeto de Console.
2. Use o NuGet para instalar e adicionar o SDK do .NET dos Serviços de Mídia do Azure.
3. Adicione outras referências: System.Configuration.
4. Adicione o arquivo de configuração que contém o nome da conta e as informações de chave:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup>
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>

                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>

                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>
7. Substitua o código no seu arquivo Program.cs pelo código mostrado nesta seção.

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;

        namespace DynamicEncryptionWithFairPlay
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];

                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);

                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;

                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);

                    bool tokenRestriction = false;
                    string tokenTemplateString = null;

                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);

                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
                    Console.WriteLine();

                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);

                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();

                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();

                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }

                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

                    Console.ReadLine();
                }

                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }

                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);

                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    locator.Delete();
                    policy.Delete();

                    return inputAsset;
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";

                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));

                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),     AssetCreationOptions.StorageEncrypted);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);

                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);

                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);

                    return key;
                }


                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions
                    // and create authorization policy          

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Open",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                                    Requirements = null
                                }
                            };


                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);


                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }

                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Token Authorization Policy",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                    Requirements = tokenTemplateString,
                                }
                            };

                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;

                    return tokenTemplateString;
                }

                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);

                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);

                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();

                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);

                    return FairPlayConfiguration;
                }

                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }

                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account,
                    // the player only recognizes a Key URL that starts with skd://.
                    // However, if you are using a customized player,
                    // you can choose whatever protocol you want.
                    // For example, "https".

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };

                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
                        assetDeliveryPolicyConfiguration);

                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);

                }


                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {

                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset.

                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();

                    // Create a 30-day readonly access policy.
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);

                    // Create a locator to the streaming content on an origin.
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));

                    // Create a URL to the manifest file.
                    return originLocator.Path + assetFile.Name;
                }

                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }

                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];

                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }

                    return returnValue;
                }
            }
        }


## <a name="next-steps-media-services-learning-paths"></a>Próximas etapas: roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Jan17_HO2-->


