---
title: Perguntas frequentes sobre o serviço de fala em texto no Azure | Microsoft Docs
description: Aqui estão as respostas para as perguntas mais populares sobre o Speech to Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 543e8d6fb68a351dfe75c962debaf15eeb080a3f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223880"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Perguntas frequentes sobre Conversão de Fala em Texto

Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade do Serviço de Fala Personalizada em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Geral

**Pergunta**: Qual é a diferença entre a linha de base e os Modelos de Conversão de Fala em Texto personalizados?

**Resposta**: os modelos de linha de base foram treinados com dados de propriedade da Microsoft e já estão implantados na nuvem. Os modelos personalizados permitem que o usuário adapte um modelo para melhor se adequar a um ambiente específico com ruído ou linguagem ambiental específica. Pisos de fábrica, carros, ruas barulhentas exigiriam modelos acústicos adaptados, enquanto tópicos específicos como biologia, física, radiologia, nomes de produtos e acrônimos personalizados exigiriam um modelo de linguagem adaptado.

**Pergunta**: Por onde eu começo se eu quiser usar um modelo de linha de base?

**Resposta**: primeiro você precisa obter uma [ chave de assinatura ](get-started.md). Se você quiser fazer chamadas REST para os modelos de linha de base pré-empregados, consulte os [detalhes aqui](rest-apis.md). Se você quiser usar WebSockets, baixe o [SDK](speech-sdk.md)

**Pergunta**: Preciso sempre criar um modelo de fala personalizado?

**Resposta**: Não, se o aplicativo estiver usando linguagem genérica do cotidiano, não será necessário personalizar um modelo. Além disso, se o aplicativo for usado em um ambiente com pouco ou nenhum ruído de fundo, não será necessário personalizá-lo. O portal permite que os usuários implantem modelos básicos e personalizados e executem testes de precisão contra eles. Os usuários podem usar esse recurso para medir a precisão de uma linha de base em comparação a um modelo personalizado.

**Pergunta**: Como saberei quando o processamento do meu conjunto de dados ou modelo está completo?

**Resposta**: Atualmente, o status do modelo ou conjunto de dados na tabela é a única maneira de saber.
Quando o processamento estiver concluído, o status será com "Êxito".

**Pergunta**: Eu posso criar mais de um modelo?

**Resposta**: Não há limite para a quantidade de modelos na coleção.

**Pergunta**: Percebi que cometi um erro. Como cancelar a importação de dados ou modelo de criação em andamento? 

**Resposta**: Atualmente você não pode reverter um processo de adaptação acústica ou de linguagem. Os dados e modelos importados poderão ser excluídos depois que estiverem em um estado de terminal.

**Pergunta**: Qual é a diferença entre os modelos pesquisa & ditado e os modelos de conversação?

**Resposta**: Há mais de um modelo de linha de base para escolher no Serviço de Fala. O modelo Conversacional é apropriado para reconhecimento de fala falada em um estilo conversacional. Esse modelo seria ideal para transcrever chamadas, enquanto a pesquisa e o Ditado são ideais para Aplicativos acionados por voz. Universal é um novo modelo que visa abordar ambos os cenários.

**Pergunta**: Posso atualizar meu modelo existente (modelo de empilhamento)?

**Resposta**: não possível atualizar os modelos existentes. Como uma solução alternativa combinar o conjunto de dados antigo com o novo e readapt.

Os conjuntos de dados antigos e novos devem ser combinados em um único .zip (se for dados acústicos) ou em um arquivo .txt, se forem dados de idioma. Uma vez adaptação é feita o novo modelo atualizado precisa ser implantado eliminação para obter um novo ponto de extremidade

**Pergunta**: E se eu precisar de maior simultaneidade para o modelo implantado do que o oferecido no portal? 

**Resposta**: você pode dimensionar o modelo em incrementos de 20 solicitações simultâneas. 

Entre em contato conosco se você precisar de maior escala.

**Pergunta**: Posso baixar meu modelo e executá-lo localmente?

**Resposta**: modelos não podem ser baixados e executados localmente.

**Pergunta**: Minhas solicitações registradas?

**Resposta**: Você tem uma opção durante a criação de uma implantação de desativar o rastreamento, neste momento nenhum áudio ou transcrições serão registradas. Caso contrário, solicitações normalmente são registradas no Azure no armazenamento seguro. Se você tiver mais problemas de privacidade que o impedem de usar o serviço de voz personalizada, entre em contato com um dos canais de suporte.

