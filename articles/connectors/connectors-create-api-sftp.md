---
title: Conectar-se à conta do SFTP - Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize tarefas e processos que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP por meio do SSH usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/26/2018
ms.openlocfilehash: 5d328164ac8ad99db15a12d850327615a9ffd809
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910277"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorar, criar e gerenciar recursos do SFTP usando os Aplicativos Lógicos do Azure

Para automatizar tarefas que monitoram, criam, enviam e recebem arquivos em um servidor [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/), você pode criar e automatizar fluxos de trabalho de integração usando os Aplicativos Lógicos do Azure e o conector SFTP. O SFTP é um protocolo de rede que fornece acesso a arquivos, transferência de arquivos e gerenciamento de arquivos em qualquer fluxo de dados confiável. Aqui estão algumas tarefas de exemplo que você pode automatizar: 

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Comparado ao [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md), o conector SFTP pode ler ou gravar arquivos de até 50 MB, a menos que você use o [agrupamento de mensagens nas ações](../logic-apps/logic-apps-handle-large-messages.md). Atualmente, não é possível usar o agrupamento para gatilhos. Para arquivos de até 1 GB, use o conector [SFTP-SSH](../connectors/connectors-sftp-ssh.md). Para arquivos maiores que 1 GB, é possível usar o conector SFTP-SSH mais o [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). 

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e disponibilizam a saída para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usando a saída das ações do SFTP. Por exemplo, se você recuperar regularmente arquivos do servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Office 365 Outlook ou o conector Outlook.com.
Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Seu endereço de servidor SFTP e credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Para usar o protocolo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), você também precisa ter acesso a uma chave privada SSH e à senha da chave privada SSH. 

  > [!NOTE]
  > 
  > O conector SFTP suporta estes formatos de chave privada: OpenSSH, ssh.com e PuTTY
  > 
  > Ao criar seu aplicativo lógico, depois de adicionar o acionador ou a ação SFTP desejada, você precisará fornecer informações de conexão para o servidor SFTP. 
  > Se você estiver usando uma chave privada SSH, certifique-se de ***copiar*** a chave do arquivo de chave privada SSH e ***colar*** essa chave nos detalhes da conexão, ***não insira ou edite manualmente a chave***, o que pode causar falha na conexão. 
  > Para mais informações, consulte as etapas posteriores neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta SFTP. Para iniciar com um gatilho SFTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de SFTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-sftp"></a>Conectar-se ao SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "sftp" como o filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação, escolha **Nova etapa**. 
   Na caixa de pesquisa, insira "sftp" como o seu filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT] 
   >
   > Ao inserir sua chave privada SSH na propriedade **chave privada SSH**, siga estas etapas adicionais, que ajudam a fornecer o valor completo e correto para essa propriedade. 
   > Uma chave inválida faz com que a conexão falhe.
   
   Embora você possa usar qualquer editor de texto, aqui estão etapas de exemplo que mostram como copiar e colar corretamente a chave usando o Notepad.exe como exemplo.
    
   1. Abra seu arquivo de chave privada SSH em um editor de texto. 
   Essas etapas usam o bloco de notas do exemplo.

   1. Do bloco de notas **edite** menu, selecione **Selecionar tudo**.

   1. Selecione **Editar** > **Copiar**.

   1. No gatilho ou ação SFTP que você adicionou, cole a chave *completa* que você copiou na propriedade **SSH private key**, que suporta várias linhas. 
   ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando você terminar inserindo os detalhes de conexão, escolha **criar**.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="trigger-limits"></a>Limites de gatilho

O SFTP aciona o trabalho pesquisando o sistema de arquivos SFTP e procurando por qualquer arquivo que tenha sido alterado desde a última pesquisa. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação | 
|-------------|--------| 
| WinSCP | Vá para **opções** > **preferências** > **transferir** > **editar**  >  **Preservar o carimbo de hora** > **desabilitar** |
| FileZilla | Ir para **Transferir** > **Preservar registros de data e hora de arquivos transferidos** > **Desativar** | 
||| 

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho. 

Ao solicitar o conteúdo do arquivo, os gatilhos não obtêm arquivos com mais de 50 MB. Para obter arquivos maiores que 50 MB, siga este padrão: 

* Use um gatilho que retorna propriedades de arquivo, como **Quando um arquivo é adicionado ou modificado (somente propriedades)**.

* Siga o gatilho com uma ação que leia o arquivo completo, como **Obter conteúdo do arquivo usando caminho** e faça a ação usar o [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Exemplos

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho do SFTP: Quando um arquivo é adicionado ou modificado

Esse acionador inicia um fluxo de trabalho de aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP. 

**Exemplo corporativo**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Em seguida, você pode usar uma ação de SFTP, como **Obter conteúdo de arquivo**, para obter o conteúdo do pedido para processamento posterior e armazenar esse pedido em um banco de dados de pedidos.

Ao solicitar o conteúdo do arquivo, os gatilhos não obtêm arquivos com mais de 50 MB. Para obter arquivos maiores que 50 MB, siga este padrão: 

* Use um gatilho que retorna propriedades de arquivo, como **Quando um arquivo é adicionado ou modificado (somente propriedades)**.

* Siga o gatilho com uma ação que leia o arquivo completo, como **Obter conteúdo do arquivo usando caminho** e faça a ação usar o [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Ação de SFTP: obter conteúdo

Esta ação obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada. 

Ao solicitar o conteúdo do arquivo, os gatilhos não obtêm arquivos com mais de 50 MB. Para obter arquivos maiores que 50 MB, siga este padrão: 

* Use um gatilho que retorna propriedades de arquivo, como **Quando um arquivo é adicionado ou modificado (somente propriedades)**.

* Siga o gatilho com uma ação que leia o arquivo completo, como **Obter conteúdo do arquivo usando caminho** e faça a ação usar o [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/sftpconnector/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
