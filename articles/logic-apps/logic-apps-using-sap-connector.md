---
title: "Conectar-se a um sistema SAP local no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Usar o gateway de dados local para se conectar a um sistema SAP local em seu fluxo de trabalho de aplicativo lógico"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>Usar o conector SAP em seu aplicativo lógico 

O gateway de dados local permite gerenciar dados e acessar com segurança recursos locais. Use este tópico para saber como se conectar a um sistema SAP local a fim de solicitar um IDOC via HTTP e enviar a resposta de volta.    

 > [!NOTE]
> Limitações atuais:
 > - Aplicativos Lógicos expiram caso uma solicitação exceda 90 segundos. Nesse cenário, as solicitações podem ser bloqueadas. 
 > - O seletor de arquivos não exibe todos os campos disponíveis. Nesse cenário, você pode adicionar os caminhos manualmente.

## <a name="prerequisites"></a>Pré-requisitos
- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127), versão 1.15.6150.1 ou superior. [Como conectar-se ao gateway de dados local em um aplicativo lógico](http://aka.ms/logicapps-gateway) lista as etapas. O gateway deve ser instalado em um computador local antes de prosseguir.

- Baixe e instale a biblioteca de cliente SAP mais recente no mesmo computador em que você instalou o gateway de dados. Use qualquer uma das versões de SAP a seguir: 
    - Servidor SAP
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode com EHP 4.0
        - SAP ECC 6.0 com EHP 7.0 e todas as versões anteriores do EHP
 
    - Cliente SAP
        - SAP RFC SDK 7.20 UNICODE
        - Conector SAP do .NET (NCo) 3.0

## <a name="add-the-sap-connector"></a>Adicione o conector SAP

O conector SAP tem Ações. Ele não tem Gatilhos. Assim, use outro gatilho no início do fluxo de trabalho. 

1. Adicione o gatilho Solicitação/Resposta e, em seguida, selecione **Nova etapa**.
2. Selecione **Adicionar uma ação** e, em seguida, escolha o conector SAP digitando `SAP` no campo de pesquisa:    
 ![Selecione Servidor de Aplicativos SAP ou Servidor de Mensagens SAP](media/logic-apps-using-sap-connector/picture1.png)

3. Selecione **servidor de aplicativos** ou [servidor de mensagens](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) [SAP](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), dependendo da sua configuração de SAP. Caso não tenha uma conexão existente, será solicitado que você crie uma: 
    1. Selecione **Conectar-se por meio do gateway de dados local** e insira os detalhes do sistema SAP:   
 ![Adicionar cadeia de conexão ao SAP](media/logic-apps-using-sap-connector/picture2.png)  
    2. Selecione um **Gateway** existente. Ou, selecione **Instalar Gateway** para instalar um novo gateway:    
 ![Instalar um novo gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. Após inserir todos os detalhes, selecione **Criar**. Os Aplicativos Lógicos configuram e testam a conexão para garantir que ela esteja funcionando corretamente.

4. Insira um nome para a conexão SAP.

5. Agora, as diferentes opções de SAP estão disponíveis. Use o seletor de arquivos para localizar sua categoria IDOC. Ou digite manualmente o caminho e selecione a resposta HTTP no campo **corpo**:     
 ![AÇÃO SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Crie uma resposta HTTP adicionando uma nova ação. A mensagem de resposta deve se originar da saída do SAP.

7. Salve seu aplicativo lógico. Teste-o enviando um IDOC por meio da URL de gatilho HTTP. Após o envio do IDOC, aguarde a resposta do aplicativo lógico:   

  > [!TIP]
  > Confira como [monitorar seus Aplicativos Lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Com o conector SAP adicionado ao aplicativo lógico, comece a explorar outras funcionalidades:

  - BAPI
  - RFC

## <a name="next-steps"></a>Próximas etapas
- Saiba como validar, transformar e outras funções semelhantes ao BizTalk no [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Criar uma [conexão local](../logic-apps/logic-apps-gateway-connection.md) com Aplicativos Lógicos.



<!--HONumber=Feb17_HO1-->


