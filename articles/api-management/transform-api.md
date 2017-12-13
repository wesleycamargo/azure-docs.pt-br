---
title: Transformar e proteger sua API com o Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como proteger sua API com cotas e políticas de limitação (limite de taxa)."
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
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>Transformar e proteger sua API 

O tutorial mostra como transformar sua API para que ela não revele informações de back-end privadas. Por exemplo, você talvez queira ocultar as informações sobre a pilha de tecnologia que está em execução no back-end. Você também pode desejar ocultar URLs originais que aparecem no corpo da resposta HTTP da API e em vez disso, redirecioná-las para o gateway de APIM.

Este tutorial também mostra como é fácil adicionar proteção à API de back-end configurando o limite de taxa com o Gerenciamento de API do Azure. Por exemplo, você talvez queira limitar um número de chamadas realizadas para a API, para que não seja usada em excesso pelos desenvolvedores. Para obter mais informações, consulte [Políticas de Gerenciamento de API](api-management-policies.md)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Transformar uma API para retirar os cabeçalhos de resposta
> * Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> * Proteger uma API adicionando a política de limite de taxa (limitação)
> * Testar as transformações

![Políticas](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformar uma API para retirar os cabeçalhos de resposta

Esta seção mostra como ocultar os cabeçalhos HTTP que você não deseja mostrar aos usuários. Neste exemplo, os cabeçalhos a seguir são excluídos na resposta HTTP:

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Selecione a guia **API**.
2. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
3. Selecione a operação **GetSpeakers**.
4. Clique na guia **Testar** na parte superior da tela.
5. Pressione o botão **Enviar** na parte inferior da tela. 

    Como você pode ver, a resposta original tem esta aparência:

    ![Políticas](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia **Design**.
6. Na janela **Processamento de saída**, clique no triângulo (ao lado do lápis).
7. Selecione **Editor de código**.
    
     ![Editar política](./media/set-edit-policies/set-edit-policies01.png)
9. Posicione o cursor dentro do elemento **<outbound>**.
10. Na janela à direita, em **Políticas de transformação**, clique em **+ Definir cabeçalho HTTP** duas vezes (para inserir dois trechos de código de política).

    ![Políticas](./media/transform-api/transform-api.png)
11. Modifique seu código **<outbound>** para ter a seguinte aparência:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM

Esta seção mostra como ocultar URLs originais que aparecem no corpo da resposta HTTP da API e em vez disso, redirecioná-las para o gateway de APIM.

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Selecione a guia **API**.
2. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
3. Selecione a operação **GetSpeakers**.
4. Clique na guia **Testar** na parte superior da tela.
5. Pressione o botão **Enviar** na parte inferior da tela. 

    Como você pode ver, a resposta original tem esta aparência:

    ![Políticas](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia **Design**.
6. Na janela **Processamento de saída**, clique no triângulo (ao lado do lápis).
7. Selecione **Editor de código**.
8. Posicione o cursor dentro do elemento **<outbound>**.
9. Na janela à direita, em **Políticas de transformação**, clique em **+ Localizar e substituir cadeia de caracteres no corpo**.
10. Modifique seu código **<find-and-replace** (no elemento **<outbound>**) para substituir a URL para corresponder ao gateway de APIM. Por exemplo:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Proteger uma API adicionando a política de limite de taxa (limitação)

Esta seção mostra como adicionar a proteção para a API de back-end configurando limites de taxa. Por exemplo, você talvez queira limitar um número de chamadas realizadas para a API, para que não seja usada em excesso pelos desenvolvedores. Neste exemplo, o limite é definido como 3 chamadas por 15 segundos para cada ID de assinatura. Depois de 15 segundos, um desenvolvedor pode repetir a chamada à API.

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia **Design**.
6. Na janela **Processamento de entrada**, clique no triângulo (ao lado de lápis).
7. Selecione **Editor de código**.
8. Posicione o cursor dentro do elemento **<inbound>**.
9. Na janela à direita, em **Políticas de restrição de acesso**, clique em **+ Limit call rate per key (+ Limitar taxa de chamada por chave)**.
10. Modifique o código **<rate-limit-by-key** (no elemento **<inbound>**) para o código a seguir:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Testar as transformações
        
Neste ponto, o código de suas políticas tem a seguinte aparência:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

O restante desta seção testa as transformações de política que você definir neste artigo.

### <a name="test-the-stripped-response-headers"></a>Testar os cabeçalhos de resposta retirados

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Clique na operação **GetSpeakers**.
5. Selecione a guia **Testar**.
6. Pressione **Enviar**.

    Como você pode ver, os cabeçalhos foram retirados:

    ![Políticas](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Testar a URL substituída

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Clique na operação **GetSpeakers**.
5. Selecione a guia **Testar**.
6. Pressione **Enviar**.

    Como você pode ver a URL foi substituída.

    ![Políticas](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Testar o limite de taxa (limitação)

1. Navegue até sua instância de APIM.
2. Selecione a guia **API**.
3. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
4. Clique na operação **GetSpeakers**.
5. Selecione a guia **Testar**.
6. Pressione **Enviar** três vezes em uma linha.

    Depois de enviar a solicitação três vezes, você obtém a resposta **429 Muitas solicitações**.
7. Aguarde 15 segundos e pressione **Enviar** novamente. Neste momento, você deve obter uma resposta **200 OK**.

    ![Limitação](./media/transform-api/test-throttling.png)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Transformar uma API para retirar os cabeçalhos de resposta
> * Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> * Proteger uma API adicionando a política de limite de taxa (limitação)
> * Testar as transformações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Monitorar sua API](api-management-howto-use-azure-monitor.md)
