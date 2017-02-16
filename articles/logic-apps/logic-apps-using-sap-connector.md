---
title: "Usar o Conector SAP com o gateway de dados local no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Os Aplicativos Lógicos permitem que você se conecte facilmente ao sistema SAP local como parte do fluxo de trabalho."
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>Introdução ao Conector SAP 

A conectividade nuvem híbrida é a base dos Aplicativos Lógicos. O gateway de dados local permite gerenciar dados e acessar com segurança recursos locais usando os Aplicativos Lógicos. Neste artigo, demonstramos como se conectar a um sistema SAP local com um simples cenário: solicitar um IDOC via HTTP e enviar a resposta de volta.    

 > [!NOTE]
 > Este Conector SAP dá suporte aos seguintes Sistemas SAP No momento, há uma limitação de tempo limite nos Aplicativos Lógicos que bloqueia solicitações acima de 90 segundos No momento, há uma limitação de quantos campos mostrar no seletor de arquivo (os caminhos podem ser adicionados manualmente)
 >
 >

## <a name="prerequisites"></a>Pré-requisitos
- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/en-us/download/details.aspx?id=53127).  

    Instale o gateway de dados local mais recente, versão 1.15.6150.1 ou superior, caso ainda não o tenha feito. As instruções podem ser encontradas [neste artigo](http://aka.ms/logicapps-gateway). O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

- Baixe e instale a biblioteca de Cliente SAP mais recente no mesmo computador onde você instalou o gateway universal

## <a name="use-sap-connector"></a>Usar o Conector SAP

1. Vamos criar um gatilho de Solicitação HTTP, em seguida, selecione a ação de conector SAP digitando "SAP" no campo de pesquisa.    
 ![Pesquisar por SAP](media/logic-apps-using-sap-connector/picture1.png)

2. Escolha "SAP" (ApplicationHost ou MessagingHost com base na sua configuração SAP) e crie uma conexão para ele usando o gateway universal.
 - Caso não tenha uma conexão existente, é solicitado que você crie uma.
 - Marque a opção "Conectar via gateway de dados local", você vê campos adicionais exibidos assim que a caixa de seleção é marcada.
 - Especificar a cadeia de nome de conexão
 - Selecione o gateway instalado na etapa anterior, ou selecione "Instalar o Gateway" para instalar um novo gateway.   
 ![Adicionar cadeia de conexão ao SAP](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > Há duas conexões SAP diferentes, uma para [Host de Aplicativo](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) e outro para [host de Mensagens](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)
  >
  >

3. Depois de fornecer todos os detalhes, clique em "Criar". Os Aplicativos Lógicos configuram e testam a conexão para garantir que ela esteja funcionando corretamente. Se tudo der certo, você verá opções para o cartão selecionado anteriormente. Use o seletor de arquivo para localizar a categoria IDOC correta ou digite o caminho manualmente e selecione a resposta HTTP no campo do corpo.    
 ![AÇÃO SAP](media/logic-apps-using-sap-connector/picture3.png)

4. Crie uma Resposta HTTP adicionando uma nova ação, a mensagem de resposta deve ser da saída do SAP

5. Salve seu Aplicativo Lógico e teste-o enviando um IDOC através da URL de gatilho HTTP

6. Depois que o IDOC é enviado, aguarde a resposta do Aplicativo Lógico   

  > [!TIP]
  > Confira como [monitorar seus Aplicativos Lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).
  >
  >

7. Pronto! Agora você tem um Aplicativo Lógico funcional usando o conector SAP. Você pode começar explorando outras funcionalidades que ele oferece:
  - BAPI
  - RFC

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Criar uma [conexão local](../logic-apps/logic-apps-gateway-connection.md) com Aplicativos Lógicos.


<!--HONumber=Jan17_HO3-->


