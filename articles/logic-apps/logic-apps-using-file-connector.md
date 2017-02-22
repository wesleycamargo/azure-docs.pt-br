---
title: "Conectar-se a uma pasta de sistema de arquivos local nos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Usar o gateway de dados local para se conectar a um sistema de arquivos local em seu fluxo de trabalho de aplicativo lógico"
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 336e3f2bba2fe3bb7abdd6959354349ff61ed6a8
ms.openlocfilehash: 3bbcb5e9c7a4731eb8eb410a94fddb93b2ea8416


---
# <a name="use-the-file-system-connector-with-the-on-premises-data-gateway-in-a-logic-app"></a>Usar o conector do sistema de arquivos com o gateway de dados local em um aplicativo lógico

A conectividade nuvem híbrida é a base dos Aplicativos Lógicos. O gateway de dados local permite gerenciar dados e acessar com segurança recursos locais usando os Aplicativos Lógicos. Neste artigo, demonstraremos como se conectar a um sistema de arquivos local em um cenário simples: copiar um arquivo que é carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email.

## <a name="prerequisites"></a>Pré-requisitos
- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/en-us/download/details.aspx?id=53127).
- Instale o gateway de dados local mais recente, versão 1.15.6150.1 ou superior. [Conectar-se ao gateway de dados local](http://aka.ms/logicapps-gateway) lista as etapas. O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

## <a name="use-file-system-connector"></a>Use o conector do Sistema de Arquivos

1. Vamos criar um gatilho do Dropbox, "Quando um arquivo é criado". Assim, obter um resumo de todas as ações compatíveis com o conector de arquivos é tão simples quanto digitar "Sistema de arquivos" na pesquisa.

 ![Procurar conector de arquivo](media/logic-apps-using-file-connector/search-file-connector.png)

2. Escolha "Criar arquivo" e crie uma conexão para ele.
 - Caso não tenha uma conexão existente, é solicitado que você crie uma.
 - Marque a opção “Conectar por meio do gateway de dados local”. Outras propriedades são exibidas.
 - Selecione a pasta raiz. A pasta raiz pode ser uma pasta local no computador em que o gateway de dados local está instalado, ou um compartilhamento de rede ao qual o computador tem acesso.
 - Insira o nome de usuário e a senha para o gateway.
 - Selecione o gateway instalado na etapa anterior.
    
 > [!NOTE]
 > A pasta raiz é a pasta pai principal, que será usada para caminhos relativos de todas as ações relacionadas ao arquivo.

 ![Configurar conexão](media/logic-apps-using-file-connector/create-file.png)

3. Depois de fornecer todos os detalhes, clique em "Criar". Os Aplicativos Lógicos configuram e testam a conexão para garantir que ela esteja funcionando corretamente. Se tudo der certo, você verá opções para o cartão selecionado anteriormente. O conector do sistema de arquivos está pronto para uso.

4. Copie o arquivo carregado no Dropbox na pasta raiz do compartilhamento de arquivos localizado no local.

 ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

5. Depois que o arquivo for copiado, vamos enviar um email para que usuários relevantes saibam sobre ele. Assim como acontece nos outros conectores, a saída das ações anteriores estará disponível no seletor de "conteúdo dinâmico".
 - Insira os destinatários, o título e o corpo do email. Use o seletor de "conteúdo dinâmico" para escolher a saída do conector de arquivo de modo a incrementar o email.

 ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

6. Salve o Aplicativo Lógico e teste-o carregando um arquivo no Dropbox. Você deve ver o arquivo sendo copiado no compartilhamento de arquivos local e receber uma notificação por email sobre a operação.

    > [!TIP] 
    > Confira como [monitorar seus Aplicativos Lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

7. Pronto! Agora você tem um Aplicativo Lógico funcional usando o conector do sistema de arquivos. Você pode começar explorando outras funcionalidades que ele oferece:

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

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Criar uma [conexão local](../logic-apps/logic-apps-gateway-connection.md) com Aplicativos Lógicos.



<!--HONumber=Jan17_HO5-->


