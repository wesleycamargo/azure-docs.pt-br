---
title: Adicionar recursos em aplicativos de LUIS | Microsoft Docs
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para adicionar recursos de aplicativos que podem melhorar a detecção ou previsão de intenções e entidades que as categorias e padrões
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222946"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Usar recursos para melhorar o desempenho do aplicativo de LUIS  

É possível adicionar recursos ao aplicativo de LUIS para melhorar a precisão. Os recursos ajudam o LUIS, fornecendo dicas de que certas palavras e frases fazem parte de uma categoria. Se o LUIS aprender a reconhecer um membro da categoria, ele poderá tratar os outros da mesma forma.

## <a name="add-phrase-list"></a>Adicionar lista de frases

1. Abra o aplicativo, clicando no nome do aplicativo na página **Meus Aplicativos**, clique em **Compilar** e, em seguida, clique em **Listas de frases** no painel esquerdo do seu aplicativo. 

    ![Navegação por lista de frases](./media/luis-add-features/phrase-list-nav.png)

2. Na página **Lista de frases**, clique em **Criar nova lista de frases**. 
 
    ![Criar nova lista de frases](./media/luis-add-features/create-new-phrase-list.png)
    
3. Na caixa de diálogo **Adicionar Lista de Frases**, digite "Cidades" como o nome da lista de frases. Na caixa de **Valor**, digite os valores da lista de frases. É possível digitar um valor por vez ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    ![Adicionar lista de frases Cidades](./media/luis-add-features/add-phrase-list-cities.png)

4. O LUIS pode propor valores relacionados para adicionar à lista de frases. Clique em **Recomendado** para obter um grupo de valores propostos relacionados semanticamente com o(s) valor(es) adicionado(s). É possível clicar em qualquer um dos valores propostos ou clicar em **Adicionar Todos** para adicionar a todos.

    ![Valores propostos à lista de frases](./media/luis-add-features/related-values.png)

5. Clique em **Esses valores são intercambiáveis**, se os valores da lista de frases adicionados forem alternativas que podem ser usadas de forma intercambiável.

    ![Valores propostos à lista de frases](./media/luis-add-features/interchangeable.png)

6. Clique em **Salvar**. A lista de frases "Cidades" é adicionada à página **Listas de Frases**.

    ![Lista de frases adicionada](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Editar lista de frases

Clique no nome da lista de frases na página **Listas de frases**. Na caixa de diálogo **Editar Lista de Frases** que é aberta, faça as alterações necessárias na edição e clique em **Salvar**.

 ![Lista de frases adicionada](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Excluir lista de frases 

Clique no botão de reticências (***...***) no final da linha e selecione **Excluir**.

 ![Excluir lista adicionada](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Desativar lista de frases 

Clique no botão de reticências (***...***) no final da linha e selecione **Desativar**.

 ![Desativar lista adicionada](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Recurso padrão (expressão regular) 
**Esse recurso está preterido**. Novos recursos de padrão não podem ser adicionados ao LUIS. Todos os recursos de padrão existentes terão suporte até maio de 2018. Contribua para a expressão regular de LUIS padrão correspondente a um PR ao [repositório Github Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Próximas etapas

Após adicionar, editar, excluir ou desativar uma lista de frases, [treine e teste o aplicativo](luis-interactive-test.md) novamente para verificar se o desempenho melhora.
