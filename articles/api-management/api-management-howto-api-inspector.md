---
title: "Depurar as APIs usando o rastreamento de solicitação no Gerenciamento de API do Azure | Microsoft Docs"
description: "Siga as etapas deste tutorial para aprender a inspecionar as etapas de processamento de solicitação no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7b9bec7927169b9d820c095a7d11705264e7dcfe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="debug-your-apis-using-request-tracing"></a>Depure suas APIs usando o rastreamento de solicitação

Este tutorial descreve como verificar o processamento da solicitação para ajudá-lo com a depuração e a solução de problemas da API. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Rastrear uma chamada

![Inspetor de API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="trace-a-call"></a>Rastrear uma chamada

1. Selecione **APIs**.
2. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
3. Selecione a operação **GetSpeakers**.
4. Mude para a guia **Teste**.
5. Não se esqueça de incluir um cabeçalho HTTP chamado **Ocp-Apim-Trace** com o valor definido como **true**.
6. Clique em **"Enviar"** para fazer uma chamada de API. 
7. Aguarde a conclusão da chamada. 
8. Acesse a guia **Rastreamento** no **Console de API**. Você pode clicar em qualquer um dos links a seguir para ir até as informações de rastreamento detalhadas: **entrada**, **back-end**, **saída**.

    Na seção **entrada**, é exibido o Gerenciamento de API da solicitação original recebido do chamador e todas as políticas aplicadas à solicitação incluindo as políticas de limite de taxa e cabeçalho de conjunto que adicionamos na etapa 2.

    Na seção **back-end**, é exibido o Gerenciamento de API de solicitações enviado ao back-end de API e a resposta recebida.
    
    Na seção **saída**, são exibidas todas as políticas aplicadas à resposta antes de enviar de volta para o chamador.
 
    > [!TIP]
    > Cada etapa também mostra o tempo decorrido desde quando a solicitação foi recebida pelo Gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Rastrear uma chamada

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Usar revisões](api-management-get-started-revise-api.md)
