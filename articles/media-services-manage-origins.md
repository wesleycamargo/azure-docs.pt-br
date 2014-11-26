<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="How to Manage Origins in a Media Services Account" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Como gerenciar origens em uma conta de Serviços de Mídia

Os Serviços de Mídia permitem adicionar várias origens de streaming à sua conta e configurá-las. Cada conta dos Serviços de Mídia tem pelo menos uma origem de streaming **padrão** associada a ela.

## Adicionando e excluindo origens

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2.  Selecione a página ORIGENS.
3.  Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que a origem padrão não pode ser excluída.
4.  Clique no botão INICIAR para iniciar a origem.
5.  Para configurar a origem, clique no nome da origem.

    ![Página Origem][Página Origem]

## Configurando a origem

A guia CONFIGURAR permite fazer as seguintes configurações:

1.  Definir o período de cache máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache que foi definido explicitamente no conteúdo do blob.

2.  Especificar endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3.  Especificar a configuração para autenticação do cabeçalho de assinatura do Akamai.

    ![Configurar a origem][Configurar a origem]

    A configuração nesta página não se aplica às origens que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda, vá para a guia ESCALA. Para obter informações detalhadas sobre unidades reservadas, consulte [Dimensionando os Serviços de Mídia (a página pode estar em inglês)][Dimensionando os Serviços de Mídia (a página pode estar em inglês)].

  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Página Origem]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configurar a origem]: ./media/media-services-manage-origins/media-services-origins-configure.png
