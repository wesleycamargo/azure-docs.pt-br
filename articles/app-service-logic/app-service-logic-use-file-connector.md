---
title: "Usar o conector do sistema de arquivos com o gateway de dados local em Aplicativos Lógicos | Microsoft Docs"
description: "Os Aplicativos Lógicos permitem que você se conecte facilmente ao sistema de arquivos local como parte do fluxo de trabalho."
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 9b2ae8a06cfa0cf686b7cae50d3b6903e536ee73
ms.openlocfilehash: fc81b6ca60af48ddfd8862d5f6620db537646ae7


---
# <a name="use-file-system-connector-with-on-premises-data-gateway"></a>Usar o conector do sistema de arquivos com o gateway de dados local

A conectividade nuvem híbrida é a base dos Aplicativos Lógicos. O gateway de dados local permite gerenciar dados e acessar com segurança recursos locais usando os Aplicativos Lógicos. Neste artigo, demonstraremos como se conectar a um sistema de arquivos local em um cenário simples: copiar um arquivo que é carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email.

## <a name="prerequisites"></a>Pré-requisitos
- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/en-us/download/details.aspx?id=53127).

 Instale o gateway de dados local mais recente, versão 1.15.6150.1 ou superior, caso ainda não o tenha feito. As instruções podem ser encontradas [neste artigo](http://aka.ms/logicapps-gateway). O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

## <a name="use-file-system-connector"></a>Usar o conector do sistema de arquivos

1. Vamos criar um gatilho do Dropbox, "Quando um arquivo é criado". Assim, obter um resumo de todas as ações compatíveis com o conector de arquivos é tão simples quanto digitar "Sistema de arquivos" na pesquisa.

 ![Procurar conector de arquivo](./media/app-service-logic-use-file-connector/search-file-connector.png)

2. Escolha "Criar arquivo" e crie uma conexão para ele.
 - Caso não tenha uma conexão existente, será solicitado que você crie uma.
 - Marque a opção "Conectar via gateway de dados local". Você verá campos adicionais que são mostrados assim que a caixa de seleção é marcada.
 - Especifique a pasta raiz, que pode ser uma pasta local no computador em que o gateway de dados local está instalado, ou um compartilhamento de rede ao qual o computador tem acesso.
 - Insira o nome de usuário e a senha para o gateway.
 - Selecione o gateway instalado na etapa anterior.
    
 > [!NOTE]
 > A pasta raiz é a pasta pai principal, que será usada para caminhos relativos de todas as ações relacionadas ao arquivo.

 ![Configurar conexão](./media/app-service-logic-use-file-connector/create-file.png)

3. Depois de fornecer todos os detalhes, clique em "Criar". Os Aplicativos Lógicos vão configurar e testar a conexão para garantir que ela esteja funcionando corretamente. Se tudo for verificado, você verá as opções do cartão selecionado anteriormente, e o conector de arquivo agora está pronto para uso.

4. Copie o arquivo carregado no Dropbox na pasta raiz do compartilhamento de arquivos localizado no local.

 ![Ação Criar arquivo](./media/app-service-logic-use-file-connector/create-file-filled.png)

5. Depois que o arquivo for copiado, vamos enviar um email para que usuários relevantes saibam sobre ele. Assim como acontece nos outros conectores, a saída das ações anteriores estará disponível no seletor de "conteúdo dinâmico".
 - Especifique os destinatários, o título e o corpo do email. Use o seletor de "conteúdo dinâmico" para escolher a saída do conector de arquivo de modo a incrementar o email.

 ![Ação Enviar email](./media/app-service-logic-use-file-connector/send-email.png)

6. Salve o Aplicativo Lógico e teste-o carregando um arquivo no Dropbox. Você deve ver o arquivo sendo copiado no compartilhamento de arquivos local e receber uma notificação por email sobre a operação.
 > [!TIP]
 > Confira como [monitorar seus Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md).

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
- Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md). 
- Criar uma [conexão local](app-service-logic-gateway-connection.md) com Aplicativos Lógicos.



<!--HONumber=Nov16_HO3-->


