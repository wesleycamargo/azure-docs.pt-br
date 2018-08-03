---
title: Perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal) no Azure | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal)
author: diberry
manager: cjgronlund
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 8e0d834b94ff902eb0c1e0ada2fb32d374cee12b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239110"
---
# <a name="language-understanding-faq"></a>Perguntas frequentes sobre o Reconhecimento vocal

Este artigo contém as respostas para perguntas frequentes sobre o LUIS (Serviço Inteligente de Reconhecimento Vocal).

## <a name="luis-authoring"></a>Criação de LUIS

### <a name="what-are-the-luis-best-practices"></a>Quais são as práticas recomendadas de LUIS? 
Comece com o [Ciclo de Criação](luis-concept-app-iteration.md) e, em seguida, leia as [melhores práticas](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual é a melhor maneira de começar a criar o meu aplicativo de LUIS?

A melhor maneira de criar seu aplicativo é por meio de um [processo incremental](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Qual é uma boa prática para modelar as intenções do meu aplicativo? Devo criar intenções mais específicas ou mais genéricas?

Escolha intenções que não sejam tão gerais a ponto de sobreporem-se, nem tão específica que seja difícil para o LUIS distinguir entre intenções semelhantes. Criar intenções discriminantes específicas é uma das melhores práticas para modelagem de LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante treinar a intenção None?

Sim, é bom treinar sua intenção **None** com mais enunciados conforme você adiciona mais rótulos a outras intenções. Uma boa proporção é 1 ou 2 rótulos adicionados a **None** para cada 10 rótulos adicionados a uma intenção. Essa proporção aumenta a capacidade discriminativa de LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como poso corrigir erros de ortografia em enunciados?

Veja o tutorial [API de Verificação Ortográfica do Bing V7](luis-tutorial-bing-spellcheck.md). O LUIS impõe limites exigidos pela API de Verificação Ortográfica do Bing V7. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como fazer para editar meu aplicativo de LUIS programaticamente?
Para editar seu aplicativo de LUIS programaticamente, use a [API de Criação](https://aka.ms/luis-authoring-apis). Veja [Chamar a API de criação LUIS](./luis-quickstart-node-add-utterance.md) e [Compilar um aplicativo de LUIS programaticamente usando Node.js](./luis-tutorial-node-import-utterances-csv.md) para obter exemplos de como chamar a API de Criação. A API de Criação exige que você use uma [chave de criação](luis-concept-keys.md#authoring-key), em vez de uma chave de ponto de extremidade. A criação programática permite até um milhão de chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves que você usa com o LUIS, consulte [Gerenciar chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Em local está o recurso de Padrão que forneceu a correspondência da expressão regular?
O **Recurso de Padrão** anterior atualmente foi preterido, tendo sido substituído por **[Padrões](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como fazer para usar uma entidade para extrair os dados corretos? 
Veja [entidades](luis-concept-entity-types.md) e [extração de dados](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variações de um enunciado de exemplo devem incluir pontuação? 
Adicione as diferentes variações como enunciados de exemplo para a intenção ou adicione o padrão do enunciado de exemplo com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação. 

### <a name="does-luis-currently-support-cortana"></a>O LUIS atualmente dá suporte à Cortana?

Os aplicativos pré-compilados da Cortana foram preteridos em 2017. Não há mais suporte para esses aplicativos. 

## <a name="luis-endpoint"></a>Ponto de extremidade LUIS

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Por que o LUIS adiciona espaços à consulta ao redor ou no meio das palavras?
O LUIS transforma em [token](luis-glossary.md#token) o enunciado com base na [cultura](luis-supported-languages.md#tokenization). O valor original e o valor em token estão disponíveis para [extração de dados](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como fazer para criar e atribuir uma chave de ponto de extremidade de LUIS?
[Crie a chave do ponto de extremidade](luis-how-to-azure-subscription.md#create-luis-endpoint-key) no Azure para seu nível de [serviço](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Atribua a chave](luis-how-to-manage-keys.md#assign-endpoint-key) na página **[Publicar](luis-how-to-publish-app.md)**. Não há uma API correspondente para essa ação. Em seguida, você deve alterar a solicitação HTTP para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Como fazer para interpretar as pontuações de LUIS? 
Seu sistema deve usar a intenção de pontuação mais alta, independentemente do seu valor. Por exemplo, uma pontuação inferior a 0,5 (menor que 50%) não significa necessariamente que o LUIS tem confiança baixa. Fornecer mais dados de treinamento pode ajudar a aumentar a pontuação da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que eu não vejo minhas ocorrências de ponto de extremidade no painel do meu aplicativo?
O total de pontos de extremidade no Painel do aplicativo é atualizado periodicamente, mas as métricas associadas à chave de ponto de extremidade de LUIS no portal do Azure são atualizadas com mais frequência. 

Se não visualizar ocorrências de ponto de extremidade atualizados no Painel, faça logon no portal do Azure e localize o recurso associado à chave de ponto de extremidade de LUIS e, em seguida, abra **Métricas** para selecionar a métrica **Total de Chamadas**. Se a chave de ponto de extremidade for usada para mais de um aplicativo de LUIS, a métrica no portal do Azure mostrará o número agregado de chamadas de todos os aplicativos de LUIS que a utilizam.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Meu aplicativo de LUIS estava funcionando ontem, mas hoje estou recebendo erros 403. Eu não alterei o aplicativo. Como corrigi-la? 
Seguindo as [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) nas próximas perguntas frequentes para criar uma chave de ponto de extremidade de LUIS e atribuindo-a ao aplicativo. Em seguida, você deve alterar a solicitação HTTP para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como fazer para protejar o ponto de extremidade de LUIS? 
Veja [Protegendo o ponto de extremidade](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhando dentro dos limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual é o número máximo de intenções e entidades compatível com um aplicativo de LUIS?
Veja a referência de [limites](luis-boundaries.md).

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero compilar um aplicativo de LUIS com mais do que o número máximo de intenções. O que devo fazer?

Veja [Melhores práticas para intenções](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero compilar um aplicativo de LUIS com mais do que o número máximo de entidades. O que devo fazer?

Veja [Melhores práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quais são os limites de número e tamanho das listas de frases?
Para o tamanho máximo de uma [lista de frases](./luis-concept-feature.md), veja a referência de [limites](luis-boundaries.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites nos enunciados de exemplo?
Veja a referência de [limites](luis-boundaries.md).

## <a name="testing-and-training"></a>Teste e treinamento

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vejo alguns erros no painel de teste em lote para alguns dos modelos de meu aplicativo. Como posso solucionar esse problema?

Os erros indicam que há alguma discrepância entre os rótulos e as previsões de seus modelos. Para resolver o problema, realize uma ou ambas as seguintes tarefas:
* Para ajudar o LUIS a melhorar a discriminação entre intenções, adicione mais rótulos.
* Para ajudar o LUIS a aprender mais rápido, adicione recursos de lista de frases que introduzam vocabulário específico do domínio.

Veja o tutorial [Teste em lote](luis-tutorial-batch-testing.md).

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando um aplicativo é exportado e reimportado em um novo aplicativo (com uma nova ID do aplicativo), as pontuações de previsão de LUIS são diferentes. Por que isso acontece? 

Veja [Diferenças de previsão entre cópias do mesmo aplicativo](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Publicação de aplicativo

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qual é a ID de locatário na janela "Adicionar uma chave ao seu aplicativo"?
No Azure, um locatário representa o cliente ou a organização que está associado a um serviço. Localize sua ID de locatário no portal do Azure na caixa **ID do Diretório** selecionando **Azure Active Directory** > **Gerenciar** > **Propriedades**.

![ID de locatário no portal do Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
### <a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Por que há mais chaves de ponto de extremidade na página de publicação do aplicativo do que eu atribuí ao aplicativo? 
Cada aplicativo de LUIS tem a chave de criação/inicial. As chaves de ponto de extremidade de LUIS criadas durante o período de GA são visíveis na página de publicação, independentemente de terem sido adicionadas ao aplicativo. Isso foi feito para facilitar a migração de GA. Qualquer nova chave de ponto de extremidade de LUIS não aparecerá na página de publicação. 

## <a name="app-management"></a>Gerenciamento de aplicativos

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como fazer para transferir a propriedade de um aplicativo de LUIS?
Para transferir um aplicativo de LUIS para uma assinatura do Azure diferente, exporte o aplicativo de LUIS e importe-lo usando uma nova conta. Atualize a ID do aplicativo de LUIS no aplicativo cliente que o chama. O novo aplicativo pode retornar pontuações de LUIS ligeiramente diferentes que as do aplicativo original. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como fazer para baixar um log de enunciados do usuário?
Por padrão, o seu aplicativo de LUIS registra enunciados dos usuários. Para baixar um log de enunciados que os usuários enviaram para seu aplicativo de LUIS, acesse **Meus Aplicativos** e clique nas reticências (***…***) na lista para o seu aplicativo. Em seguida, clique em **Exportar Logs de Ponto de Extremidade**. O log é formatado como um arquivo CSV (valores separados por vírgulas).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como eu desabilito o log de enunciados?
Você pode desligar o log de enunciados do usuário configurando `log=false` na URL do Ponto de Extremidade que seu aplicativo cliente usa para consultar o LUIS. No entanto, desativar o registro em log desabilita a habilidade do seu aplicativo de LUIS de sugerir enunciados ou melhorar o desempenho com base em [aprendizado ativo](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se você definir `log=false` devido a questões de privacidade de dados, não poderá baixar um registro desses enunciados do usuário de LUIS nem usar esses enunciados para melhorar o seu aplicativo.

O registro em log é o único armazenamento de enunciados. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que eu não quero registrar em log todos os meus enunciados de ponto de extremidade?
Se você estiver usando o log para análise de previsão, não capture enunciados de teste em seu log.

## <a name="data-management"></a>Gerenciamento de dados

### <a name="can-i-delete-data-from-luis"></a>Posso excluir dados de LUIS? 

* Você sempre pode excluir enunciados de exemplo usados para treinar o LUIS. Se você excluir um enunciado de exemplo do seu aplicativo de LUIS, ele será removido do serviço Web de LUIS e não estará disponível para exportação.
* É possível excluir enunciados da lista de enunciados do usuário sugeridos pelo LUIS na página **Examinar enunciados do ponto de extremidade**. Excluir enunciados dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.
* Se você excluir uma conta, todos os aplicativos serão excluídos, junto com os respectivos logs e enunciados de exemplo. Os dados serão mantidos nos servidores por 60 dias antes de serem excluídos permanentemente.

## <a name="language-and-translation-support"></a>Suporte a idioma e tradução 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Eu tenho um aplicativo em um idioma e desejo criar um aplicativo paralelo em outro idioma. Qual é a maneira mais fácil de fazer isso?
1. Exporte seu aplicativo.
2. Traduza os enunciados rotulados no arquivo JSON do aplicativo exportado para o idioma de destino.
3. Talvez seja necessário alterar os nomes das intenções e das entidades ou deixá-los como estão.
4. Por fim, importe o aplicativo para ter um aplicativo de LUIS no idioma de destino.

## <a name="app-notification"></a>Notificação do aplicativo

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Porque eu recebi um email dizendo que estou quase sem cota?
Sua chave de criação/inicial tem permissão apenas para mil consultas de ponto de extremidade por um mês. Crie uma chave de ponto de extremidade de LUIS (gratuita ou paga) e use essa chave ao fazer consultas de ponto de extremidade. Se você estiver fazendo consultas de ponto de extremidade de um bot ou outro aplicativo cliente, precisará alterar a chave do ponto de extremidade LUIS lá. 

## <a name="integrating-luis"></a>Integrando LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Em que local meu aplicativo de LUIS é criado durante o processo de assinatura de bot do aplicativo Web do Azure?
Se você selecionar um modelo de LUIS e o botão **Selecionar** no painel de modelo, o painel da esquerda mudará para incluir o tipo de modelo e perguntará em que região criar o modelo de LUIS. No entanto, o processo de bot do aplicativo Web não cria uma assinatura de LUIS.

![Região do bot do aplicativo Web de modelo de LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões de LUIS são compatíveis com a preparação de fala do Bot Framework?
[Preparação de fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) é compatível apenas com aplicativos LUIS na instância central (EUA). 

## <a name="luis-service"></a>Serviço LUIS 

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>O LUIS está disponível na nuvem privada ou local?
Não. 

## <a name="changes-to-the-docs"></a>Alterações aos documentos

### <a name="where-did-the-tutorials-go"></a>Para que local foram os tutoriais? 
Os artigos que estavam anteriormente na seção Tutorial agora estão na seção de Instruções dos documentos. 

|Tutorial|
|--|
|Integrar LUIS a um bot com [C#](luis-csharp-tutorial-build-bot-framework-sample.md) e [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Adicionar Application Insights a um Bot com [C#](luis-tutorial-bot-csharp-appinsights.md) e [Node.js](luis-tutorial-function-appinsights.md)|
|Criar um aplicativo de LUIS programaticamente usando [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Usar a [entidade composta](luis-tutorial-composite-entity.md) para extrair dados agrupados|
|Adicionar [lista de entidades](luis-tutorial-list-entity.md) para maior detecção de entidade usando Node.js|
|Melhorar a precisão da previsão com [lista de frases](luis-quickstart-primary-and-secondary-data.md), [padrões](luis-tutorial-pattern.md) e [teste em lote](luis-tutorial-batch-testing.md)|
|[Corrigir a ortografia](luis-tutorial-batch-testing.md) com a API de Verificação Ortográfica do Bing v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na Conferência Build 2018, ouvi falar de uma demonstração ou um recurso de Reconhecimento Vocal, mas não lembro como se chamava. 

Os seguintes recursos foram lançados na Conferência Build 2018:

|NOME|Conteúdo|
|--|--|
|Melhorias|Entidades de [Expressão regular](luis-concept-data-extraction.md##regular-expression-entity-data) e de [Frase-chave](luis-concept-data-extraction.md#key-phrase-extraction-entity-data)
|Padrões|[Conceito](luis-concept-patterns.md), [tutorial](luis-tutorial-pattern.md) e [instruções](luis-how-to-model-intent-pattern.md) de padrões<br>Conceito da entidade [Patterns.Any](luis-concept-entity-types.md) incluindo [Lista explícita](luis-concept-patterns.md#explicit-lists) para exceções<br>Conceito de [Funções](luis-concept-roles.md)|
|Integrações|Integração de [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) da [análise de sentimento](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>Integração de [fala](https://docs.microsoft.com/azure/cognitive-services/speech) da [preparação de fala](luis-how-to-publish-app.md#enable-speech-priming) junto com o [SDK de Fala](https://aka.ms/SpeechSDK)|
|Ferramenta de Expedição|Parte de [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), [ferramenta](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) de linha de comando de Expedição para combinar vários aplicativos LUIS e QnA Maker em único aplicativo de LUIS para um melhor reconhecimento de intenção em um Bot

[Rotas de API](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) de criação adicionais foram incluídas. 

Vídeos: 
* [Azure Friday na Build 2018: Serviços Cognitivos – Linguagem (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Mostra de IA na Build 2018 – Novidades do Serviço Inteligente de Reconhecimento Vocal](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sessão da Build 2018 – Práticas recomendadas da inteligência do bot, dos recursos de fala e de NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 – atualizações de LUIS](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projetos: 
* demonstração do [Bot Contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) – código-fonte no GitHub

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o LUIS, veja os seguintes recursos:
* [Perguntas do Stack Overflow marcadas com LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum de LUIS (Serviço Inteligente de Reconhecimento Vocal) na MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 