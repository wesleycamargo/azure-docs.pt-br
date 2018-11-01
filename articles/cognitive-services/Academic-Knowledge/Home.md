---
title: O que é a API de Conhecimento Acadêmico?
titlesuffix: Azure Cognitive Services
description: Use a API de Conhecimento Acadêmico para interpretar consultas de usuário e recuperar informações detalhadas do Academic Graph.
services: cognitive-services
author: darrine
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: aa945eb8f0b79a6b0760650bd34dba55d80ef3a4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240351"
---
# <a name="academic-knowledge-api"></a>API de Conhecimento Acadêmico

Seja bem-Vindo ao API Com esse serviço, você poderá interpretar consultas de usuários para fins acadêmicos e recuperar informações ricas do MAG (Microsoft Academic Graph). A base de dados de conhecimento MAG é um gráfico de entidades heterogêneas de escala da web composto de entidades que modelam atividades acadêmicas: campo de estudo, autor, instituição, papel, local e eventos. 

Os dados MAG são extraídos do índice de web do Bing como uma base de Conhecimento interna do Bing. Como resultado da indexação do Bing contínua, essa API conterá informações atualizadas da Web após a descoberta e indexação pelo Bing. Com base nesse conjunto de dados, as APIs de dados de Conhecimento acadêmico permitem que uma caixa de diálogo interativa, controlada por conhecimento que combina perfeitamente pesquisa reativa com experiências de sugestão proativa, resultados de pesquisa de gráfico de papel de pesquisa avançada e distribuições de histograma das valores de atributo para um conjunto de documentos e entidades relacionadas.

Para obter mais informações sobre o Microsoft Academic Graph, consulte [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

A API de Conhecimento Acadêmico foi movido da versão prévia de Serviços Cognitivas para os Laboratórios dos Serviços Cognitivos. A nova página inicial para o projeto é [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Sua chave de API existente continuará a funcionar até 24 de maio de 2018. Após essa data, gere uma nova chave de API. Observe que a visualização paga não estará disponível depois que a chave existente expirar. Entre em contato com nossa equipe se a camada gratuita da API não for suficiente para suas finalidades. 

## <a name="features"></a>Recursos
A API de Conhecimento Acadêmico consiste em quatro pontos de extremidade REST relacionados:  
  1. **interpret** – Interpreta uma cadeia de consulta de linguagem natural do usuário. Retorna interpretações anotadas para habilitar experiências de preenchimento automático da caixa de pesquisa que antecipam o que o usuário está digitando.  
  2. **evaluate** – avalia uma expressão de consulta e retorna os resultados de entidade de Conhecimento Acadêmico.  
  3. **calchistogram** – calcula um histograma da distribuição dos valores de atributo para as entidades acadêmicas retornadas por uma expressão de consulta, como a distribuição de citações por ano para determinado autor.  
  
Usados juntos, esses métodos de API permitem a criação de uma experiência avançada de pesquisa semântica. Dada uma cadeia de caracteres de consulta do usuário, o método **interpret** fornece uma versão comentada da consulta e uma expressão de consulta estruturada, ao concluir, opcionalmente, a consulta do usuário com base na semântica de acadêmicas a dados subjacentes. Por exemplo, se um usuário digita a cadeia de caracteres *latentes*, o método **interpret** pode fornecer um conjunto de interpretações classificadas, sugerindo que o usuário pode estar pesquisando o campo de estudo *análise semântica latente*, a *análise de estrutura latente*, ou outras expressões de entidade começando com *latentes*. Essas informações podem ser usadas para orientar rapidamente o usuário para os resultados de pesquisa desejado.

O método **evaluate** pode ser usado para recuperar um conjunto de correspondência de entidades de papel da base de conhecimento acadêmica, e o método **calchistogram** pode ser usado para calcular a distribuição de valores atribuídos para um conjunto de entidades de papel que podem ser usados para depois filtrar os resultados da pesquisa.        

## <a name="getting-started"></a>Introdução 
Consulte os subtópicos à esquerda para a documentação detalhada.  Observe que, para melhorar a legibilidade dos exemplos, as chamadas da API REST contém caracteres (tais como espaços) que não foram codificados de URL.  Seu código precisa aplicar as codificações de URL apropriadas.
