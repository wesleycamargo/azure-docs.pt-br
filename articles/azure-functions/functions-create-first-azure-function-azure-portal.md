---
title: Criar uma função no Portal do Azure | Microsoft Docs
description: Crie seu primeiro Azure Function, um aplicativo sem servidor, em menos de dois minutos.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga

---
# Criar uma função no portal do Azure
## Visão geral
O Azure Functions consiste em uma experiência de computação sob demanda controlada por eventos que estende a plataforma de aplicativos existente do Azure com recursos para implementar código disparado por eventos que ocorrem em outros serviços do Azure, produtos de SaaS e sistemas locais. Com o Azure Functions, seus aplicativos são dimensionados sob demanda e você paga apenas pelos recursos que consome. O Azure Functions permite que você crie unidades de código agendadas ou disparadas, que serão implementadas em várias linguagens de programação. Para saber mais sobre o Azure Functions, confira a [Visão geral do Azure Functions](functions-overview.md).

Este tópico mostra como usar o portal do Azure Functions para criar uma Azure Function "hello world" simples do Node.js que é invocada por um gatilho HTTP. Antes de criar uma função no portal do Azure, você deve criar um aplicativo de funções explicitamente no Serviço de Aplicativo do Azure. Para que o aplicativo de funções seja criado para você automaticamente, confira [os outros tutoriais de início rápido do Azure Functions](functions-create-first-azure-function.md), que é uma experiência de início rápido mais simples e inclui vídeo.

## Criar um aplicativo de funções
Um aplicativo de funções hospeda a execução de suas funções no Azure. Siga estas etapas para criar um aplicativo de funções no portal do Azure.

Antes de criar sua primeira função, você precisará ter uma conta ativa do Azure. Se ainda não tiver uma conta do Azure, saiba que [há contas gratuitas disponíveis](https://azure.microsoft.com/free/).

1. Vá para o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **+Novo** > **Web + Móvel** > **Aplicativo de Funções**, selecione a **Assinatura**, digite um **Nome do aplicativo** exclusivo que identifica seu aplicativo de funções e especifique as seguintes configurações:
   
   * **[Grupo de recursos](../azure-portal/resource-group-portal.md)**: selecione **Criar novo** e digite um nome para seu novo grupo de recursos. Você também pode escolher um grupo de recursos existente; no entanto, é possível que você não consiga criar um Plano do Serviço de Aplicativo para seu aplicativo de funções.
   * **[Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**: escolha *dinâmico* ou *clássico*.
     * **Dinâmico**: o tipo padrão de plano para o Azure Functions. Quando você escolhe um plano dinâmico, também deve escolher o **Local** e definir a **Alocação de Memória** (em MB). Para saber como a alocação de memória afeta os custos, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/).
     * **Clássico**: um Plano do Serviço de Aplicativo clássico requer que você crie um **Local/Plano do Serviço de Aplicativo** ou selecione um existente. Essas configurações determinam o [local, os recursos, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados ao seu aplicativo.
   * **Conta de armazenamento**: cada aplicativo de funções requer uma conta de armazenamento. Você pode usar uma conta de armazenamento existente ou criar uma nova.
     
     ![Criar um aplicativo de funções no portal do Azure](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)
3. Clique em **Criar** para provisionar e implantar o novo aplicativo de funções.

Agora que o aplicativo de funções está provisionado, você pode criar sua primeira função.

## Criar uma função
Estas etapas criam uma função no guia de início rápido do Azure Functions.

1. Na guia **Início Rápido**, clique em **WebHook + API** e **JavaScript**; em seguida, clique em **Criar uma função**. Uma nova função predefinida do Node.js é criada.
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)
2. (Opcional) Neste ponto no início rápido, é possível fazer um tour rápido pelos recursos do Azure Functions no portal. Depois que você tiver concluído ou ignorado o tour, poderá testar sua nova função usando o gatilho HTTP.

## Testar a função
Como os inícios rápidos do Azure Functions contêm código funcional, você poderá testar imediatamente sua nova função.

1. Na guia **Desenvolver**, examine a janela **Código** e observe que esse código Node.js espera uma solicitação HTTP com um valor *name* passado no corpo da mensagem ou em uma cadeia de consulta. Quando a função é executada, esse valor é retornado na mensagem de resposta.
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)
2. Role para baixo até a caixa de texto **Corpo da solicitação**, altere o valor da propriedade *name* para seu nome e clique em **Executar**. Veja que a execução é disparada por uma solicitação HTTP de teste, as informações são gravadas em logs de streaming e a resposta "hello" é exibida na **Saída**.
3. Para disparar a execução da mesma função de outra janela ou guia, copie o valor de **URL da Função** da guia **Desenvolver** e cole-o na barra de endereços de um navegador, anexe o valor da cadeia de consulta `&name=yourname` e pressione enter. As mesmas informações são gravadas nos logs e o navegador exibe a resposta "hello" como antes.

## Próximas etapas
Este início rápido demonstra uma execução muito simples de uma função básica disparada por HTTP. Veja estes tópicos para saber mais sobre como usar o poder do Azure Functions em seus aplicativos.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md) Referência do programador para a codificação de funções e a definição de gatilhos e de associações.
* [Testando o Azure Functions](functions-test-a-function.md) Descreve várias ferramentas e técnicas para testar suas funções.
* [Como dimensionar o Azure Functions](functions-scale.md) Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de serviço Dinâmico, e como escolher o plano certo.
* [O que é o Serviço de Aplicativo do Azure?](../app-service/app-service-value-prop-what-is.md) O Azure Functions utiliza a plataforma Serviço de Aplicativo do Azure para a funcionalidade básica, como implantações, variáveis de ambiente e diagnóstico.

[!INCLUDE [Observação de introdução](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0914_2016-->