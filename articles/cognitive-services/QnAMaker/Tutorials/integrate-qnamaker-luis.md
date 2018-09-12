---
title: Integrar o QnA Maker e o LUIS - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: um tutorial passo a passo sobre como integrar o QnA Maker e o LUIS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781740"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrar o QnA Maker e o LUIS para distribuir sua base de dados de conhecimento
À medida que sua base de dados de conhecimento do QnA Maker aumentar, ficará difícil mantê-la como um único conjunto monolítico definido e haverá a necessidade de dividir a base de dados de conhecimento em partes lógicas menores.

Embora seja simples criar várias bases de dados de conhecimento no QnA Maker, você precisará de alguma lógica para rotear a pergunta de entrada para a base de dados de conhecimento apropriada. Você pode fazer isso usando o LUIS.

## <a name="architecture"></a>Arquitetura

![Arquitetura luis do QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

No cenário anterior, primeiro o QnA Maker obtém a intenção do ponto de entrada de um modelo LUIS e então usa isso para roteá-lo para a base de dados de conhecimento QnA Maker correta.

## <a name="prerequisites"></a>Pré-requisitos
- Faça logon no portal [LUIS](https://www.luis.ai/) e [crie um aplicativo](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Adicionar intenções](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) de acordo com seu cenário.
- [Treinar](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) e [publicar](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) seu Aplicativo LUIS.
- Faça logon no [QnA Maker](https://qnamaker.ai) e [crie](https://www.qnamaker.ai/Create) bases de dados de conhecimento, de acordo com seu cenário.
- Teste e publique as bases de dados de conhecimento.

## <a name="qna-maker--luis-bot"></a>QnA Maker + Bot LUIS
1. Primeiro crie um bot do aplicativo Web com o modelo LUIS, vincule-o com o aplicativo LUIS criado acima e modifique as intenções. Consulte as etapas detalhadas [aqui](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Adicione dependências à parte superior do arquivo, com as outras dependências:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Adicione a classe abaixo para chamar o serviço QnA Maker:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. Vá para https://qnamaker.ai -> Minhas bases de dados de conhecimento -> Exibir código de sua base de dados de conhecimento correspondente. Obtenha as seguintes informações:

    ![Solicitação HTTP do QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Criar uma instância da classe QnAMakerService para cada uma das suas bases de dados de conhecimento:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Chame a base de dados de conhecimento correspondente para a intenção.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>Criar o bot
1. No editor de código, clique duas vezes em `build.cmd` e selecione **Executar no Console**.

    ![executar no console](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. O modo de exibição de código é substituído por uma janela de terminal mostrando o andamento e os resultados da compilação.

    ![compilação de console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testar o bot
No portal do Azure, selecione **Testar no Webchat** para testar o bot. Digite mensagens com intenções diferentes para obter a resposta da base de dados de conhecimento correspondente.

![teste de chat na Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um plano de continuidade de negócios para o QnA Maker](../How-To/business-continuity-plan.md)
