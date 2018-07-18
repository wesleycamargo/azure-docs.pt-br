---
title: Melhores práticas - QnA Maker - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Use essas melhores práticas para melhorar a base de dados de conhecimento e fornecer melhores resultados aos usuários finais do aplicativo/chat.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364866"
---
# <a name="best-practices"></a>Melhores práticas
O [ciclo de vida da base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) orienta sobre como gerenciar a base de dados de conhecimento de ponta a ponta. Use essas melhores práticas para melhorar a base de dados de conhecimento e fornecer melhores resultados aos usuários finais do aplicativo/chat.

## <a name="extraction"></a>Extração
O QnA Maker está melhorando continuamente os algoritmos que extraem QnAs do conteúdo e expandindo a lista de formatos de arquivo e de página HTML com suporte. Siga as [diretrizes](../Concepts/data-sources-supported.md) para extração com base no tipo de documento do qual você está extraindo. 

Em geral, as páginas de perguntas frequentes devem ser independentes e não combinadas com outras informações. Manuais de produtos devem ter títulos claros e, de preferência, uma página de índice. 

## <a name="rankingmatching"></a>Classificação/Correspondência
Certifique-se de que você está utilizando da melhor forma os recursos de classificação com suporte do QnA Maker. Isso aumentará a probabilidade de que uma determinada consulta de usuário seja respondida com uma resposta apropriada.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Perguntas alternativas](../How-To/edit-knowledge-base.md) melhoram a probabilidade de uma correspondência com uma consulta de usuário. Perguntas alternativas são úteis quando há várias maneiras de como a mesma pergunta pode ser feita. Isso pode incluir alterações na estrutura da sentença (por exemplo, *"Há estacionamento disponível?"* em comparação a *"Tem estacionamento?"*) ou alteações no estilo de palavra e gíria (por exemplo, *"Oi"* em comparação a *"Opa"*, *"Olá!"*).

### <a name="use-metadata-filters"></a>Use filtros de metadados
[Metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de restringir os resultados de uma consulta de usuário com base em filtros. A resposta da base de dados de conhecimento poderá variar com base na marca de metadados, mesmo se a consulta for a mesma. Por exemplo, *"onde o estacionamento está localizado"* poderá ter uma resposta diferente se a localização do branch do restaurante for diferente - ou seja, os metadados são *Localização: Seattle* versus *Localização: Redmond*.)

### <a name="use-synonyms"></a>Usar sinônimos
Embora haja algum suporte para sinônimos no idioma inglês, use [alterações de palavras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd)para adicionar sinônimos a palavras-chave que apresentam formas diferentes (Exemplo: *comprar* -> *adquirir* ou *netbanking* -> *net banking*. Os sinônimos devem ser adicionados no nível de serviço do QnA Maker e compartilhados por todas as bases de dados de conhecimento no serviço.

### <a name="use-distinct-words-to-differentiate-questions"></a>Use palavras distintas para diferenciar perguntas
Os algoritmos de correspondência e classificação do QnA Maker que correspondem a uma consulta de usuário com uma pergunta na base de dados de conhecimento funcionam melhor se cada pergunta abordar necessidades diferentes. A repetição do mesmo conjunto de palavras entre as perguntas reduz a probabilidade da resposta correta ser escolhida para uma determinada consulta de usuário com essas palavras.

## <a name="collaborate"></a>Colaborar
O QnA Maker permite que os usuários [colaborem](../How-to/collaborate-knowledge-base.md) em uma base de dados de conhecimento. Os usuários exigem acesso ao grupo de recursos do QnA Maker do Azure para acessar as bases de dados de conhecimento. Algumas organizações podem querer terceirizar a edição e manutenção da base de dados de conhecimento e ainda proteger o acesso aos recursos do Azure. Esse modelo de aprovador de editor pode ser realizado, configurando dois [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) em diferentes assinaturas e designando um para o ciclo de teste de edição. Quando o teste estiver concluído, o conteúdo da base de dados de conhecimento poderá ser transferido com um processo de [importação-exportação](../Tutorials/migrate-knowledge-base.md) para o serviço do QnA Maker do aprovador que finalmente publicará a base de dados de conhecimento e atualizará o ponto de extremidade.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)

