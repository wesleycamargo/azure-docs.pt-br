---
title: Listas de frases para aprimorar a detecção de entidade
titleSuffix: Azure Cognitive Services
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para adicionar recursos de aplicativos que podem melhorar a detecção ou previsão de intenções e entidades que as categorias e padrões
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6831f67a33d114ca5c42ddacf8ef4de704e21711
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036949"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Usar listas de frases para melhorar o sinal da lista de palavras

É possível adicionar recursos ao aplicativo de LUIS para melhorar a precisão. Os recursos ajudam o LUIS fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário do domínio do aplicativo. 

## <a name="add-phrase-list"></a>Adicionar lista de frases

1. Abra o aplicativo, clicando no nome do aplicativo na página **Meus Aplicativos**, clique em **Compilar** e, em seguida, clique em **Listas de frases** no painel esquerdo do seu aplicativo. 

2. Na página **Lista de frases**, clique em **Criar nova lista de frases**. 
 
3. Na caixa de diálogo **Adicionar Lista de Frases**, digite "Cidades" como o nome da lista de frases. Na caixa de **Valor**, digite os valores da lista de frases. É possível digitar um valor por vez ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    ![Adicionar lista de frases Cidades](./media/luis-add-features/add-phrase-list-cities.png)

4. O LUIS pode propor valores relacionados para adicionar à lista de frases. Clique em **Recomendado** para obter um grupo de valores propostos relacionados semanticamente com o(s) valor(es) adicionado(s). É possível clicar em qualquer um dos valores propostos ou clicar em **Adicionar Todos** para adicionar a todos.

    ![Valores propostos à lista de frases](./media/luis-add-features/related-values.png)

5. Clique em **Esses valores são intercambiáveis**, se os valores da lista de frases adicionados forem alternativas que podem ser usadas de forma intercambiável.

    ![Valores propostos à lista de frases](./media/luis-add-features/interchangeable.png)

6. Clique em **Salvar**. A lista de frases "Cidades" é adicionada à página **Listas de Frases**.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Você pode editar, excluir ou desativar uma lista de frases no botão de reticências (***...***) no final da linha de cada lista de frases.

## <a name="pattern-regular-expression-feature"></a>Recurso padrão (expressão regular) 
**Esse recurso está preterido**. Novos recursos de padrão não podem ser adicionados ao LUIS. Todos os recursos de padrão existentes terão suporte até maio de 2018. Contribua para a expressão regular de LUIS padrão correspondente a um PR ao [repositório Github Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Próximas etapas

Após adicionar, editar, excluir ou desativar uma lista de frases, [treine e teste o aplicativo](luis-interactive-test.md) novamente para verificar se o desempenho melhora.
