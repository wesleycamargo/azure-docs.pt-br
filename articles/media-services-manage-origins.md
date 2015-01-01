<properties urlDisplayName="index" pageTitle="Como gerenciar origens em uma conta de Serviços de Mídia" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Streaming Endpoints in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


<h1><a id="managemediaservicesorigins"></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia</h1>

Os Serviços de Mídia permitem adicionar vários pontos de extremidade de streaming à sua conta e configurá-los. Cada conta dos Serviços de Mídia tem, pelo menos, um ponto de extremidade de streaming chamada **padrão** associado a ela.

>[WACOM.NOTE] Pontos de extremidade de streaming costumavam ser conhecidos como Origens. 


<h2>Adicionando e excluindo pontos de extremidade de streaming</h2> 

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**.Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de PONTOS DE EXTREMIDADE DE STREAMING. 
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído. 
4. Clique no botão INICIAR para iniciar o ponto de extremidade de streaming. 
5. Clique no nome do ponto de extremidade de streaming para configurá-lo.   

	![Origin page][origin-page]

<h2>Configurando o ponto de extremidade de streaming</h2>

A guia CONFIGURAR permite fazer as seguintes configurações:

1. Defina o período de cache máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache definido explicitamente no conteúdo do blob.

2. Especifique endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especifique a configuração para autenticação do cabeçalho de assinatura do Akamai.



	![Configure origin][configure-origin]

	A configuração nesta página se aplica apenas aos pontos de extremidade de streaming que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda, vá para a guia ESCALA. Para obter informações detalhadas sobre unidades reservadas, consulte[Dimensionando os Serviços de Mídia](../media-services-how-to-scale/).


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

<!--HONumber=35_1-->
