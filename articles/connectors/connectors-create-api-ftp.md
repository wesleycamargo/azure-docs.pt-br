---
title: Conectar ao servidor FTP - Aplicativos Lógicos do Azure | Microsoft Docs
description: Crie, monitore e gerencie arquivos em um servidor FTP com os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: d57a80ec2a1ebfca173d7eaa165de4d344af2ccf
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391106"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando Aplicativos Lógicos do Azure

Com o conector FTP e dos Aplicativos Lógicos do Azure, você pode criar tarefas automatizadas e fluxos de trabalho que criar, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor FTP e disponibilizar a saída para outras ações. Use ações de execução nos aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode ter outro uso de ações para usar a saída das ações do FTP. Por exemplo, se você obter arquivos regularmente do servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou do Outlook.com. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> O conector de FTP dá suporte a apenas os arquivos que têm 50 MB ou menos, a menos que você use o [agrupamento para tratar mensagens grandes](../logic-apps/logic-apps-handle-large-messages.md). 
>
> Além disso, o conector do FTP dá suporte a apenas FTP explícito sobre SSL (FTPS) e não é compatível com FTPS implícito. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Seu endereço de servidor de host FTP e credenciais de conta

  O conector de FTP exige que o servidor FTP seja acessível da internet e configure para operar no modo *passivo*. Suas credenciais permitem que o aplicativo lógico crie uma conexão e acesse sua conta do FTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta FTP. Para iniciar com um gatilho de FTP [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação FTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-ftp"></a>Conectar-se ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**e, em seguida, selecione **Adicionar uma ação**. 
   Na caixa de pesquisa, insira “ftp” como filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho do FTP: Quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico ao detectar quando um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deve obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta no servidor SFTP. 

**Exemplo corporativo**: Use esse gatilho para monitorar uma pasta do FTP em busca de novos arquivos que descrevem os pedidos de clientes. Você pode, então, usar uma ação de FTP como **Obter conteúdo do arquivo**, para que possa obter o conteúdo da ordem para mais processamento e armazenar essa ordem em um banco de dados de ordens.

Um aplicativo lógico válido e funcional requer um gatilho e pelo menos uma ação. Portanto, certifique-se de adicionar uma ação depois de adicionar um gatilho.

Este é um exemplo que mostra esse gatilho: **Quando um arquivo é adicionado ou modificado**

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é adicionado ou modificado – FTP**

   ![Localize e selecione o gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. 
   Para transferir arquivos no formato binário, por exemplo, nos casos em que a codificação é usada, selecione **Transporte Binário**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Ao lado da **Pasta**, escolha o ícone da pasta, para que a lista apareça. Para localizar a pasta que você deseja monitorar arquivos novos ou editados, selecione a seta de ângulo direito (**>**), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localize e selecione a pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder.png)  

   Sua pasta selecionada aparece na caixa **Pasta**.

   ![Pasta selecionada](./media/connectors-create-api-ftp/selected-folder.png)  

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Neste exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

### <a name="ftp-action-get-content"></a>Ação do FTP: obter conteúdo

Esta ação obtém o conteúdo de um arquivo em um servidor FTP quando esse arquivo é adicionado ou atualizado. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma ação que obtém o conteúdo do arquivo depois que esse arquivo é adicionado ou editado. 

1. No gatilho ou quaisquer outras ações, escolha **Nova etapa**. 

1. Na caixa de pesquisa, insira “ftp” como filtro. Na lista de ações, selecione esta ação: **Obter conteúdo do arquivo – FTP**

   ![Selecionar ação FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se você já tiver uma conexão ao seu servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, escolha **Criar**. 

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Após a ação **Obter conteúdo do arquivo** abrir, clique dentro da caixa do **Arquivo** para que apareça na lista de conteúdo dinâmica. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, selecione a propriedade **Conteúdo do Arquivo**, que tem o conteúdo do arquivo adicionado ou atualizado.  

   ![Localize e selecione o arquivo](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A propriedade do **Conteúdo do Arquivo** aparece agora na caixa **Arquivo**.

   ![Propriedade "Conteúdo do arquivo" selecionada](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Salve seu aplicativo lógico. Para testar seu fluxo de trabalho, adicione um arquivo para a pasta FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/ftpconnector/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)