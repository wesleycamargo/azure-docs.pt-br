---
title: Como criar uma base de dados de conhecimento - QnA Maker - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Como criar uma base de dados de conhecimento
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364869"
---
# <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

O QnA Maker simplifica o uso das fontes de dados existentes para criar uma base de dados de conhecimento. É possível criar uma nova base de dados de conhecimento do QnA Maker a partir de páginas de perguntas frequentes, manuais de produtos, documentos estruturados ou adicionando editorialmente.

## <a name="steps"></a>Etapas

1. Para começar, entre no [portal do QnA Maker](https://qnamaker.ai) com suas credenciais do Azure e clique em **Criar novo serviço**.

    ![Criar uma base de dados de conhecimento ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Se você ainda não criou um serviço do QnA Maker, selecione **Criar um serviço do QnA**. De outro modo, escolha um serviço do QnA Maker nos menus suspensos da Etapa 2. Selecione o serviço do QnA Maker que hospedará a Base de Dados de Conhecimento.

    ![Configurar serviço do QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Insira as informações a seguir para criar a base de dados de conhecimento.

    ![Definir fontes de dados](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Forneça um **nome** ao serviço. Nomes duplicados e caracteres especiais têm suporte.
    - Cole as URLs das quais serão extraídas. Consulte mais informações sobre os tipos de fontes com suporte [aqui](../Concepts/data-sources-supported.md).
    - Como alternativa, carregue arquivos dos quais os dados são extraídos. Consulte as [informações sobre preços](https://aka.ms/qnamaker-pricing
) para ver quantos documentos é possível adicionar.
    - Se quiser adicionar QnAs manualmente, você poderá ignorar os arquivos de vinculação.

4. Selecione **Criar**.

    ![Criar uma base de dados de conhecimento](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Demora alguns minutos para os dados serem extraídos.

    ![Extração](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Se a Base de Dados de Conhecimento for criada com êxito, você será redirecionado para a página **Base de Dados de Conhecimento**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
