---
title: Conectar-se ao Dropbox - aplicativos lógicos do Azure
description: Carregar e gerenciar arquivos com as APIs REST do Dropbox e os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312520"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Carregar e gerenciar arquivos no Dropbox usando aplicativos lógicos do Azure

Com o conector do Dropbox e aplicativos lógicos do Azure, você pode criar fluxos de trabalho automatizados que carregar e gerenciam arquivos em sua conta do Dropbox. 

Este artigo mostra como conectar-se ao Dropbox de seu aplicativo lógico e, em seguida, adicionar o Dropbox **quando um arquivo é criado** disparador e o Dropbox **obter conteúdo do arquivo usando caminho** ação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Um [conta do Dropbox](https://www.dropbox.com/), que você pode se inscrever gratuitamente. Suas credenciais de conta são necessárias para a criação de uma conexão entre seu aplicativo lógico e sua conta do Dropbox.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é criado**

   ![Selecionar o gatilho do Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para os Aplicativos Lógicos do Azure.

1. Forneça as informações necessárias para o gatilho. 

   Neste exemplo, selecione a pasta onde você deseja controlar a criação do arquivo. Para procurar as pastas, escolha o ícone de pasta ao lado de **pasta** caixa.

## <a name="add-action"></a>Adicionar ação

Agora, adicione uma ação que obtém o conteúdo de qualquer arquivo novo.

1. No gatilho, escolha **Próxima etapa**. 

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista de ações, selecione esta ação: **Obter o conteúdo do arquivo usando o caminho**

1. Se você já não tiver autorizado a aplicativos de lógicos do Azure para acessar o Dropbox, autorize o acesso agora.

1. Para navegar até o caminho do arquivo que você deseja usar, ao lado de **caminho do arquivo** , escolha as reticências (**...** ) botão. 

   Você também pode clicar dentro de **caminho do arquivo** caixa e, em seguida, na lista de conteúdo dinâmica, selecione **caminho do arquivo**, cujo valor está disponível como saída do gatilho que você adicionou na seção anterior.

1. Quando terminar, salve o aplicativo lógico.

1. Para disparar seu aplicativo lógico, crie um novo arquivo no Dropbox.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente conhecido como Swagger) de arquivos, consulte o [página de referência do conector](/connectors/dropbox/).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
