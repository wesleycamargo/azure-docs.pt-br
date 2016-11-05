---
title: Introdução ao fornecimento de conteúdo sob demanda usando o .NET | Microsoft Docs
description: Este tutorial orienta você pelas etapas de implementação de um aplicativo de fornecimento de conteúdo sob demanda com os Serviços de Mídia do Azure usando .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: juliako

---
# Introdução ao fornecimento de conteúdo sob demanda usando o SDK do .NET
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> 

## Visão geral
Este tutorial orienta você pelas etapas de implementação de um aplicativo de entrega de conteúdo de vídeo sob demanda (VoD) com os Serviços de Mídia do Azure (AMS) para .NET.

O tutorial apresenta o fluxo de trabalho básico dos Serviços de Mídia e os objetos e as tarefas de programação mais comuns necessárias para o desenvolvimento dos Serviços de Mídia do Microsoft Azure. No final do tutorial, você poderá transmitir ou baixar progressivamente um arquivo de mídia de exemplo que você carregou, codificou e baixou.

## O que você aprenderá
O tutorial mostra como concluir as seguintes tarefas:

1. Criar uma conta do Serviços de Mídia (usando o Portal Clássico do Azure).
2. Configurar o ponto de extremidade de streaming (usando o portal).
3. Criar e configurar um projeto do Visual Studio.
4. Conectar-se à conta dos Serviços de Mídia.
5. Criar um novo ativo e carregar um arquivo de vídeo.
6. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
7. Publicar o ativo e obter URLs para streaming e download progressivo.
8. Testar ao reproduzir o conteúdo.

## Pré-requisitos
Os itens a seguir são necessários para concluir o tutorial.

* Para concluir este tutorial, você precisa de uma conta do Azure.
  
    Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos do Azure gratuitos, como o recurso de aplicativos Web do Serviço de Aplicativo do Azure.
* Sistemas operacionais: Windows 8 ou posterior, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 ou posterior
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.

## Baixar exemplo
Obtenha e execute um exemplo [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## Criar uma conta de Serviços de Mídia usando o portal
1. No Portal Clássico do Azure, clique em **Novo**, em **Serviço de Mídia** e em **Criação Rápida**.
   
    ![Criação Rápida dos Serviços de Mídia](./media/media-services-dotnet-get-started/wams-QuickCreate.png)
2. Em **NOME**, insira o nome da nova conta. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3-24 caracteres de comprimento.
3. Em **REGIÃO**, selecione a região geográfica que será usada para armazenar os registros de metadados para sua conta de Serviços de Mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na lista suspensa.
4. Em **CONTA DE ARMAZENAMENTO**, selecione uma conta de armazenamento para fornecer armazenamento de blob do conteúdo de mídia de sua conta de Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região.
5. Se você criou uma nova conta de armazenamento, em **NOME DA NOVA CONTA DE ARMAZENAMENTO**, insira um nome para a conta de armazenamento. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.
6. Clique em **Criação Rápida** na parte inferior do formulário.

Você pode monitorar o status do processo na área de mensagem na parte inferior da janela.

Depois que a conta for criada com êxito, o status mudará para **Ativo**.

Na parte inferior da página, o botão **GERENCIAR CHAVES** é exibido. Quando você clica nesse botão, é exibida uma caixa de diálogo com o nome da conta de Serviços de Mídia e as chaves primárias e secundárias. Será necessário o nome da conta e as informações de chave primária para acessar a conta de Serviços de Mídia de modo programático.

![Página Serviços de Mídia](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

Quando você clica duas vezes no nome da conta, a página **Início Rápido** é exibida por padrão. Esta página permite que você execute algumas tarefas de gerenciamento que também estão disponíveis em outras páginas do portal. Por exemplo, você pode carregar um arquivo de vídeo por meio desta página ou da página CONTEÚDO.

## Configurar o ponto de extremidade de streaming usando o portal
Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Com streaming de taxa de bits adaptável, o cliente pode alternar para um fluxo de taxa de bits maior ou menor, já que o vídeo é exibido com base na largura de banda de rede atual, a utilização da CPU e outros fatores. Os Serviços de Mídia dão suporte às seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados Adobe PrimeTime/Access).

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codificar ou transcodificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde neste tutorial).
* Obter pelo menos uma unidade de streaming para o **ponto de extremidade de streaming** do qual você planeja fornecer seu conteúdo.

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente.

