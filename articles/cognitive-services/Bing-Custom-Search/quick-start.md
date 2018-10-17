---
title: 'Início Rápido: criar a primeira instância da Pesquisa Personalizada do Bing'
titlesuffix: Azure Cognitive Services
description: Para usar a Pesquisa Personalizada do Bing, você precisa criar uma instância de pesquisa personalizada que defina o modo de exibição ou a fatia da web. A instância contém configurações que especificam os domínios públicos, subsites e páginas da Web que você deseja que o Bing pesquise e os ajustes de classificação.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816197"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: criar a primeira instância da Pesquisa Personalizada do Bing
Para usar a Pesquisa Personalizada do Bing, você precisa criar uma instância de pesquisa personalizada que defina o modo de exibição ou a fatia da web. A instância contém configurações que especificam os domínios públicos, websites e páginas da Web que você deseja que o Bing pesquise e os ajustes de classificação. Para criar a instância, use o [portal](https://customsearch.ai) da Pesquisa Personalizada do Bing. 

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1.  Obter uma chave de API de Pesquisa Personalizada. Consulte [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Clique no botão **Entrar** e entre no portal usando uma conta da Microsoft (MSA). 
    - Se você não tiver uma MSA, clique em **Criar uma conta Microsoft**. O portal solicita permissões para acessar seus dados. Clique em **Sim**.
    - Concorde com os Termos dos Serviços Cognitivos. Marque **Eu concordo** e clique em **Concordo**.  
3.  Depois de entrar, clique em **Nova Instância** e nomeie a instância. Use um nome que seja significativo e que descreva o tipo de conteúdo que a pesquisa retorna. É possível alterar o nome a qualquer momento. 
4.  Na guia **Ativo** em **Experiência de Pesquisa**, insira a URL de um ou mais sites que você quer incluir em sua pesquisa.
5.  Para confirmar que a sua instância retorna resultados, digite uma consulta no painel de visualização à direita. Se não houver resultados, especifique um novo site. O Bing retorna resultados apenas para sites públicos indexados.
6.  Clique em **Publicar** para publicar as alterações de configuração para a produção. Quando solicitado, clique em **Publicar** para confirmar.
7.  Clique em **Produção** > **Pontos de Extremidade** e copie a **ID de Configuração Personalizada**. Você precisa dessa ID para chamar a API de Pesquisa Personalizada.

## <a name="next-steps"></a>Próximas etapas

Continue a trabalhar com a instância da Pesquisa Personalizada que você criou seguindo as instruções desses guias de instruções:

- [Configurar a experiência de pesquisa personalizada](./define-your-custom-view.md)
- [Chamar sua pesquisa personalizada](./search-your-custom-view.md)
- [Compartilhar sua pesquisa personalizada](./share-your-custom-search.md)
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)
