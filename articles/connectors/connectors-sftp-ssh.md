---
title: Conectar-se à conta do SFTP com o Aplicativo Lógico do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP usando o Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: 2250c6952aeac7b10dcb1a1a35419941e5cad507
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233201"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Monitorar, criar e gerenciar arquivos SFTP usando os Aplicativo Lógico do Azure e o conector SFTP-SSH

Com os Aplicativos Lógicos do Azure e o conector SFTP-SSH, você pode criar tarefas e fluxos de trabalho automatizados que monitoram, criam, enviam e recebem arquivos por meio de sua conta em um servidor [SFTP](https://www.ssh.com/ssh/sftp/), juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, renomear, atualizar, listar e excluir arquivos.
* Criar pasta.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor SFTP e disponibilizar a saída para outras ações. Você pode usar ações nos aplicativos lógicos para executar tarefas com arquivos no seu servidor SFTP. Você também pode fazer com que outras ações usem a saída das ações de SFTP. Por exemplo, se você regularmente recuperar arquivos do seu servidor SFTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou Outlook.com.
Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH versus SFTP

Aqui estão algumas principais diferenças entre o conector SFTP-SSH e o conector [SFTP](../connectors/connectors-create-api-sftp.md). O conector SFTP-SSH fornece estes recursos:

* Usa a biblioteca <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a>, que é uma biblioteca SSH (Secure Shell) de software livre para .NET.

* Fornece suporte para arquivos grandes **1 GB**. O conector pode ler ou gravar arquivos de até 1 GB de tamanho.

* Fornece a ação **Criar pasta**, que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **Renomear arquivo**, que renomeia um arquivo no servidor SFTP.

* Armazena em cache a conexão com o servidor SFTP, o que melhora o desempenho e reduz o número de tentativas de conexão no servidor. 

  Você pode controlar a duração usada para armazenar a conexão em cache configurando a propriedade <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> em seu servidor SFTP. 

## <a name="how-trigger-polling-works"></a>Como a sondagem de gatilho funciona

Os gatilhos de SFTP-SSH funcionam sondando o sistema de arquivos SFTP e procurando por arquivos alterados desde a última sondagem. Algumas ferramentas permitem que você preserve o carimbo de data/hora quando os arquivos são alterados, portanto, nesses casos, você precisa desabilitar esse recurso para que o gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação | 
|-------------|--------| 
| WinSCP | Opções → Preferências… → Transferir → Editar… → Preservar carimbo de data/hora → Desabilitar |
| FileZilla | Transferir → Preservar carimbo de data/hora de arquivos transferidos → Desabilitar | 
||| 

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho. 

Ao solicitar o conteúdo do arquivo, o gatilho não recupera arquivos com mais de 50 MB. Para selecionar arquivos com mais de 50 MB, siga este padrão:

* Use um gatilho que retorna propriedades de arquivo, como **Quando um arquivo é adicionado ou modificado (somente propriedades)**. 
* Siga o gatilho com uma ação que lê o arquivo completo, como **Obter conteúdo do arquivo usando caminho**.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* As credenciais da conta e o endereço do servidor host SFTP, que autoriza o aplicativo lógico para criar uma conexão e acessar sua conta SFTP.

  Copie e cole o conteúdo completo da chave privada SSH na propriedade **Chave privada SSH** seguindo o formato multilinha. 
  Aqui estão as etapas de exemplo que mostram como fornecer a chave privada SSH usando o Notepad.exe:
    
  1. Abrir o arquivo de chave privada SSH no Notepad.exe
  2. No menu **Editar**, selecione **Selecionar Tudo**.
  3. Selecione **Editar** > **Copiar**.
  4. Ao criar a conexão, na propriedade **Chave privada SSH**, cole a chave. Não edite manualmente a propriedade **Chave privada SSH**.

     O conector usa a biblioteca SSH.NET, que dá suporte a esses formatos de chave privada SSH e somente a impressão digital MD5:

     * RSA 
     * DSA

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

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho SFTP: quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico ao detectar quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deve obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta no servidor SFTP. 

**Exemplo corporativo**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Você pode, então, usar uma ação de SFTP como **Obter conteúdo do arquivo**, para que possa obter o conteúdo da ordem para mais processamento e armazenar essa ordem em um banco de dados de ordens.

### <a name="sftp-action-get-content"></a>Ação de SFTP: obter o conteúdo

Esta ação obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada. 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/sftpconnector/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)