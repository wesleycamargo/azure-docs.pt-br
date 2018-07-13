---
title: 'Pesquisa Personalizada do Bing: defina sugestões personalizadas da Sugestão Automática | Microsoft Docs'
description: Descreve como configurar a Sugestão Automática Personalizada com sugestões personalizadas
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363840"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurar experiência de sugestão automática personalizada
Se você se inscreveu na Pesquisa Personalizada no nível apropriado (consulte as [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), pode personalizar as sugestões de pesquisa feitas em sua experiência de Pesquisa Personalizada. A Sugestão Automática Personalizada retorna uma lista de consultas sugeridas com base em uma cadeia de caracteres de consulta parcial que o usuário fornece. Com a Sugestão Automática Personalizada, você deve fornecer sugestões de pesquisa personalizada relevantes para sua experiência de pesquisa. Você especifique se deseja retornar somente as sugestões personalizadas ou para incluir também as sugestões do Bing. Se você incluir sugestões do Bing, as sugestões personalizadas aparecem antes das sugestões do Bing. As sugestões do Bing são restritas ao contexto de sua instância da Pesquisa Personalizada.

## <a name="configure-custom-autosuggest"></a>Configurar Sugestão Automática Personalizada
Use as instruções a seguir para configurar a Sugestão Automática Personalizada para sua instância da Pesquisa Personalizada.

1.  Entre na [Pesquisa Personalizada](https://customsearch.ai).
2.  Clique em uma instância de Pesquisa Personalizada. Para criar uma instância, veja [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).
3.  Clique na guia **Sugestão Automática**.

## <a name="enable-bing-suggestions"></a>Habilitar sugestões do Bing
Para habilitar as sugestões do Bing, alterne o controle deslizante **Sugestões automáticas do Bing** para a posição ativada. O controle deslizante fica azul.

## <a name="add-suggestions"></a>Adicionar sugestões
Para adicionar uma sugestão, insira-a na caixa de texto. Pressione a tecla enter ou clique no ícone **+**. As sugestões personalizados podem estar em qualquer idioma e aparecerão antes de sugestões de Bing.

## <a name="upload-suggestions"></a>Carregar sugestões
Você pode carregar uma lista de sugestões de um arquivo. Coloque cada sugestão em uma linha separada. Clique no ícone de upload e selecione seu arquivo.

## <a name="remove-suggestions"></a>Remover sugestões
Para remover uma sugestão, clique no ícone de remoção ao lado de sugestão que deseja remover.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Pode levar até 24 horas para que as alterações de configuração personalizada de Sugestão Automática Personalizada entram em vigor.

## <a name="next-steps"></a>Próximas etapas

- [Obter sugestões personalizadas](./get-custom-suggestions.md)
- [Pesquisar sua instância personalizada](./search-your-custom-view.md)
- [Configurar e consumir a interface de usuário personalizada hospedada](./hosted-ui.md)