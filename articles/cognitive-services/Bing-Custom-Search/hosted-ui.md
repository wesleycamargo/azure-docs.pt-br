---
title: 'Pesquisa Personalizada do Bing: pesquisa de site | Microsoft Azure'
description: Descreve como configurar a interface de usuário hospedada
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158394"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a experiência de interface do usuário hospedada
Depois de configurar sua instância de pesquisa personalizada, você pode chamar a API de Pesquisa Personalizada para obter os resultados da pesquisa e exibi-los em seu aplicativo. Ou, se o seu aplicativo for um aplicativo Web, você poderá usar a interface do usuário hospedada fornecida pela Pesquisa Personalizada.   

## <a name="configure-custom-hosted-ui"></a>Configurar a interface de usuário hospedada personalizada
Use as instruções a seguir para configurar uma interface de usuário hospedada para incluir em seu aplicativo Web.
1.  Entre no [portal de](https://customsearch.ai) Pesquisa Personalizada.
2.  Clique em uma instância de Pesquisa Personalizada. Para criar uma instância, veja [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).
3.  Clique na guia **Interface do Usuário Hospedada**.
4.  Selecione um layout.
    - Barra de pesquisa e resultados (padrão) &mdash; Exibe uma caixa de pesquisa e os resultados da pesquisa
    - Somente os resultados &mdash; Não exibe uma caixa de pesquisa, mostra apenas os resultados
    - Pop-over &mdash; Não exibe uma caixa de pesquisa, mostra apenas os resultados em uma sobreposição deslizante
    
   > [!IMPORTANT]
   > Lembre-se de incluir o parâmetro de consulta customConfig ao selecionar o layout **Apenas resultados**, confira [Parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  Em **Configurações adicionais**, forneça os valores conforme apropriado para o seu aplicativo. Essas configurações são opcionais. Para ver o efeito da aplicação ou remoção delas, veja o painel de visualização à direita.  Há três opções de configuração disponíveis:
    - Configurações de pesquisa da Web:
        - Resultados da Web habilitados &mdash; Determina se os resultados da pesquisa da Web retornarão
        - Habilitar sugestão automática &mdash; Determina se a sugestão automática personalizada está habilitada
        - Resultados da Web por página &mdash; Número de resultados da pesquisa da Web para exibir por vez
        - Legenda da imagem &mdash; Determina se as imagens são exibidas com os resultados da pesquisa
        - Realçar palavras &mdash; Determina se os resultados são exibidos com os termos de pesquisa em negrito
    - Configurações de pesquisa de imagem:
        - Resultados de imagem habilitados &mdash; Determina se os resultados da pesquisa de imagem retornarão
    - Configurações diversas:
        - Título da página &mdash; Texto exibido na área de título da página
        - Tema de barra de ferramentas &mdash; Determina a cor do plano de fundo da área de título da página
        - Espaço reservado para texto da caixa de pesquisa &mdash; Texto exibido na caixa de pesquisa antes da entrada
        - Url de link do título &mdash; Destino para o link do título
        - Url do logotipo &mdash; Imagem exibida ao lado do título 
        - Url do favicon &mdash; Ícone exibido na barra de título do navegador

   > [!IMPORTANT]
   > É necessário habilitar pelo menos a Pesquisa de imagens ou a Pesquisa Web.

6.  Insira a chave de assinatura da pesquisa ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas do Azure da sua conta. Confira [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Se você habilitou a sugestão automática, insira a chave de assinatura de sugestão automática ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves de assinaturas do Azure da sua conta. A Sugestão Automática Personalizada exige um preço de assinatura específico, consulte as [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> À medida que você faz alterações na configuração de interface do usuário hospedada personalizada, o painel à direita fornece uma referência visual para as alterações feitas. Os resultados da pesquisa exibidos não são os resultados reais da instância

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir interface do usuário personalizada
Para consumir a interface de usuário hospedada: 

- Inclua o script em sua página da Web
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Use a URL fornecida `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > A página não pode exibir a política de privacidade ou outros avisos e termos. A adequação para o seu uso pode variar.  

Para obter informações adicionais, incluindo a ID de Configuração Personalizada, acesse **Pontos de extremidade** na guia **Produção**.

## <a name="next-steps"></a>Próximas etapas
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)