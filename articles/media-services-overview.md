<properties 
	pageTitle="Visão geral dos Serviços de Mídia do Azure" 
	description="Este tópico oferece uma visão geral dos Serviços de Mídia do Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

#Visão geral dos Serviços de Mídia do Azure

Os Serviços de Mídia do Microsoft Azure tratam-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia.

Para compilar soluções de serviços de mídia, você pode usar:

- [API REST dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um dos SDKs de cliente disponíveis: [O SDK dos serviços de mídia do Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java), [do Azure Media Services para Node. js](https://github.com/fritzy/node-azure-media), [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php)
- Ferramentas existentes: [Portal de gerenciamento do Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Contrato de nível de serviço (SLA)**: Serviços de mídia garantem a disponibilidade de 99,9% de transações de API REST para codificação de serviços de mídia. Streaming sob demanda atenderá com êxito solicitações com uma garantia de disponibilidade de 99,9% para conteúdos de mídia existentes em casos que pelo menos uma unidade reservada para streaming é adquirida. Disponibilidade é calculada durante um ciclo de cobrança mensal. Para obter mais informações, baixe o [Documento do SLA](https://www.microsoft.com/download/details.aspx?id=39302).

Pôster do seguinte descreve os fluxos de trabalho dos Serviços de Mídia do Azure, desde a criação de mídia por meio de consumo. Você pode baixar o cartaz aqui: [Cartaz dos Serviços de Mídia do Azure](http://www.microsoft.com/download/details.aspx?id=38195).

![Overview][overview]

##Conceitos

Para obter mais informações, consulte [Conceitos](../media-services-concepts).

##Escolhendo a sua experiência de mídia

Uma das primeiras etapas no compartilhamento de conteúdo de vídeo é decidir que tipo de experiência você deseja que seus clientes tenham. Como os clientes visualizarão o conteúdo do vídeo? Eles estarão conectados à Internet? Eles verão seu conteúdo em um computador ou um dispositivo portátil? Os clientes vão esperar o vídeo esteja em HD? Perguntas como essas podem ajudar você a oferecer aos clientes a melhor experiência possível.

###Acessando o vídeo
 
Existem quatro maneiras básicas pelas quais os clientes podem acessar vídeos:

- Exibição offline 
- Download progressivo
- Taxa de bits de Streaming adaptável\Streaming

####Exibição offline

Para exibir um vídeo offline um cliente baixará o vídeo inteiro em seu computador ou dispositivo. Os vídeos tendem a ser muito grandes, por isso pode levar algum tempo para concluir o download. O benefício da exibição offline é que você não precisa de uma conexão de rede para exibir o vídeo quando ele foi baixado para o seu dispositivo. 

####Download progressivo

O download progressivo requer quer um cliente esteja conectado à Internet e permita que eles comecem a ver o vídeo antes que todo o vídeo tenha sido baixado. Ambas as abordagens de download offline quanto exibição progressiva exigem que o dispositivo que o cliente está usando para exibir o vídeo tenha espaço de armazenamento suficiente para manter o vídeo inteiro.

####de streaming

Tecnologias de streaming também exigem uma conexão com a Internet, mas eles baixam uma pequena parte do vídeo por vez e descarta após ela ser exibida. Isso requer muito pouco de armazenamento no dispositivo de exibição. O rendimento de uma conexão de rede pode variar, mas os clientes ainda esperam conseguir exibir vídeos, independentemente da largura de banda de rede. Tecnologias de taxa de bits adaptável permitem que os aplicativos de player de vídeo determinar as condições da rede e selecionar entre várias taxas de bits. Quando a comunicação da rede degrada, o cliente pode selecionar uma taxa de bits inferior, permitindo que o player continue a reproduzir o vídeo com uma qualidade de vídeo inferior. Como melhorarem as condições de rede cliente pode alternar para uma taxa de bits mais alta com melhor qualidade de vídeo. Os Serviços de Mídia do Azure dão suporte às seguintes tecnologias de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS.

###Em quais dispositivos

Outra decisão que precisa ser feita é o tipo de dispositivo que o cliente usará para exibir seus vídeos. Os Serviços de Mídia fornecem suporte para navegadores da Web, Smartphones, tablets, XBOX, decodificadores de sinais e TVs conectadas.

####Navegadores da Web

Navegadores da Web podem ser executados em computadores Windows, Macintosh PCs e Smartphones. Quando em execução em PCs ou Macintosh, você pode aproveitar a tela grande e a maior capacidade de armazenamento. Isso permite transmitir vídeos de qualidade superiores. PCs Windows ou Macintosh podem exibir vídeos entregues pelos Serviços de Mídia por meio de um aplicativo nativo ou um navegador da Web compatível com HTML. Aplicativos nativos podem dar suporte a Smooth Streaming, HLS da Apple, Download progressivo ou exibição offline. Páginas da web HTML5 dão suporte a Download progressivo.


####Os smartphones

Os smartphones têm telas pequenas e capacidades de armazenamento menores. Streaming é a melhor escolha para esses dispositivos. iPhones, Windows Phones e telefones Android têm suporte. iPhones e telefones Android dão suporte tanto a Smooth Streaming quanto a HLS. Windows Phones dão suporte a Smooth Streaming.

###Tablets

Tablets têm telas maiores que a dos Smartphones, mas até normalmente têm menor capacidade de armazenamento. Streaming é a melhor escolha para tablets. Tablets com maiores capacidades de armazenamento também podem tirar proveito da exibição offline, bem como do Download progressivo.

####Xbox

Os consoles XBox têm a vantagem de telas grandes e maior capacidade de armazenamento, o que torna offline, o download progressivo e streaming de uma boa opção.
Decodificadores de sinais e TVs conectadas
Esses dispositivos também têm uma tela grande, mas capacidade de armazenamento mínima, com streaming sendo a melhor opção.

###Tecnologias com suporte pelo dispositivo

A tabela a seguir mostra cada tipo de dispositivo e as tecnologias de cliente com suporte pelos Serviços de Mídia:
 
<table border="1">
<tr><th>Dispositivo</th><th>Tecnologias</th></tr>
<tr><td>iOS</td><td>Smooth Streaming, HLS Apple, Download progressivo</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming, Download progressivo</td></tr>
<tr><td>Telefones Android</td><td>Smooth Streaming e HLS Apple</td></tr>
<tr><td>Xbox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>HLS Apple, Download progressivo</td></tr>
<tr><td>Decodificador de sinais, TV conectada</td><td>Smooth Streaming, HLS Apple, Download progressivo</td></tr>
</table>


##Fluxos de trabalho de desenvolvimento comuns: Vídeo sob demanda e transmissão ao vivo

###Vídeo sob demanda: Fornecer conteúdo de streaming 

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
1. Codificar para MP4 definir a taxa de bits adaptável.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico).
1. (Opcionalmente) Configure a proteção de conteúdo e a política de autorização-chave (usada pela criptografia dinâmica).
1. Publique o ativo.
1. Fluxo de conteúdo publicado. 

###Vídeo sob demanda: Conteúdo do índice

1. Carregar um arquivo mezzanine de alta qualidade em um Ativo.
1. Conteúdo do índice.
1. Consumir conteúdo indexado.

###Vídeo sob demanda: Entregar o download progressivo 

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
1. Codificar para conjunto de MP4 com taxa de bits adaptável ou para um único MP4.
1. Publique o ativo.
1. Download progressivo de conteúdo.

[Serviços de Mídia de vídeo sob demanda](../media-services-video-on-demand-workflow) Contém links para tópicos que mostram como executar tarefas as mencionadas acima.

###Streaming ao vivo

1. Criar e iniciar um canal.
1. URL de ingestão de recuperação do canal.
1. Inicie e configure o transcodificador ao vivo de sua escolha.
1. Recuperar o ponto de extremidade de visualização do canal e verificar se o canal está recebendo corretamente a transmissão ao vivo.
1. Crie um programa e ativo. 
2. Configure a política de entrega para o ativo (usada pelo empacotamento dinâmico).
3. (Opcionalmente) Configure a proteção de conteúdo e a política de autorização-chave (usada pela criptografia dinâmica).
1. Para disponibilizar o fluxo para os usuários, crie um localizador de streaming necessário para acessar o ativo associado ao programa.
1. Inicie o programa quando estiver pronto para iniciar a transmissão e o arquivamento.
1. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
1. Exclua o programa (e, opcionalmente, exclua o ativo).

[Streaming ao vivo dos serviços de mídia](../media-services-live-streaming-wrokflow) Contém links para tópicos que mostram como executar tarefas as mencionadas acima.

##Clientes

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais.

- [SDK do cliente do Smooth Streaming](http://www.iis.net/downloads/microsoft/smooth-streaming)
- [Plataforma de Mídia da Microsoft: Estrutura de Player](http://playerframework.codeplex.com/) 
- [Documentação da estrutura de Player HTML5](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation)
- [Microsoft Smooth Streaming plug-in para OSMF](https://www.microsoft.com/download/details.aspx?id=36057)
- [Media Player Framework para iOS](https://github.com/Azure/azure-media-player-framework)
- [Licenciamento do kit de portabilidade de cliente do Microsoft(r) Smooth Streaming](https://www.microsoft.com/mediaplatform/sspk.aspx)
- Criando aplicativos de vídeo no Windows 8 
- [Desenvolvimento de aplicativos de vídeo do XBOX](http://xbox.create.msdn.com/)

Para obter mais informações, consulte [desenvolvendo aplicativos de Player de vídeo](../media-services-develop-video-players)

##Padrões e práticas de orientação

[Padrões e práticas de orientação](https://wamsg.codeplex.com/)
[Documentação online](https://msdn.microsoft.com/library/dn735912.aspx)
[Livro eletrônico para download](https://www.microsoft.com/download/details.aspx?id=42629)

##Suporte

[Suporte do Azure](http://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo serviços de mídia.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png

<!--HONumber=47-->
