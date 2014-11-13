<properties urlDisplayName="index" pageTitle="Como gerenciar as origens em uma conta de Servi&ccedil;os de M&iacute;dia" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como gerenciar pontos de extremidade de streaming em uma conta de Servi&ccedil;os de M&iacute;dia" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia

Os Serviços de Mídia permitem adicionar vários pontos de extremidade de streaming à sua conta e configurá-los. Cada conta dos Serviços de Mídia tem, pelo menos, um ponto de extremidade de streaming **padrão** associado a ela.

> [WACOM.NOTE] Pontos de extremidade de streaming usados que serão conhecidos como Origens.

## Adicionando e excluindo pontos de extremidade de streaming

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2.  Selecione a página de PONTOS DE EXTREMIDADE DE STREAMING.
3.  Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído.
4.  Clique no botão INICIAR para iniciar o ponto de extremidade de streaming.
5.  Clique no nome do ponto de extremidade de streaming para configurá-lo.

    ![Página Origem][Página Origem]

## Configurando o ponto de extremidade de streaming

A guia CONFIGURAR permite fazer as seguintes configurações:

1.  Definir o período de cache máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache que foi definido explicitamente no conteúdo do blob.

2.  Especificar endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3.  Especificar a configuração para autenticação do cabeçalho de assinatura do Akamai.

    ![Configurar a origem][Configurar a origem]

    A configuração nesta página se aplica apenas aos pontos de extremidade de streaming que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda, vá para a guia ESCALA. Para obter informações detalhadas sobre unidades reservadas, consulte [Dimensionando os Serviços de Mídia (a página pode estar em inglês)][Dimensionando os Serviços de Mídia (a página pode estar em inglês)].

  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Página Origem]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configurar a origem]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [Dimensionando os Serviços de Mídia (a página pode estar em inglês)]: ../media-services-how-to-scale/
