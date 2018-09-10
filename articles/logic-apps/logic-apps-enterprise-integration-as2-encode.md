---
title: Codificar mensagens AS2 – Aplicativo Lógico do Azure | Microsoft Docs
description: Codificar mensagens AS com o Aplicativo Lógico do Azure e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 6bb19199929a004ee5668a3a6e057a69c24dd752
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122706"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar mensagens AS2 com o Aplicativo Lógico do Azure e o Enterprise Integration Pack

Para estabelecer confiabilidade e segurança ao transmitir mensagens, use o conector de mensagem AS2 de codificação. Ele fornece a assinatura digital, criptografia e confirmações por meio de Notificações de Disposição de Mensagem (MDN), o que também leva a suporte para Não Repúdio.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem AS2 de codificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato AS2](logic-apps-enterprise-integration-as2.md) que já está definido em sua conta de integração

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. [Criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem AS2 de codificação não possui gatilhos. Você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.  Na caixa de pesquisa, digite "AS2" como filtro. Selecione **AS2 - Codificar Mensagem AS2**.
   
    ![Procure "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 
   
    ![criar conexão com a conta de integração](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.
   
    ![detalhes de conexão de integração](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Depois que a conexão é criada, conforme mostrado neste exemplo, forneça detalhes para **os identificadores AS2-De**, **AS2-Para** conforme configurado no seu contrato e **Corpo**, que é a carga da mensagem.
   
    ![fornecer campos obrigatórios](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Detalhes do codificador AS2

O conector codificador AS2 executa as seguintes tarefas: 

* Aplica cabeçalhos HTTP/AS2
* Sinaliza mensagens de saída (se configurado)
* Criptografa mensagens de saída (se configurado)
* Compacta as mensagens (se configurado)
* Nome do arquivo de transmissão no cabeçalho MIME (se configurado)


  > [!NOTE]
  > Se você usar o Azure Key Vault para o gerenciamento de certificado, certifique-se de configurar as chaves para permitir a operação **Criptografar**.
  > Caso contrário, a Codificação de AS2 falhará.
  >
  > ![Descriptografias de cofre de chaves](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Experimente este exemplo

Para tentar implantar um aplicativo lógico totalmente operacional e o cenário de AS2 de exemplo, confira o[modelo e cenário de aplicativo lógico AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Exibir o swagger
Consulte os [detalhes do Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 

