---
title: Adicionar um bate-papo a uma base de dados de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar um bate-papo pessoal ao seu bot o torna mais comunicativo e interessante ao criar uma KB. O QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 7c54628faba4a3d7a89c1944e2f434a83035707b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081755"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar bate-papo à base de dados de conhecimento

Adicionar bate-papo ao seu bot o torna mais comunicativo e interessante. O recurso de bate-papo do QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB (base de dados de conhecimento). Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero.  

Esse conjunto de dados contém cerca de 100 cenários de bate-papo na voz de três personas – o profissional, o amigo e o comediante. Escolha a persona que mais se assemelha à voz do seu bot. Dada uma consulta de usuário, o QnA Maker tenta correspondê-la às perguntas e respostas do bate-papo conhecido mais próximo. 

Veja alguns exemplos das diferentes personalidades: <!-- added quotes so acrolinx doesn't score these sentences -->
|Consulta do usuário|O profissional|O amigo|O comediante|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Aw, that's sweet.`|`Sure. Take me to city hall. See what happens.`|


> [!NOTE]
> No momento, o bate-papo está disponível apenas em inglês. 

## <a name="add-chit-chat-during-kb-creation"></a>Adicionar bate-papo durante a criação da KB
Durante a criação da base de dados de conhecimento, depois de adicionar suas URLs e arquivos de origem, há uma opção para adicionar um bate-papo. Escolha a personalidade que você deseja como sua base de bate-papo. Se você não quiser adicionar um bate-papo ou se já tiver um bate-papo compatível nas suas fontes de dados, escolha **Nenhum**. 
   
![Adicionar bate-papo durante a criação](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar bate-papo a uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Há um link para todos os conjuntos de dados de bate-papo no formato **.tsv** apropriado. Baixe a personalidade que você desejar e, em seguida, carregue-a como uma fonte de arquivo. Não edite o formato ou os metadados ao baixar e carregar o arquivo. 
  
![Adicionar bate-papo a uma KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar as perguntas e respostas do seu bate-papo
Ao editar sua KB, você verá uma nova fonte para bate-papo, com base na personalidade que você selecionou. Agora é possível adicionar perguntas alteradas ou editar as respostas, assim como com qualquer outra fonte. 

![Editar as perguntas e respostas do bate-papo](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicionar perguntas e respostas adicionais ao bate-papo
É possível adicionar novas perguntas e respostas ao bate-papo que não estão presentes no conjunto predefinido. Verifique se você não está duplicando um par de pergunta e resposta já abordado no conjunto de bate-papo. Ao adicionar novas perguntas e respostas ao bate-papo, elas são adicionadas à fonte **Editorial**. Para garantir que o classificador compreenda que se trata de bate-papo, adicione o par chave-valor de metadados "Editorial: chit-chat", como mostrado na imagem a seguir:
   
![Adicionar perguntas e respostas ao bate-papo](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Excluir o bate-papo de uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Sua fonte de bate-papo é listada como um arquivo, com o nome de personalidade selecionada. Você pode excluí-lo como um arquivo de origem.

![Excluir o bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também 

[Visão geral do QnA Maker](../Overview/overview.md)
