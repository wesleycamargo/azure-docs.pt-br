<properties urlDisplayName="Media Services" pageTitle="Como usar os Serviços de Mídia (Java) - Guia de recursos do Azure" metaKeywords="Azure Media Services, Azure media, Azure streaming, azure media, azure streaming, azure encoding" description="Describes how to use Azure Media Services to perform common tasks including encoding, encrypting, and streaming resources." metaCanonical="" services="media-services" documentationCenter="Java" title="How to Use Media Services" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="robmcm" />

#Como usar os Serviços de Mídia

Este guia mostra como começar a programar com os Serviços de Mídia do Azure usando Java. O guia inclui uma visão geral técnica dos Serviços de Mídia, etapas para configurar sua conta dos Serviços de Mídia do Azure e o código que mostra como realizar tarefas comuns de programação. 

## Sumário

-   [O que são Serviços de Mídia?](#media_services)
-   [Configurando uma conta do Azure para Serviços de Mídia](#setup-account)
-   [Configurando para desenvolvimento dos Serviços de Mídia](#setup-dev)
-   [Como: Usar os Serviços de Mídia com o Java](#connect)
-   [Recursos adicionais](#additional-resources)

 
##<a id="media_services"></a>O que são Serviços de Mídia

Os Serviços de Mídia do Azure formam uma plataforma de mídia extensível que integra o melhor da Plataforma de Mídia da Microsoft e os componentes de mídia de terceiros no Azure. Os Serviços de Mídia fornecem um pipeline de mídia na nuvem que permite que os parceiros do setor estendam ou substituam as tecnologias componentes. Os ISVs e os provedores de mídia podem usar os Serviços de Mídia para criar soluções de mídia completas. Esta visão geral descreve a arquitetura geral e os cenários de desenvolvimento comuns dos Serviços de Mídia.

O diagrama a seguir ilustra a arquitetura básica dos Serviços de Mídia.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###Suporte ao recurso dos Serviços de Mídia
A versão atual dos Serviços de Mídia fornece o seguinte conjunto de recursos para o desenvolvimento de aplicativos de mídia na nuvem. 

- **Ingestão**. As operações de ingestão trazem ativos para o sistema, por exemplo, carregando e criptografando-os antes de serem colocados no Armazenamento do Azure.No lançamento do RTM, os Serviços de Mídia oferecerão integração com componentes de parceiros para fornecer soluções de carregamento UDP (protocolo UDP) rápido.
- **Codificação**. As operações de codificação incluem codificação, transformação e conversão de ativos de mídia. Você pode executar tarefas de codificação na nuvem usando o Codificador de Mídia que é incluído nos Serviços de Mídia.As opções de codificação incluem:
   - Usar o Codificador de Mídia do Azure e trabalhar com um intervalo de codecs e formatos padrão, incluindo o líder do setor, o Smooth Streaming do IIS, MP4 e a conversão para HTTP Live Streaming da Apple.
   - Converter bibliotecas inteiras ou arquivos individuais com controle total sobre entrada e saída.
   - Um grande conjunto de tipos, formatos e codecs de arquivos com suporte (consulte [Tipos de arquivos com suporte para os Serviços de Mídia][]).
   - Conversões de formato com suporte. Os Serviços de Mídia permitem que você converta ISO MP4 (.mp4) em formato de arquivo Smooth Streaming (PIFF 1.3) (.ismv; .isma). Você também pode converter o formato de arquivo Smooth Streaming (PIFF) no HTTP Live Streaming da Apple (.msu8, .ts).
- **Proteção**. Proteger o conteúdo significa criptografar streaming ao vivo ou conteúdo sob demanda para proteger o transporte, o armazenamento e a entrega.  Os Serviços de Mídia fornecem uma solução independente de tecnologia DRM para proteger conteúdo. As tecnologias DRM com suporte no momento são a Proteção do Microsoft PlayReady e a Criptografia Comum MPEG. Suporte para tecnologias DRM adicionais estarão disponíveis. 
- **Stream**. Conteúdo de streaming envolve o envio ao vivo ou sob demanda a clientes, ou a recuperação ou o download de arquivos de mídia específicos da nuvem.  Os Serviços de Mídia fornecem uma solução independente de formato DRM para conteúdo de streaming. Os Serviços de Mídia dão suporte à origem de streaming para Smooth Streaming, Live Streaming HTTP da Apple e formatos MP4. Suporte para formatos adicionais estarão disponíveis.   Você pode fornecer conteúdo de streaming transparentemente usando o CDN do Azure ou um CDN de terceiros, o que habilita a opção de escalar para milhões de usuários.   

###Cenários de desenvolvimento dos Serviços de Mídia
Os Serviços de Mídia dão suporte a vários cenários comuns de desenvolvimento de mídia conforme descrito na tabela a seguir. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Cenário</th>
       <th>Descrição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Criando fluxos de trabalho completos</td>
        <td>Compile fluxos de trabalho de mídia abrangentes inteiramente na nuvem. Do carregamento de mídia à distribuição do conteúdo, os Serviços de Mídia oferecem um intervalo de componentes que podem ser combinados para tratar fluxos de trabalho de aplicativos específicos. Os recursos atuais incluem carregamento, armazenamento, codificação, conversão de formato, proteção de conteúdo e entrega de streaming sob demanda.</td>
    </tr>
    <tr>
        <td>Criando fluxos de trabalho híbridos</td>
        <td>Você pode integrar os Serviços de Mídia com as ferramentas e os processos existentes. Por exemplo, codificar conteúdo no local e, em seguida, carregá-lo para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio do CDN do Azure ou de terceiros. Os Serviços de Mídia podem ser chamados individualmente por meio de APIs REST padrão para integração com aplicativos e serviços externos.</td>
    </tr>
    <tr>
        <td>Fornecendo suporte de nuvem para players de mídia</td>
        <td>Você pode criar, gerenciar e entregar mídia em vários dispositivos (incluindo dispositivos iOS, Android e Windows) e plataformas.</td>
    </tr>
  </tbody>
</table>

###Desenvolvimento de cliente dos Serviços de Mídia
Amplie o alcance da sua solução de Serviços de Mídia usando SDKs e estruturas de player para criar aplicativos de clientes de mídia. Esses clientes são para desenvolvedores que desejam criar aplicativos de Serviços de Mídia que oferecem experiências de usuário atraentes em vários dispositivos e plataformas. Dependendo dos dispositivos para os quais você deseja criar aplicativos de cliente, há opções para SDKs e estruturas de player disponíveis na Microsoft e em outros parceiros.  

Uma lista dos SDKs de clientes disponíveis e das estruturas do player é fornecida a seguir.  Para obter mais informações sobre esses e outros SDKs e estruturas de player planejados e sobre a funcionalidade para a qual eles podem dar suporte, consulte [Desenvolvimento de clientes dos Serviços de Mídia]. 

####Suporte a clientes PC e Mac  
Para PCs e Macs, você pode direcionar uma experiência de streaming usando o Microsoft Silverlight ou Adobe Open Source Media Framework.

-	[Cliente Smooth Streaming para Silverlight](http://www.iis.net/download/smoothclient)
-	[Plataforma de Mídia da Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)
-	[Plug-in do Smooth Streaming para OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Para obter informações sobre como usar esse plug-in, consulte [Como usar o plug-in do Smooth Streaming para Adobe Open Source Media Framework.](http://go.microsoft.com/fwlink/?LinkId=275034).

####Aplicativos do Windows 8
Para o Windows 8, você pode criar aplicativos da Windows Store usando qualquer uma das linguagens e construções de desenvolvimento com suporte, como HTML, Javascript, XAML, C# e C+.

-	[SDK do Cliente Smooth Streaming para Windows 8].(http://go.microsoft.com/fwlink/?LinkID=246146). Para obter mais informações sobre como criar um aplicativo da Windows Store usando esse SDK, consulte [Como criar um aplicativo de Smooth Streaming da Windows Store](http://go.microsoft.com/fwlink/?LinkId=271647). Para obter informações sobre como criar um player de Smooth Streaming em HTML5, consulte [ Passo a passo: Criando seu primeiro player do Smooth Streaming em HTML5 ](http://msdn.microsoft.com/pt-br/library/jj573656.aspx).

-	[Plataforma de Mídia da Microsoft: Player Framework para aplicativos Windows 8 da Windows Store](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
O Xbox dá suporte a aplicativos Xbox LIVE que podem consumir conteúdo de Smooth Streaming. O ADK (Kit de Desenvolvimento de Aplicativo) do Xbox LIVE inclui:

-	Cliente Smooth Streaming para o ADK do Xbox LIVE
-	Plataforma de Mídia da Microsoft: Player Framework para o ADK do Xbox LIVE

####Dispositivos inseridos ou dedicados
Dispositivos, como TVs conectadas, decodificadores de sinais, Blu-Ray players, caixas de TV OTT e dispositivos móveis que têm uma estrutura de desenvolvimento de aplicativos personalizada e um pipeline de mídia personalizado. A Microsoft fornece os seguintes kits de portabilidade que podem ser licenciados e permite que os parceiros portem a reprodução de Smooth Streaming para a plataforma.

-	[Kit de portabilidade do cliente Smooth Streaming](http://www.microsoft.com/pt-br/mediaplatform/sspk.aspx)
-	[Kit de portabilidade de dispositivo Microsoft PlayReady](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
A Microsoft fornece um SDK que pode ser usado para criar aplicativos de vídeo premium para Windows Phone. 

-	[Cliente Smooth Streaming para Silverlight](http://www.iis.net/download/smoothclient)
-	[Plataforma de Mídia da Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)

####Dispositivos iOS
Para dispositivos iOS, inclusive iPhone, iPod e iPad, a Microsoft fornece um SDK que pode ser usado para compilar aplicativos para essas plataformas, a fim de entregar conteúdo de vídeo premium: SDK do Smooth Streaming para dispositivos iOS com PlayReady.  O SDK está disponível somente para licenciados, portanto, para obter mais informações,[envie email para a Microsoft](mailto:askdrm@microsoft.com). Para obter informações sobre o desenvolvimento do iOS, consulte a [Central de Desenvolvedores do iOS](https://developer.apple.com/devcenter/ios/index.action).

####Dispositivos Android
Vários parceiros da Microsoft fornecem SDKs para a plataforma Android que adicionam a capacidade de reproduzir um Smooth Streaming em um dispositivo Android. Envie [email para a Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices) para obter mais detalhes sobre os parceiros.


##<a id="setup-account"></a>Configurando uma conta do Azure para Serviços de Mídia

Para configurar sua conta de Serviços de Mídia, use o Portal de Gerenciamento.  Consulte o tópico [Como criar uma conta de Serviços de Mídia][].Depois de criar sua conta no Portal de Gerenciamento, você estará pronto para configurar seu computador para desenvolvimento de Serviços de Mídia. 

##<a id="setup-dev"></a>Configurando para desenvolvimento dos Serviços de Mídia

Esta seção contém os pré-requisitos gerais para desenvolvimento de Serviços de Mídia usando o SDK dos Serviços de Mídia para Java.

###Pré-requisitos

-   Uma conta dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Consulte o tópico [Como criar uma conta de Serviços de Mídia][].
-   As bibliotecas do Azure para Java, que podem ser instaladas na [Central de desenvolvedores de Java no Azure][].

##<a if="connect"></a>Como: Usar os Serviços de Mídia com o Java

O código a seguir mostra como criar um ativo, carregar um arquivo de mídia para o ativo, executar um trabalho com uma tarefa para transformar o ativo e baixar os arquivos de saída do ativo transformado.

Você precisará configurar uma conta de Serviços de Mídia para usar esse código. Para obter informações sobre como configurar uma conta, consulte [Como criar uma conta de Serviços de Mídia](http://www.windowsazure.com/pt-br/manage/services/media-services/how-to-create-a-media-services-account/).

Substitua seus valores pelas variáveis `clientId` e`clientSecret`. O código também depende de um arquivo armazenado localmente, `c:/media/MPEG4-H264.mp4`. Você precisará fornecer seu próprio arquivo a ser usado. O código também requer uma pasta de saída,`c:/output`, onde os arquivos de saída serão baixados.

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/pt-br/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

Os ativos que você cria são armazenados no Armazenamento do Azure. No entanto, use somente as APIs dos Serviços de Mídia do Azure (não as APIs de Armazenamento do Azure) para adicionar, atualizar ou excluir ativos.

###Determinando quais processadores de mídia estão disponíveis

O código acima usou um processador de mídia acessando-o por meio de um nome de processador de mídia específico (e se houvesse mais de uma versão, ele usaria a versão mais recente). Para determinar quais processadores de mídia estão disponíveis, você pode usar o código a seguir.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

Como alternativa, o código a seguir mostra como recuperar a ID de um processador de mídia pelo nome.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

###Cancelando um trabalho
Se for necessário cancelar um trabalho que não concluiu o processamento, o código a seguir mostra como cancelar um trabalho pela ID do trabalho.

    mediaService.action(Job.cancel(jobId));

##< id="additional-resources"></a>Recursos adicionais

Para obter a documentação do Javadoc dos Serviços de Mídia, consulte a[Documentação das bibliotecas do Azure para Java][].

<!-- URLs. -->

  [Como criar uma conta de Serviços de Mídia]: http://go.microsoft.com/fwlink/?linkid=256662
  [Central de desenvolvedores de Java no Azure]: http://www.windowsazure.com/pt-br/develop/java/
  [Documentação de bibliotecas do Azure para Java]: http://dl.windowsazure.com/javadoc/
  [Desenvolvimento de cliente dos Serviços de Mídia]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223283.aspx


<!--HONumber=35_1-->
