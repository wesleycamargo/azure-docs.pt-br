---
title: "Criando um painel de mídia social sem servidor no Azure | Microsoft Docs"
description: "Criando um painel de mídia social sem servidor no Azure"
services: functions, logic-apps, cognitive-services
keywords: "fluxo de trabalho, aplicativos de nuvem, serviços de nuvem, processos de negócios, integração de sistemas, integração de aplicativos empresariais, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>Criando um painel de mídia social sem servidor no Azure

O [Azure Functions](functions-overview.md) se integra com o [Aplicativo Lógico do Azure](../logic-apps/logic-apps-what-are-logic-apps.md) para possibilitar que você crie orquestrações complexas com outros serviços de terceiros e do Azure. Este tópico demonstra como disparar um aplicativo lógico de um feed de mídia social e analisar o texto com os [Serviços Cognitivos do Azure](../cognitive-services/Welcome.md).

Este artigo mostra como criar um aplicativo lógico no Portal do Azure que:

> [!div class="checklist"]
> * Verifica se há novos tweets usando uma palavra-chave ou hashtag que você fornecer.
> * Usa o conector **Detectar Sentimento** para estimar o sentimento dos tweets (de ruim a bom).
> * Usa uma função do Azure para processar o sentimento do tweet em três categorias (VERMELHO, AMARELO ou VERDE – para bom, neutro e ruim).
> * Usa uma condição para verificar se o sentimento é VERMELHO (ruim).
> * Se a condição for VERMELHO, envia um email.

A imagem a seguir mostra uma parte do aplicativo lógico no designer:

![imagem dos dois primeiros passos do aplicativo no Designer de Aplicativos Lógicos](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Twitter.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>Criar uma função de categorização

Após a conclusão da implantação do aplicativo de funções, abra o novo aplicativo de funções. Nesta seção, você cria uma função para categorizar o sentimento do tweet em três categorias (VERMELHO, AMARELO ou VERDE – para bom, neutro e ruim).

![Folha Aplicativos de Funções, Funções +](media/functions-twitter-email/add_fun.png)

Mantenha o padrão **Webhook + API**, **CSharp** e, em seguida, selecione **Criar esta função**.

![Folha Aplicativos de Funções, Funções +](media/functions-twitter-email/add_fun2.png)

Você criou uma função de Webhook/API (também conhecida como gatilho HTTP) que pode ser chamada sob demanda pelo aplicativo que você está criando. Se quisesse criar uma função que fosse executada segundo um cronograma, você criaria uma função de Temporizador.

Substitua o conteúdo do arquivo *run.csx* pelo código a seguir:

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

    if (score < .3)
    {
        category = "RED";
    }
    else if (score < .6)
    {
        category = "YELLOW";
    }

    return req.CreateResponse(HttpStatusCode.OK, category);
}
```

Salve a alteração.

### <a name="test-the-function"></a>Testar a função

Selecione **Testar** (à direita da caixa de código).  Insira 0.2 na caixa de texto **Corpo da solicitação** e, em seguida, selecione **Executar**. A saída mostra "VERMELHO" e o status HTTP é 200 OK.

 ![test ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>Serviços Cognitivos

Criar uma conta dos Serviços Cognitivos. Uma conta dos Serviços Cognitivos é necessária para detectar o sentimento dos tweets que estamos monitorando.

Navegue até **Novo > Inteligência + Análise > Serviços Cognitivos**. Defina cada campo obrigatório:

![Criar folha de conta Cognitiva](media/functions-twitter-email/cog_svcs_account.png)

| Campo               | Valor de exemplo | Comentário |
| ----------------- | ------------ | ------------- |
| Nome da conta | MyCognitiveServicesAccnt | Insira um nome exclusivo. |
| Tipo de API | API de Análise de Texto | Selecione Análise de Texto |
| Local | Oeste dos EUA | Atualmente, apenas **Oeste dos EUA** está disponível |
| Camada de preços | F0 | Caso suas chamadas se esgotem, defina uma camada mais elevada.|
| Grupo de recursos | rg1 | Use o grupo de recursos especificado anteriormente.|

### <a name="copy-the-cognitive-services-key"></a>Copie a chave dos Serviços Cognitivos

Selecione **Chaves**. Você precisará de uma chave em uma etapa posterior.

 ![simétricas](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

No Portal do Azure, clique em **Novo > Enterprise Integration > Aplicativo Lógico**

![etapa novo aplicativo lógico, etapa anterior](media/functions-twitter-email/new_logicApp.png)

Na folha **Criar aplicativo lógico**, insira cada campo e selecione **Criar**.

![Etapa criar aplicativo lógico, etapa anterior](media/functions-twitter-email/new_logicApp2.png)

Após ter sido criado, o aplicativo lógico é aberto no designer. Escolha o modelo **Aplicativo Lógico em branco**.

![Aplicativo Lógico em branco](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>Adicionar um gatilho para o Twitter

O **Designer do Aplicativo Lógico** exibe diversos serviços e gatilhos a que você pode se conectar.

Selecione o serviço **Twitter**.

![conector do Twitter](media/functions-twitter-email/twitter_connector.png)

Selecione o gatilho **Quando um novo Tweet é postado**.

![Gatilho "Quando um novo Tweet é postado"](media/functions-twitter-email/tw_trig.png)

Entre em sua conta do Twitter.

![Entre em sua conta do Twitter](media/functions-twitter-email/signin_twit.png)

Insira sua senha e selecione **Autorizar aplicativo**.

![autenticação do Twitter em nova janela acima](media/functions-twitter-email/auth_twit.png)

Insira o texto, a frequência e o intervalo de pesquisa. Caso especifique uma hashtag popular (como #football, #futebol ou #futbol), você pode usar rapidamente todas as chamadas de serviço alocadas em sua conta de serviços cognitivos. Caso as chamadas sejam esgotadas, você pode aumentar o tipo de preço. 

Pesquise por #Azure a cada 15 minutos:

![#Azure a cada 15 minutos](media/functions-twitter-email/azure_tweet.png)

Salve o aplicativo.

### <a name="add-a-text-analytics-connector"></a>Adicionar um conector de **Análise de Texto**

O conector de análise de texto detecta o sentimento dos tweets.

Selecione **Nova Etapa** e **Adicionar uma ação**.

![Nova Etapa e, então, Adicionar uma ação](media/functions-twitter-email/new_step.png)

Adicione o conector de **Análise de Texto**.

![Escolha uma janela de ação](media/functions-twitter-email/choose_action.png)

Selecione a ação **Detectar Sentimento**. A classificação de sentimento costuma ser precisa, mas às vezes ela interpreta o texto erroneamente.

![Detectar Sentimento](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>Criar a ação Detectar Sentimento

  * Insira um nome de conexão, como **MyKey**.
  * Copie e cole a chave que você criou na etapa [Criar uma conta dos Serviços Cognitivos](#cognitive-services).
  * Selecione **Criar**.
  * Salve o aplicativo.

![Detectar Sentimento](media/functions-twitter-email/ta_detect_sent.png)

Selecione o ícone **Texto do tweet** como o **Texto a ser analisado**

![Detectar Sentimento](media/functions-twitter-email/ds_tta.png)

![Detectar Sentimento](media/functions-twitter-email/ds_tta2.png)

Salve o aplicativo.

## <a name="connect-to-the-azure-function"></a>Conectar-se à função do Azure

Nesta seção, você adiciona a função que criou anteriormente que categoriza o sentimento do tweet como VERMELHO, AMARELO ou VERDE.

* No Designer de Aplicativos Lógicos, selecione **Nova etapa** e, em seguida, selecione **Adicionar uma ação**.
* Selecione **Azure Functions**.
* Selecione **Escolher uma Função do Azure**.

![Caixa de função do Azure mostrando "Escolher uma Função do Azure"](media/functions-twitter-email/choose_fun.png)

* Selecione a Função do Azure que você criou anteriormente.
* Selecione **Pontuação** para preencher o **Corpo da Solicitação**.

![Pontuação](media/functions-twitter-email/trigger_score.png)

Salve o aplicativo.

## <a name="add-email-notification"></a>Adicionar notificação por email

Nesta seção, adicionamos uma verificação condicional para tweets se sentimento negativo (condição VERMELHA).

* Selecione **Nova etapa**.
* Selecione **Adicionar uma condição**.
* Selecione **Corpo** na primeira caixa de texto **Escolher um valor**.
* Insira "VERMELHO" na segunda caixa de texto **Escolher um valor**.
* Salve o aplicativo.

![caixa de condição](media/functions-twitter-email/condition.png)

* Na caixa **SE SIM, NÃO FAZER NADA**, selecione **Adicionar uma ação**.
* Insira o Outlook ou o Gmail na caixa **Pesquisar todos os serviços e ações**. O Outlook é usado neste tutorial. Consulte [Adicionar uma ação do Gmai] (../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger) para obter instruções sobre o Gmail. Observação: se tiver uma [conta da Microsoft](https://account.microsoft.com/account) pessoal, você poderá usar essa conta como conta do Outlook.com.

![Caixa "Escolher uma ação"](media/functions-twitter-email/outlook.png)

Selecione **Enviar um email do Outlook.com**.

![Caixa "Outlook.com"](media/functions-twitter-email/sendEmail.png)

Entre no Outlook.com.

![caixa de entrada](media/functions-twitter-email/signin_outlook.png)

Insira os itens a seguir:

   * **Para**: o endereço de email para o qual a mensagem deve ser enviada.
   * **Assunto**: Pontuação.
   * **Corpo**: o local e o texto do tweet.

![Caixa "Enviar um email"](media/functions-twitter-email/sendEmail2.png)

Salve o aplicativo.
Selecione **Executar** para iniciar o aplicativo.

### <a name="check-the-status"></a>Verifique o status

Na folha do Aplicativo lógico, selecione **Visão geral** e, em seguida, selecione uma linha na coluna **Executar histórico**:

![Folha de visão geral](media/functions-twitter-email/over1.png)

A imagem a seguir mostra os detalhes da execução; quando o valor da condição não era "true", o email não foi enviado.

![Folha de visão geral](media/functions-twitter-email/skipped.png)

Se quiser testar imediatamente a função **Enviar um email**:

* Altere as **ENTRADAS** na primeira etapa (**Quando um novo tweet é postado**) por um termo popular, como #football, #futebol ou #futbol.

Processar termos populares consome mais recursos do que processar termos menos populares. Talvez você queira alterar o termo de pesquisa depois de verificar se o email está funcionando.

A imagem a seguir mostra os detalhes da execução; quando o valor da condição era "true", o email foi enviado.

![Folha de visão geral](media/functions-twitter-email/sent.png)

Você pode selecionar qualquer uma das caixas de serviço para exibir mais informações sobre os dados usados para a execução. Selecione **Quando um novo tweet é postado**; o texto de pesquisa e todas as saídas são exibidas, incluindo as saídas que não estamos usando.

## <a name="next-steps"></a>Próximas etapas

*  [Introdução ao Azure Functions](functions-overview.md)
*  [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [Adicionar condições e executar fluxos de trabalho](../logic-apps/logic-apps-use-logic-app-features.md)
*  [Modelos de aplicativos lógicos](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Criar aplicativos lógicos a partir dos modelos do Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

