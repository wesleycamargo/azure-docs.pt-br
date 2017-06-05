---
title: "Conectar a sistemas de arquivos locais por meio do Aplicativo Lógico do Azure | Microsoft Docs"
description: "Conectar a sistemas de arquivos locais no fluxo de trabalho dos aplicativos lógicos por meio do gateway de dados local e do conector do Sistema de Arquivos"
keywords: sistemas de arquivos
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3faa238b882c88f1a0eb59a4e7a2f8bb6d6e6b9b
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Conectar a sistemas de arquivos locais em aplicativos lógicos com o conector do Sistema de Arquivos

A conectividade de nuvem híbrida é central para aplicativos lógicos e, portanto, para gerenciar dados e acessar os recursos locais com segurança, os aplicativos lógicos podem usar o gateway de dados local. Neste artigo, mostramos como se conectar a um sistema de arquivos local em um cenário básico: copiar um arquivo que é carregado no Dropbox para um compartilhamento de arquivos e, depois, enviar um email.

## <a name="prerequisites"></a>Pré-requisitos

- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127).
- Instale o gateway de dados local mais recente, versão 1.15.6150.1 ou superior. [Conectar-se ao gateway de dados local](http://aka.ms/logicapps-gateway) lista as etapas. O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Adicionar um gatilho e ações para conectar ao sistema de arquivos

1. Crie um aplicativo lógico e adicione este gatilho do Dropbox: **Quando um arquivo é criado** 
2. No gatilho, escolha **Próxima Etapa** > **Adicionar uma ação**. 
3. Na caixa de pesquisa, insira `file system` de forma que você possa exibir todas as ações com suporte no conector do Sistema de Arquivos.

   ![Procurar conector de arquivo](media/logic-apps-using-file-connector/search-file-connector.png)

2. Escolha a ação **Criar arquivo** e crie uma conexão com o sistema de arquivos.

   Caso não tenha uma conexão existente, é solicitado que você crie uma.

   1. Escolha **Conectar por meio do gateway de dados local**. Outras propriedades serão exibidas.
   2. Selecione a pasta raiz do sistema de arquivos.
      
       > [!NOTE]
       > A pasta raiz é a pasta pai principal, que é usada para caminhos relativos de todas as ações relacionadas ao arquivo. É possível especificar uma pasta local no computador em que o gateway de dados local está instalado ou a pasta pode ser um compartilhamento de rede que pode ser acessado pelo computador.

   3. Insira o nome de usuário e a senha do gateway.
   4. Selecione o gateway instalado anteriormente.

       ![Configurar conexão](media/logic-apps-using-file-connector/create-file.png)

3. Depois de fornecer todos os detalhes, escolha **Criar**. 

   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto. 
   Se a conexão estiver configurada de forma correta, você verá opções para a ação selecionada anteriormente. 
   O conector do sistema de arquivos está pronto para uso.

4. Especifique que você deseja copiar os arquivos do Dropbox para a pasta raiz do compartilhamento de arquivos local.

   ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

5. Depois que o aplicativo lógico copiar o arquivo, adicione uma ação do Outlook que envia um email para que os usuários relevantes fiquem cientes do novo arquivo. Insira os destinatários, o título e o corpo do email. 

   No seletor de conteúdo dinâmico, é possível escolher saídas de dados do conector de arquivo, de forma que você possa adicionar mais detalhes ao email.

   ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

6. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox. O arquivo deve ser copiado para o compartilhamento de arquivos local e você deverá receber um email sobre a operação.

   > [!TIP] 
   > Saiba como [monitorar os aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns! Agora você tem um aplicativo lógico em operação que pode se conectar ao sistema de arquivos local. Tente explorar outras funcionalidades oferecidas pelo conector, por exemplo:

- Criar arquivo
- Lista de arquivos na pasta
- Acrescentar arquivo
- Excluir arquivo
- Obter conteúdo do arquivo
- Obter o conteúdo do arquivo usando o caminho
- Obter Metadados do Arquivo
- Obter Metadados do Arquivo usando o caminho
- Lista de arquivos na pasta-raiz
- Atualizar arquivo

## <a name="view-the-swagger"></a>Exibir o Swagger
Consulte os [detalhes do Swagger](/connectors/fileconnector/). 

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se a dados locais](../logic-apps/logic-apps-gateway-connection.md) por meio de aplicativos lógicos
- Saiba mais sobre a [integração de empresas](../logic-apps/logic-apps-enterprise-integration-overview.md)

