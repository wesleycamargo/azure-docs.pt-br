---
title: Conectar a sistemas de arquivos locais – Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que se conectam a sistemas de arquivos locais com o conector do sistema de arquivos por meio do gateway de dados local no Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 0c30ffec58b1542fa80cf0c9873a0e6df8641104
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232538"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Conectar a sistemas de arquivos locais com o Aplicativo Lógico do Azure

Com o conector do sistema de arquivos e o Aplicativo Lógico do Azure, você pode criar tarefas e fluxos de trabalho automatizados que criam e gerenciam arquivos em um compartilhamento de arquivos local, por exemplo:  

- Criar, obter, acrescentar, atualizar e excluir arquivos
- Listar arquivos em pastas ou pastas raiz.
- Obter conteúdo e metadados do arquivo.

Este artigo mostra como se conectar a um sistema de arquivos local como descrito por este cenário de exemplo: copiar um arquivo carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email. Para se conectar e acessar sistemas locais com segurança, os aplicativos lógicos usam o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Antes de poder conectar aplicativos lógicos a sistemas locais, como seu servidor do sistema de arquivos, você precisará [instalar e configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode especificar para usar sua instalação de gateway quando criar a conexão do sistema de arquivos do seu aplicativo lógico.

* Uma [conta do Dropbox](https://www.dropbox.com/) e suas credenciais de usuário

  Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Dropbox. 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na caixa de pesquisa, insira "dropbox" como o seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é criado** 

   ![Selecionar o gatilho do Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para o Aplicativo Lógico do Azure. 

1. Forneça as informações necessárias para o gatilho.

   ![Gatilho do Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. No gatilho, escolha **Próxima etapa**. Na caixa de pesquisa, insira "sistema de arquivos" como filtro. Na lista de ações, selecione esta ação: **Criar arquivo – sistema de arquivos**

   ![Localizar o conector do sistema de arquivos](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se você ainda não tiver uma conexão com seu sistema de arquivos, receberá uma solicitação para criar essa conexão.

   ![Criar conexão](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Obrigatório | Valor | DESCRIÇÃO | 
   | -------- | -------- | ----- | ----------- | 
   | **Nome da Conexão** | SIM | <*connection-name*> | O nome desejado para a conexão | 
   | **Pasta raiz** | SIM | <*root-folder-name*> | A pasta raiz do sistema de arquivos, como uma pasta local no computador em que o gateway de dados local está instalado ou a pasta de um compartilhamento de rede que o computador pode acessar. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta raiz é a pasta pai principal, que é usada para caminhos relativos de todas as ações relacionadas ao arquivo. | 
   | **Tipo de autenticação** | Não  | <*auth-type*> | O tipo de autenticação usado pelo seu sistema de arquivos, por exemplo, **Windows** | 
   | **Nome de Usuário** | SIM | <*domain*>\\<*username*> | O nome de usuário do seu gateway de dados instalado anteriormente | 
   | **Senha** | SIM | <*your-password*> | A senha do seu gateway de dados instalado anteriormente | 
   | **gateway** | SIM | <*installed-gateway-name*> | O nome do seu gateway instalado anteriormente | 
   ||| 

1. Quando terminar, escolha **Criar**. 

   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto. 
   Se a conexão estiver configurada de forma correta, você verá opções para a ação selecionada anteriormente. 

1. Na ação **Criar arquivo**, forneça os detalhes para copiar os arquivos do Dropbox para a pasta raiz no compartilhamento de arquivos local. Para adicionar saídas de etapas anteriores, clique dentro das caixas e selecione os campos disponíveis quando a lista de conteúdo dinâmico for exibida.

   ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um email para que os usuários apropriados saibam sobre o novo arquivo. Insira os destinatários, o título e o corpo do email. Para testar, você pode usar seu próprio endereço de email.

   ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

1. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox. 

   Seu aplicativo lógico deve copiar o arquivo para o compartilhamento de arquivos local e enviar aos destinatários um email sobre o arquivo copiado.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/fileconnector/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site do User Voice dos Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [se conectar a dados locais](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