Para alterar o número de unidades reservadas para streaming, faça o seguinte:

1. No [portal](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de PONTOS DE EXTREMIDADE DE STREAMING. Então, clique no ponto de extremidade que deseja modificar.
3. Para especificar o número de unidades de streaming, clique na guia ESCALA e mova o controle deslizante **capacidade reservada**.
   
    ![Página Escala](./media/media-services-dotnet-get-started/media-services-origin-scale.png)
4. Pressione **SALVAR** para salvar suas alterações.

A alocação de quaisquer novas unidades de streaming leva cerca de 20 minutos para ser concluída.

> [!NOTE]
> No momento, mudar de qualquer valor positivo de unidades de streaming de volta para nenhuma unidade pode desabilitar o streaming por até uma hora.
> 
> O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para saber mais sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).
> 
> 

## Criar e configurar um projeto do Visual Studio
1. Crie um novo Aplicativo de Console C# no Visual Studio 2013, no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **nome**, o **Local** e o **Nome da solução** e **OK**.
2. Use o pacote Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **extensões do SDK .NET dos Serviços de Mídia do Azure**. As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código e tornarão mais fácil desenvolver com os Serviços de Mídia. Instalar esse pacote também instala o **SDK do .NET dos Serviços de Mídia** e adiciona todas as outras dependências necessárias.
3. Adicione uma referência ao assembly do System.Configuration. Esse assembly contém a classe **System.Configuration.ConfigurationManager** que é utilizada para acessar arquivos de configuração, como App.config.
4. Abra o arquivo App.config (adicione o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo. Defina os valores para o nome e chave de conta de seus Serviços de Mídia do Azure, conforme mostrado no exemplo a seguir. Para obter as informações de nome da conta e de chave, abra o Portal Clássico do Azure, selecione sua conta de serviços de mídia e clique no botão **GERENCIAR CHAVES**.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Substitua as instruções **using** existentes no início do arquivo Program.cs pelo código a seguir.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Crie uma nova pasta sob o diretório projects e copie um arquivo .mp4 ou .wmv que você deseja codificar e enviar por streaming, ou baixar progressivamente. Este exemplo usa o caminho "C:\\VideoFiles".

## Conectar-se à conta dos Serviços de Mídia
Ao usar os serviços de mídia com o .NET, você deve usar a classe **CloudMediaContext** para a maioria das tarefas de programação dos Serviços de Mídia: conectar-se à conta de Serviços de Mídia; criar, atualizar, acessar e excluir os seguintes objetos: ativos, arquivos de ativos, trabalhos, políticas de acesso, localizadores, etc.

Substitua a classe Program padrão pelo código a seguir. O código demonstra como ler os valores de conexão por meio do arquivo App.config e como criar o objeto **CloudMediaContext** para poder se conectar aos Serviços de Mídia. Para saber mais sobre como conectar-se aos Serviços de Mídia, veja [Conectando-se aos Serviços de Mídia com o SDK dos Serviços de Mídia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

A função **Main** chama métodos que serão definidos posteriormente nesta seção.

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
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

## Criar um novo ativo e carregar um arquivo de vídeo
No Serviços de Mídia, você carrega (ou insere) seus arquivos digitais em um ativo. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados, o conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão. Os arquivos no ativo são chamados **Arquivos de Ativo**.

O método **UploadFile** definido abaixo chama **CreateFromFile** (definido em extensões do SDK .NET). **CreateFromFile** cria um novo ativo no qual o arquivo de origem especificado é carregado.

O método **CreateFromFile** contém **AssetCreationOptions**, que permite especificar uma das seguintes opções de criação de ativos:

* **None** - nenhuma criptografia é usada. Esse é o valor padrão. Observe que, ao usar essa opção, seu conteúdo não será protegido quando estiver em trânsito ou em repouso no armazenamento. Se você pretende enviar um MP4 usando o download progressivo, use essa opção.
* **StorageEncrypted** – use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregá-lo para o armazenamento do Azure, onde ele é armazenado, criptografado em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.
* **CommonEncryptionProtected** — use esta opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).
* **EnvelopeEncryptionProtected** – use esta opção se você estiver carregando HLS criptografado com AES. Observe que os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.

O método **CreateFromFile** também permite especificar um retorno de chamada para relatar o progresso do upload do arquivo.

No exemplo a seguir, podemos especificar **Nenhum** para as opções de ativo.

Adicionar o método a seguir à classe do programa.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável
Após a inserção de Ativos nos Serviços de Mídia, a mídia poderá ser codificada, transmultiplexada, marcada com marca d'água e assim por diante antes que seja entregue aos clientes. Essas atividades são agendadas e executadas em contraste com várias instâncias de função de plano de fundo para garantir a disponibilidade e desempenho elevados. Essas atividades são chamadas de trabalhos, e cada Trabalho é composto por tarefas atômicas, que fazem o trabalho real no arquivo do ativo.

Como mencionado anteriormente, ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes. Os serviços de mídia podem empacotar dinamicamente um conjunto de arquivos MP4 com taxas de bit adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados do Adobe PrimeTime/Access).

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codificar ou transcodificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável.
* Obter pelo menos uma unidade de streaming para o ponto de extremidade de streaming do qual você planeja fornecer seu conteúdo.

