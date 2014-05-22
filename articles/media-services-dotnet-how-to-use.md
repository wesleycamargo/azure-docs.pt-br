<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como usar os Serviços de Mídia" authors="" solutions="" manager="" editor="" />


#Como usar os Serviços de Mídia

Este guia mostra como começar a programar com os Serviços de Mídia do Azure. O guia consiste em um conjunto de artigos e inclui uma visão geral técnica dos Serviços de Mídia, etapas para configurar sua conta do Azure para os Serviços de Mídia, um guia de instalação para desenvolvimento e tópicos que mostram como realizar tarefas comuns de programação. Os cenários demonstrados incluem: carregamento de ativos, criptografia de ativos, codificação de ativos e entrega de ativos. Os exemplos são escritos em C# e usam o SDK dos Serviços de Mídia para .NET. Para obter mais informações sobre os Serviços de Mídia do Azure, consulte o tópico [Próximas etapas][].

Você também pode programar os Serviços de Mídia usando as APIs REST baseadas no OData. Você pode criar um aplicativo que faz chamadas de API REST para os Serviços de Mídia em linguagens .NET ou em outras linguagens de programação. Para obter uma série completa de documentos sobre a programação com a API REST dos Serviços de Mídia, consulte [Criando aplicativos com a API REST dos Serviços de Mídia do Azure](http://go.microsoft.com/fwlink/?linkid=252967). 

Para iniciar a programação com a API REST dos Serviços de Mídia ou o SDK dos Serviços de Mídia, primeiro habilite sua conta do Azure para Serviços de Mídia conforme descrito na seção [Configurando uma conta do Azure para Serviços de Mídia][].

A documentação mais recente do SDK dos Serviços de Mídias está localizada [aqui](http://msdn.microsoft.com/pt-br/library/hh973613.aspx). 

##<a name="what-are"></a><span class="short header">O que são os Serviços de Mídia?</span>
Os Serviços de Mídia do Azure formam uma plataforma de mídia extensível que integra o melhor da Plataforma de Mídia da Microsoft e os componentes de mídia de terceiros no Azure. Os Serviços de Mídia fornecem um pipeline de mídia na nuvem que permite que os parceiros do setor estendam ou substituam as tecnologias componentes. Os ISVs e os provedores de mídia podem usar os Serviços de Mídia para criar soluções de mídia completas. Esta visão geral descreve a arquitetura geral e os cenários de desenvolvimento comuns dos Serviços de Mídia.

O diagrama a seguir ilustra a arquitetura básica dos Serviços de Mídia.

![Arquitetura dos Serviços de Mídia](./media/media-services-dotnet-how-to-use/wams-01.png)

###Suporte aos recursos dos Serviços de Mídia
A versão atual dos Serviços de Mídia fornece o seguinte conjunto de recursos para o desenvolvimento de aplicativos de mídia na nuvem.

- **Ingestão**. As operações de ingestão trazem ativos para o sistema, por exemplo, carregando e criptografando-os antes de serem colocados no Armazenamento do Azure. Os Serviços de Mídia oferecem integração com componentes de parceiros para fornecer soluções de carregamento UDP rápido.
- **Codificar**. As operações de codificação incluem codificação, transformação e conversão de ativos de mídia. Você pode executar tarefas de codificação na nuvem usando o Codificador de Mídia do Azure. As opções de codificação incluem:
   - Usar o codificador de Mídia do Azure e trabalhar com um intervalo de codecs e formatos padrão, incluindo o IIS Smooth Streaming, o líder da indústria, o MP4 e a conversão para o HTTP Live Streaming da Apple.
   - Converter bibliotecas inteiras ou arquivos individuais com controle total sobre entrada e saída.
   - Um grande conjunto de tipos, formatos e codecs de arquivos suportados (consulte [Tipos de arquivos suportados para Serviços de Mídia][]).
   - Conversões de formato suportadas. Os Serviços de Mídia permitem que você converta ISO MP4 (.mp4) em formato de arquivo Smooth Streaming (PIFF 1.3) (.ismv; .isma). Você também pode converter o formato de arquivo Smooth Streaming (PIFF) no HTTP Live Streaming da Apple (.msu8, .ts).
- **Proteger**. Proteger o conteúdo significa criptografar streaming ao vivo ou conteúdo sob demanda para proteger o transporte, o armazenamento e a entrega. Os Serviços de Mídia fornecem uma solução independente de tecnologia DRM para proteger conteúdo.  As tecnologias DRM com suporte no momento são o Microsoft PlayReady e a Criptografia Comum MPEG. Suporte para tecnologias DRM adicionais estarão disponíveis. 
- **Fluxo**. O conteúdo de streaming envolve o envio ao vivo ou sob demanda a clientes, ou o download de arquivos de mídia específicos da nuvem. Os Serviços de Mídia fornecem uma solução independente de formato DRM para conteúdo de streaming.  Os Serviços de Mídia oferecem suporte à origem de streaming para Smooth Streaming, Live Streaming HTTP da Apple e vários formatos MP4. Suporte para formatos adicionais estarão disponíveis. Você pode fornecer conteúdo de streaming transparentemente usando uma CDN de terceiros, o que habilita a opção de escalar para milhões de usuários.   

###Cenários de desenvolvimento dos Serviços de Mídia
Os Serviços de Mídia oferecem suporte a vários cenários comuns de desenvolvimento de mídia conforme descrito na tabela a seguir. 
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
        <td>Crie fluxos de trabalho de mídia abrangentes inteiramente na nuvem. Do carregamento de mídia à distribuição do conteúdo, os Serviços de Mídia oferecem um intervalo de componentes que podem ser combinados para tratar fluxos de trabalho de aplicativos específicos. Os recursos atuais incluem carregamento, armazenamento, codificação, conversão de formato, proteção de conteúdo e entrega de streaming sob demanda.</td>
    </tr>
    <tr>
        <td>Criando fluxos de trabalho híbridos</td>
        <td>Você pode integrar os Serviços de Mídia com as ferramentas e os processos existentes. Por exemplo, codificar conteúdo no local e, em seguida, carregá-lo para os Serviços de Mídia para transcodificá-lo em vários formatos e entregá-lo por meio do CDN do Azure ou de terceiros. Os Serviços de Mídia podem ser chamados individualmente por meio de APIs REST padrão para integração fácil com aplicativos e serviços externos.</td>
    </tr>
    <tr>
        <td>Fornecendo suporte de nuvem para players de mídia</td>
        <td>Você pode criar, gerenciar e entregar mídia em vários dispositivos (incluindo dispositivos iOS, Android e Windows) e plataformas.</td>
    </tr>
  </tbody>
</table>

<h3><a name="media-client"></a>Desenvolvimento de clientes dos Serviços de Mídia</h3>
Amplie o alcance da sua solução de Serviços de Mídia usando SDKs e estruturas de player para criar aplicativos de clientes de mídia. Esses clientes são para desenvolvedores que desejam criar aplicativos de Serviços de Mídia que oferecem experiências de usuário atraentes em vários dispositivos e plataformas. Dependendo dos dispositivos de destino, há opções para SDKs e estruturas de player disponíveis na Microsoft e em outros parceiros.  

Uma lista dos SDKs de clientes disponíveis e das estruturas do player é fornecida a seguir.  Para obter mais informações sobre esses e outros SDKs e estruturas de player planejados e sobre a funcionalidade para a qual podem oferecer suporte, consulte [Desenvolvimento de cliente de Serviços de Mídia][] nos fóruns dos Serviços de Mídia. 

####Suporte a clientes de PC e Mac  
Para PCs e Macs, você pode direcionar uma experiência de streaming usando o Microsoft Silverlight ou Adobe Open Source Media Framework.

-	[Cliente de Smooth Streaming para Silverlight.](http://www.microsoft.com/pt-br/download/details.aspx?id=29940)
-	[Plataforma de Mídia da Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)
-	[Plug-in do Smooth Streaming para OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Para obter informações sobre como usar esse plug-in, consulte [Como usar o plug-in do Smooth Streaming para Adobe Open Source Media Framework](http://go.microsoft.com/fwlink/?LinkId=275034).

####Aplicativos do Windows 8
Para o Windows 8, você pode criar aplicativos da Windows Store usando qualquer uma das linguagens e construções de desenvolvimento com suporte, como HTML, Javascript, XAML, C# e C+.

-	[SDK do Cliente do Smooth Streaming para Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Para obter mais informações sobre como criar um aplicativo da Windows Store usando esse SDK, consulte [Como criar um aplicativo de Smooth Streaming da Windows Store](http://go.microsoft.com/fwlink/?LinkId=271647). Para obter informações sobre como criar um player de Smooth Streaming em HTML5, consulte [Passo a passo: Criando seu primeiro player de Smooth Streaming em HTML5](http://msdn.microsoft.com/pt-br/library/jj573656(v=vs.90).aspx).

-	[Plataforma de Mídia da Microsoft: Player Framework para aplicativos do Windows 8 da Windows Store](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
O Xbox oferece suporte a aplicativos Xbox LIVE que podem consumir conteúdo de Smooth Streaming. O ADK (Kit de Desenvolvimento de Aplicativo) do Xbox LIVE inclui:

-	Cliente do Smooth Streaming para o ADK do Xbox LIVE
-	Plataforma de Mídia da Microsoft: Player Framework para o ADK do Xbox LIVE

####Dispositivos incorporados ou dedicados
Dispositivos, como TVs conectadas, decodificadores de sinais, Blu-Ray players, caixas de TV OTT e dispositivos móveis que têm uma estrutura de desenvolvimento de aplicativos personalizada e um pipeline de mídia personalizado. A Microsoft fornece os seguintes kits de portabilidade que podem ser licenciados e permite que os parceiros portem a reprodução de Smooth Streaming para a plataforma.

-	[Kit de portabilidade de cliente do Smooth Streaming](http://www.microsoft.com/pt-br/mediaplatform/sspk.aspx)
-	[Kit de portabilidade de dispositivo do Microsoft PlayReady](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
A Microsoft fornece um SDK que pode ser usado para criar aplicativos de vídeo premium para Windows Phone. 

-	[Cliente de Smooth Streaming para Silverlight.](http://www.microsoft.com/pt-br/download/details.aspx?id=29940)
-	[Plataforma de Mídia da Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)

####Dispositivos iOS
Para dispositivos iOS incluindo iPhone, iPod e iPad, a Microsoft fornece um SDK que pode ser usado para criar aplicativos para essas plataformas para entregar conteúdo de vídeo premium: SDK do Smooth Streaming para dispositivos com iOS com PlayReady.  O SDK está disponível somente para licenciados, portanto, para obter mais informações, [envie um email para a Microsoft](mailto:askdrm@microsoft.com). Para obter informações sobre o desenvolvimento do iOS, consulte a [Central de desenvolvedores do iOS (a página pode estar em inglês)](https://developer.apple.com/devcenter/ios/index.action).

####Dispositivos Android
Vários parceiros da Microsoft fornecem SDKs para a plataforma Android que adicionam a capacidade de reproduzir um Smooth Streaming em um dispositivo Android. [Envie um email para a Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices) para obter mais detalhes sobre os parceiros.

##Próximas etapas
Agora que você tem uma visão geral dos Serviços de Mídia, vá para o tópico [Configurando seu computador para os Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=301751).


  [O que são os Serviços de Mídia?]: #what-are
  [Desenvolvimento de cliente de Serviços de Mídia]: #media-client
  [Configurando uma conta do Azure para Serviços de Mídia]: #setup-account
  [Configurando para desenvolvimento de Serviços de Mídia]: #setup-dev
  [Como conectar-se aos Serviços de Mídia de forma programática]: #connect
  [Como criar um ativo criptografado e carregar no armazenamento]: #create-asset
  [Como obter uma instância do processador de mídia]: #get-mediaproc
  [Como verificar o andamento do trabalho]: #check-job-progress
  [Como codificar um ativo]: #encode-asset
  [Como proteger um ativo com a Proteção do PlayReady]: #playready
  [Como gerenciar ativos no armazenamento]: #manage-asset
  [Como fornecer um ativo por Download]: #download-asset
  [Como fornecer conteúdo de streaming]: #stream-asset
  [Como fornecer conteúdo de Streaming HLS da Apple]: #stream-HLS
  [Como habilitar a CDN do Azure]: #enable-cdn
  [Próximas etapas]: #next-steps

  [Criando aplicativos com a API REST dos Serviços de Mídia do Azure]: http://go.microsoft.com/fwlink/?linkid=252967
  [Protocolo Open Data]: http://odata.org/
  [WCF Data Services 5.0 para OData v3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Azure Marketplace]: https://datamarket.azure.com/
  [Desenvolvimento de cliente de Serviços de Mídia]: http://social.msdn.microsoft.com/Forums/pt-br/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Visualização dos Serviços de Mídia: recursos com suporte]: http://social.msdn.microsoft.com/Forums/pt-br/MediaServices/thread/eb946433-16f2-4eac-834d-4057335233e0
  [Versões futuras dos Serviços de Mídia: suporte planejado aos recursos]: http://social.msdn.microsoft.com/Forums/pt-br/MediaServices/thread/431ef036-0939-4784-a939-0ecb31151ded
  [Configuração de conta de Visualização dos Serviços de Mídia]: http://go.microsoft.com/fwlink/?linkid=247287
  [SDK dos Serviços de Mídia do Azure para .NET]: http://go.microsoft.com/fwlink/?LinkID=256500
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Instalando o SDK do Azure no Windows 8]: http://www.windowsazure.com/pt-br/develop/net/other-resources/windows-azure-on-windows-8/
  [Documentação dos Serviços de Mídia do Azure]: http://go.microsoft.com/fwlink/?linkid=245437
  [Introdução à CDN do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff919705.aspx
  [Fórum dos Serviços de Mídia]: http://social.msdn.microsoft.com/Forums/pt-br/MediaServices/threads
  [Introdução ao SDK dos Serviços de Mídia para .NET]: http://go.microsoft.com/fwlink/?linkid=252966
  [Criando aplicativos com o SDK dos Serviços de Mídia para .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Como criar uma conta de Serviços de Mídia]: http://go.microsoft.com/fwlink/?linkid=256662
  [Tipos de arquivos suportados para Serviços de Mídia]: http://msdn.microsoft.com/pt-br/library/hh973634



