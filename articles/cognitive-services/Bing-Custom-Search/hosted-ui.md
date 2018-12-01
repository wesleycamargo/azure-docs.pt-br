---
title: Pesquisa do site, usar a Pesquisa Personalizada do Bing de interface do usuário hospedada
titlesuffix: Azure Cognitive Services
description: Descreve como configurar a Pesquisa Personalizada do Bing da interface do usuário hospedada.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: c71597cf540cca67b9558ce28d20ce1d21ae0243
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424979"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a experiência de interface do usuário hospedada

Depois de configurar sua instância de pesquisa personalizada, você pode chamar a API de Pesquisa Personalizada para obter os resultados da pesquisa e exibi-los em seu aplicativo. Ou, se o seu aplicativo for um aplicativo Web, você poderá usar a interface do usuário hospedada fornecida pela Pesquisa Personalizada.   

## <a name="configure-custom-hosted-ui"></a>Configurar a interface de usuário hospedada personalizada

Para configurar uma interface do usuário hospedada para seu aplicativo Web, siga estas etapas:

1. Entre no [portal de](https://customsearch.ai) Pesquisa Personalizada.  
  
2. Clique em uma instância de Pesquisa Personalizada. Para criar uma instância, veja [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).  

3. Clique na guia **Interface do Usuário Hospedada**.  
  
4. Selecione um layout.
  
  - Barra de pesquisa e resultados (padrão) &mdash; Este layout é sua página de pesquisa tradicional com a caixa de pesquisa e os resultados da pesquisa.
  - Resultados apenas &mdash; Este layout exibe apenas resultados da pesquisa. Este layout não exibe uma caixa de pesquisa. Você deve fornecer a consulta de pesquisa adicionando o parâmetro de consulta (&q=\<cadeia de caracteres de consulta>) para a URL de solicitação no snippet de JavaScript ou link de ponto de extremidade HTML.
  - Pop-over &mdash; Este layout fornece uma caixa de pesquisa e exibe os resultados da pesquisa em uma sobreposição deslizante.
      
5. Selecione um tema de cor. Os temas possíveis são: 
  
  - Clássico
  - Escuro
  - Horizonte Azul

  Clique em cada um dos temas para ver qual funciona melhor com seu aplicativo Web. Se você precisar ajustar o tema de cores para integrar-se melhor ao seu aplicativo Web, clique em **Personalizar tema**. Nem todas as configurações de cores se aplicam a todos os temas de layout. Para alterar uma cor, insira o valor RGB HEX da cor (por exemplo, # 366eb8) na caixa de texto correspondente. Ou clique no botão de cor e clique na sombra que funciona para você. 
  
  Depois de alterar uma cor, confira como a alteração afeta o exemplo de visualização à direita. Você sempre poderá clicar em **Redefinir para padrão** voltar para as cores padrão para o tema selecionado.

  > [!NOTE]
  > Quando você alterar o tema de cores, considere a acessibilidade ao escolher as cores.

5. Em **Configurações adicionais**, forneça os valores conforme apropriado para o seu aplicativo. Essas configurações são opcionais. Para ver o efeito da aplicação ou remoção delas, veja o painel de visualização à direita. Há três opções de configuração disponíveis:  
  
  - Configurações de pesquisa da Web:
    - Resultados da Web habilitados &mdash; Determina se a pesquisa na Web está habilitada (você verá uma guia da Web na parte superior da página).
    - Habilitar sugestão automática &mdash; Determina se a sugestão automática personalizada está habilitada (consulte [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para ver o custo adicional).
    - Resultados por página da Web &mdash; Número de resultados da pesquisa na Web a exibir de cada vez (o máximo é de 50 resultados por página).
    - Legenda da imagem &mdash; Determina se as imagens são exibidas com os resultados da pesquisa.
  
    As configurações a seguir serão mostradas se você clicar em **Mostrar configurações avançadas**.  
  
    - Realçar palavras &mdash; Determina se os resultados são exibidos com os termos de pesquisa em negrito. 
    - Destino do link &mdash; Determina se a página da Web será aberta em uma nova guia do navegador (em branco) ou na mesma guia do navegador (própria) quando o usuário clicar em um resultado de pesquisa. 

  - Configurações de pesquisa de imagem:
    - Resultados da imagem habilitados &mdash; Determina se a pesquisa de imagem está habilitada (você verá uma guia imagens na parte superior da página).   
    - Resultados de imagem por página &mdash; Número de resultados da pesquisa de imagens a exibir de cada vez (o máximo é de 150 resultados por página).  
  
    A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
    - Habilitar filtros &mdash; Adiciona filtros que o usuário pode usar para filtrar as imagens que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados somente para GIFs animados.

  - Configurações de pesquisa de vídeo:
    - Resultados de vídeos habilitados &mdash; Determina se a pesquisa de vídeo está habilitada (você verá uma guia de vídeos na parte superior da página).  
    - Resultados de vídeo por página &mdash; Número de resultados da pesquisa de vídeos a exibir de cada vez (o máximo é de 150 resultados por página).
  
    A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
    - Habilitar filtros &mdash; Adiciona filtros que o usuário pode usar para filtrar os vídeos que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados para vídeos com uma determinada resolução ou vídeos descobertos nas últimas 24 horas.

  - Configurações diversas:
    - Título da página &mdash; Texto exibido na área de título da página de resultados da pesquisa (não para o layout de pop-over).
    - Tema da barra de ferramentas &mdash; Determina a cor da tela de fundo da área de título da página de resultados da pesquisa.  
  
    As configurações a seguir serão mostradas se você clicar em **Mostrar configurações avançadas**.  
  
    - Espaço reservado para texto da caixa de pesquisa &mdash; Texto exibido na caixa de pesquisa antes da entrada.
    - URL de link do título &mdash; Destino para o link do título.
    - URL do logotipo &mdash; Imagem exibida ao lado do título. 
    - URL do Favicon &mdash; Ícone exibido na barra de título do navegador.  

    As configurações a seguir serão aplicáveis somente se você consumir a interface do usuário hospedada pelo ponto de extremidade HTML (não serão aplicáveis se você usar o snippet de JavaScript).
    
    - Título da página
    - Tema da barra de ferramentas
    - URL do link de título
    - URL do logotipo
    - URL do Favicon  
  
6. Insira a chave de assinatura da pesquisa ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas da sua conta do Azure. Confira [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Se você habilitou a sugestão automática, insira a chave de assinatura de sugestão automática ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas da sua conta do Azure. A Sugestão Automática Personalizada exige um preço de assinatura específico, consulte o [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> À medida que você faz alterações na configuração de interface do usuário hospedada personalizada, o painel à direita fornece uma referência visual para as alterações feitas. Os resultados da pesquisa exibidos não são os resultados reais da instância.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir interface do usuário personalizada

Para consumir a interface de usuário hospedada: 

- Inclua o script em sua página da Web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Outra opção é usar a seguinte URL em um navegador da Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Adicione os seguintes parâmetros de consulta à URL conforme necessário. Para obter informações sobre esses parâmetros, consulte a referência [API de Pesquisa Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > A página não pode exibir a política de privacidade ou outros avisos e termos. A adequação para o seu uso pode variar.  

Para obter informações adicionais, incluindo a ID de Configuração Personalizada, acesse **Pontos de extremidade** na guia **Produção**.

## <a name="next-steps"></a>Próximas etapas

- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)
