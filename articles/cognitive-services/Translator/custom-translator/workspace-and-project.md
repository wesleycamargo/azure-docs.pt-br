---
title: O que é um espaço de trabalho e projeto? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um espaço de trabalho é uma área de trabalho para compor e construir o sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos. Um projeto é um wrapper para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são carregados nesse espaço de trabalho que tenham o par de idiomas correto.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 6f88ff8ecd1aee588cb82c08ae2eda58fe2eb1e7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626650"
---
# <a name="what-is-a-custom-translator-workspace"></a>O que é um espaço de trabalho do Tradutor Personalizado?

Um espaço de trabalho é uma área de trabalho para compor e construir o sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos. Todos os trabalhos que você fizer no Tradutor Personalizado estará em um espaço de trabalho específico.

O espaço de trabalho é particular a você e às pessoas que você convida para o seu espaço de trabalho. Pessoas não convidadas não têm acesso ao conteúdo do seu espaço de trabalho. Você pode convidar quantas pessoas quiser para o seu espaço de trabalho e modificar ou remover os respectivos acessos a qualquer momento. Além disso, você também pode criar um novo espaço de trabalho. Por padrão, um espaço de trabalho não conterá nenhum projeto ou documento que esteja em outros espaços de trabalho.

## <a name="what-is-a-custom-translator-project"></a>O que é um projeto do Tradutor Personalizado?

Um projeto é um wrapper para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são carregados nesse espaço de trabalho que tenham o par de idiomas correto. Por exemplo, se você tiver um projeto de inglês para espanhol e um de espanhol para inglês, os mesmos documentos serão incluídos nos dois projetos. Cada projeto tem uma ID de Categoria associada ao projeto que é utilizada ao consultar a [API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) para traduções. A ID de Categoria é o parâmetro usado para obter traduções de um sistema personalizado criado com o Tradutor Personalizado.

## <a name="project-categories"></a>Categorias de projeto

A categoria identifica o domínio – a área de terminologia e estilo que você deseja usar – do seu projeto. Escolha a categoria mais relevante para seus documentos. Em alguns casos, a escolha da categoria influencia diretamente o comportamento do Tradutor Personalizado.

Ainda não há modelos personalizados para categorias, exceto um sistema geral de linha de base. Ainda assim, é recomendável que os usuários selecionem a categoria mais aplicável ao domínio, de modo que possa ser utilizada como um identificador na ID de Categoria. Para projetos no domínio de tecnologia, selecionar "Tecnologia" garantirá que quando um modelo de linha de base estiver disponível, seu projeto poderá utilizá-lo.

No mesmo espaço de trabalho, é possível criar projetos para o mesmo par de idiomas em diferentes categorias. O Tradutor Personalizado impede a criação de um projeto duplicado com o mesmo par de idiomas e categoria. Aplicar um rótulo ao projeto permite evitar essa restrição. Não use rótulos, a menos que esteja criando sistemas de tradução para vários clientes, pois adicionar um rótulo exclusivo ao projeto refletirá na ID de Categoria de projetos.

## <a name="project-labels"></a>Rótulos de projetos

O Tradutor Personalizado permite atribuir um rótulo de projeto ao seu projeto. O rótulo de projeto faz distinção entre vários projetos com o mesmo par de idiomas e categoria. Como melhor prática evite usar rótulos de projeto, exceto se necessário.

O rótulo de projeto é usado como parte da ID de Categoria. Se o rótulo de projeto não for definido ou se for definido de maneira idêntica nos projetos, os projetos com a mesma categoria e pares de idiomas *diferentes* compartilharão a mesma ID de Categoria. Essa abordagem é vantajosa porque permite que você ou seu cliente alterne entre idiomas ao usar a API de Tradução de Texto sem se preocupar com uma ID de Categoria exclusiva para cada projeto.

Por exemplo, se eu quisesse habilitar traduções no domínio de Tecnologia de inglês para francês e de francês para inglês, eu criaria dois projetos: um para inglês -\> francês, e um para francês -\> inglês. Especificaria a mesma categoria (Tecnologia) para ambos e deixaria o rótulo de projeto em branco. A ID de Categoria para ambos os projetos corresponderia e, desse modo, eu poderia consultar a API para ambas as traduções de inglês e francês sem a necessidade de modificar minha ID de Categoria.

Se você é um provedor de serviços de linguagem e deseja atender a vários clientes com modelos diferentes que mantenham a mesma categoria e par de idiomas, usar um rótulo de projeto para diferenciar os clientes é a decisão acertada.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [Treinamento e modelo](training-and-model.md) para saber como criar um modelo de tradução com eficiência.