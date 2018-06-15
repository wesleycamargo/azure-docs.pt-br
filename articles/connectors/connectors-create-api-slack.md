---
title: Usar o Conector do Slack em seus Aplicativos Lógicos do Azure | Microsoft Docs
description: Conectar-se ao Slack em seus aplicativos lógicos
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 88b134a90ac385ad957d76f420fe85dc2dbbf751
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296216"
---
# <a name="get-started-with-the-slack-connector"></a>Introdução ao conector do Slack
O Slack é uma ferramenta de comunicação da equipe que reúne todas as comunicações de equipe em um só lugar, que pode ser pesquisado instantaneamente e está disponível onde você estiver. 

Comece pela criação de um aplicativo lógico; veja [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Criar uma conexão com o Slack
Para usar o conector do Slack, crie primeiro uma **conexão** e forneça os detalhes dessas propriedades: 

| Propriedade | Obrigatório | DESCRIÇÃO |
| --- | --- | --- |
| A criptografia do token |sim |Fornecer credenciais do Slack |

Entre no Slack usando as seguintes etapas e conclua a configuração da **conexão** do Slack em seu aplicativo lógico:

1. Selecione **Recorrência**
2. Selecione uma **Frequência** e insira um **Intervalo**
3. Selecione **Adicionar uma ação**  
   ![Configurar a Margem de atraso][1]  
4. Insira Margem de atraso na caixa de pesquisa e aguarde até que a pesquisa retorne todas as entradas com Margem de atraso no nome
5. Selecione **Margem de atraso – Postar mensagem**
6. Selecione **Entrar no Slack**:  
   ![Configurar a Margem de atraso][2]
7. Fornecer suas credenciais do Slack para entrar e autorizar o aplicativo    
   ![Configurar a Margem de atraso][3]  
8. Você será redirecionado à página de logon de sua organização. **Autorize** o Slack a interagir com seu aplicativo lógico:      
   ![Configurar a Margem de atraso][5] 
9. Após o término da autorização, você sé redirecionado para seu aplicativo lógico para concluí-lo configurando a seção **Slack - Obter todas as mensagens**. Adicione outros gatilhos e outras ações necessárias.  
   ![Configurar a Margem de atraso][6]
10. Salve seu trabalho selecionando **Salvar** no menu (na parte superior).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/slack/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