O código a seguir mostra como enviar um trabalho de codificação. O trabalho contém uma tarefa que determina a transcodificação do arquivo de mezanino em um conjunto de MP4s de taxa de bits adaptável usando o **Codificador de Mídia Standard**. O código envia o trabalho e aguarda até que ele seja concluído.

Depois que o trabalho for concluído, você poderá transmitir seu ativo ou baixar progressivamente arquivos MP4 criados como resultado de transcodificação. Observe que você não precisa ter mais de 0 unidade de streaming para baixar progressivamente arquivos MP4.

Adicionar o método a seguir à classe do programa.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## Publicar o ativo e obter URLs para streaming e download progressivo
Para transmitir ou baixar um ativo, primeiro você precisa "publicá-lo" criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizador: OnDemandOrigin, usados para transmitir mídia por streaming (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e SAS (Assinatura de Acesso), usados para baixar arquivos de mídia.

Depois de criar os localizadores, você pode criar as URLs usadas para transmitir ou baixar os arquivos.

Uma URL de streaming para Smooth Streaming tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Uma URL de streaming para HLS tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Uma URL de streaming para MPEG DASH tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL SAS usada para baixar arquivos tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Extensões do SDK do .NET dos Serviços de Mídia fornecem métodos auxiliares práticos, que retornam URLs formatadas para o ativo publicado.

O código a seguir usa extensões do SDK .NET para criar os localizadores e obter streaming e URLs de download progressivo. O código também mostra como baixar os arquivos em uma pasta local.

Adicionar o método a seguir à classe do programa.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## Testar ao reproduzir o conteúdo
Depois que você executar o programa definido na seção anterior, as URLs semelhantes à seguinte serão exibidas na janela do console.

URLs de streaming adaptáveis:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de download progressivo (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para o fluxo de vídeo, use [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, Internet Explorer, Chrome ou Safari).

## Próximas etapas: roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### Procurando outra coisa?
Se este tópico não contiver o que você esperava, se estiver faltando alguma informação ou se não tiver atendido de alguma outra forma às suas necessidades, envie seus comentários usando o thread Disqus abaixo.

<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/

<!---HONumber=AcomDC_0824_2016-->