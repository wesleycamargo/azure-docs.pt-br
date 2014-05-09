<properties linkid="scripting-center-index" urlDisplayName="índice" pageTitle="Índice do Centro de Scripts" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como gerenciar as origens em uma conta de Serviços de Mídia" authors="juliako" solutions="" manager="" editor="" />





<h1><a id="managemediaservicesorigins"></a>Como gerenciar as origens em uma conta de Serviços de Mídia</h1>

Os Serviços de Mídia permitem adicionar várias origens de streaming à sua conta e configurá-las. Cada conta dos Serviços de Mídia tem pelo menos uma origem de streaming **padrão** associada a ela. 


<h2>Adicionando e excluindo origens</h2> 

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página ORIGENS. 
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que a origem padrão não pode ser excluída. 
4. Clique no botão INICIAR para iniciar a origem. 
5. Se desejar configurar a origem, clique no nome da origem. 

	![Página Origem][origin-page]

<h2>Configurando a origem</h2>

A guia CONFIGURAR permite fazer as seguintes configurações:

1. Definir o período de caching máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache que foi definido explicitamente no conteúdo do blob.

2. Especificar endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especificar a configuração para solicitações de autenticação G20 nos servidores Akami.


	![Configurar a origem][configure-origin]

	A configuração nesta página não se aplica às origens que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda, vá para a guia ESCALA. Para obter informações detalhadas sobre unidades reservadas, consulte [Dimensionando os Serviços de Mídia (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/).


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