## <a name="importing-data"></a>Importação de dados

**Pergunta**: Qual é o limite no tamanho do conjunto de dados? Por quê? 

**Resposta**: O limite atual de um conjunto de dados é de 2 GB, devido a restrições quanto ao tamanho de um arquivo para upload HTTP. 

**Pergunta**: Posso compactar meus arquivos de texto para carregar um arquivo de texto maior? 

**Resposta**: Não, no momento são permitidos apenas os arquivos de texto não compactados.

**Pergunta**: O relatório de dados diz que houve falha utterances. Qual é o problema?

**Resposta**: Falha ao carregar a 100% de utterances em um arquivo não é um problema.
Se definir a maioria das utterances em um dado acústico ou idioma (por exemplo, > 95%) com êxito são importados, o conjunto de dados pode ser usado. No entanto, é recomendável que você tente entender por que o utterances falha e corrija os problemas. Os problemas mais comuns, como a formatação de erros, são difíceis de resolver. 

## <a name="creating-am"></a>Criar AM

**Pergunta**: Quantos dados acústicos são necessários?

**Resposta**: É recomendável iniciar com 30 minutos a uma hora de dados acústicos.

**Pergunta**: Quais dados devo coletar?

**Resposta**: Colete dados que sejam o mais próximos possível do cenário do aplicativo e do caso de uso.
A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivo ou dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados de uma variedade de falantes o mais ampla possível. 

**Pergunta**: Como devo coletar isso? 

**Resposta**: Você pode criar um aplicativo de coleta de dados independente ou usar algum software de gravação de áudio da plataforma.
Você também pode criar uma versão do seu aplicativo que registre os dados de áudio e usá-los. 

**Pergunta**: Preciso transcrever dados de adaptação? 

**Resposta**: Sim! Você pode transcrever você mesmo ou usar um serviço profissional de transcrição. Alguns usuários preferem transcritores profissionais, enquanto outros usam crowdsourcing ou eles mesmos fazem as transcrições.

## <a name="accuracy-testing"></a>Teste de precisão

**Pergunta**: Posso realizar testes off-line do meu modelo acústico personalizado usando um modelo de linguagem personalizado?

**Resposta**: Sim, basta selecionar o modelo de idioma personalizado no menu suspenso ao configurar o teste off-line

**Pergunta**: Posso realizar o teste off-line do meu modelo de idioma personalizado usando um modelo acústico personalizado?

**Resposta**: Sim, basta selecionar o modelo acústico personalizado no menu suspenso ao configurar o teste off-line.

**Pergunta**: O que é o WER (Relatório de Erros do Windows) e como é calculado?

**Resposta**: O WER (Relatório de Erros do Windows) é a métrica de avaliação para reconhecimento de fala. É contado como o número total de erros, o que inclui inserções, exclusões e substituições, dividido pelo número total de palavras na transcrição de referência. Mais detalhes [aqui](https://en.wikipedia.org/wiki/Word_error_rate).

**Pergunta**: Como determino se os resultados de um teste de precisão são bons?

**Resposta**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo personalizado.
Você deve tentar atingir o modelo de referência para que a personalização seja útil.

**Pergunta**: Como fazer para descobrir o Relatório de Erros do Windows dos modelos de base, de modo que eu possa ver se houve melhorias? 

**Resposta**: Os resultados do teste offline mostram a exatidão da precisão da linha de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-lm"></a>Criar LM

**Pergunta**: A quantidade de dados texto é necessário carregar?

**Resposta**: Depende de quão diferentes o vocabulário e as frases usadas na sua aplicação são dos modelos de idioma de partida. Para todas as palavras novas, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluir frases nos dados do idioma também é útil, pois informa ao sistema para ouvir esses termos também. É comum ter pelo menos 100 e normalmente várias centenas de expressões ou mais no conjunto de dados de linguagem. Também se determinados tipos de consultas devem ser mais comuns do que outras pessoas, você pode inserir várias cópias das consultas comuns no conjunto de dados.

**Pergunta**: posso apenas carregar uma lista de palavras?

**Resposta**: carregar uma lista de palavras será obter as palavras em para vocabulário mas ensina o sistema como as palavras são usadas normalmente.
Ao fornecer declarações completas ou parciais (frases ou frases de coisas que os usuários provavelmente dirão), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizada é bom não apenas para obter novas palavras no sistema, mas também para ajustar a probabilidade de palavras conhecidas para sua aplicação. Fornecer utterances completas ajuda o sistema Saiba mais. 

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
