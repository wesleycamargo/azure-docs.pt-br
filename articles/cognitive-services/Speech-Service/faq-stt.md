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
ms.openlocfilehash: 64e505889ef9472603471d67a961985c1290663a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054855"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Perguntas Frequentes do Serviço de Fala Personalizado

Se você não encontrar respostas para suas perguntas nas perguntas frequentes, tente pedir a comunidade de serviço de voz personalizada em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Geral

**Pergunta**: qual é a diferença entre a linha de base e fala personalizado para modelos de texto?

**Resposta**: modelos de linha de base foi treinados com dados de propriedade do Microsoft e já foram implantados na nuvem. Modelos personalizados permitem ao usuário adaptar um modelo para melhor se ajustar a um ambiente específico com ruídos de ambiente específicos ou idioma. Pisos de fábrica, carros, ruídas ruas exigiria adaptado modelo acústico enquanto tópicos específicos como biologia, física, radiologia, nomes de produto e acrônimos personalizados exigem um modelo de idioma.

**Pergunta**: onde iniciar se desejo usar um modelo de linha de base?

**Resposta**: primeiro você precisa obter um [chave de assinatura](get-started.md). Se você quiser fazer chamadas REST para os modelos de linha de base predeployed, consulte o [detalhes aqui](rest-apis.md). Se você quiser usar o download de WebSockets do [SDK](speech-sdk.md)

**Pergunta**: fazer sempre necessário criar um modelo personalizado de fala?

**Answer**: Não, se seu aplicativo estiver usando linguagem genérica do dia a dia sem vocabulário personalizado ou que talvez seja raro, não será necessário personalizar um modelo. Além disso, se seu aplicativo for a ser usado em um ambiente onde há pouca ou nenhuma ruídos de fundo, em seguida, você não precisa personalizar qualquer um. O portal permite aos usuários implantar modelos personalizados e linha de base e executar testes de precisão em relação a elas. Os usuários podem usar esse recurso para medir a precisão de uma linha de base versus um modelo personalizado.

**Pergunta**: como saber quando o processamento do meu conjunto de dados ou o modelo está concluído?

**Answer**: Atualmente, o status do modelo ou conjunto de dados na tabela é o único que você deseja saber.
Quando o processamento for concluído, o status será "Pronto".

**Pergunta**: posso criar mais de um modelo em um momento?

**Resposta**: não há nenhum limite para quantas modelos estão em sua coleção, mas apenas um pode ser criado por vez em cada página.
Por exemplo, você não pode iniciar um processo de criação de modelo de idioma se houver atualmente um modelo de idioma no estágio do processo.
No entanto, você pode ter um modelo acústico e um modelo de idioma de processamento ao mesmo tempo. 

**Pergunta**: eu percebida fiz um erro. Como cancelar a importação de dados ou modelo de criação em andamento? 

**Resposta**: no momento você não pode reverter um processo de adaptação acústico ou idioma.
Dados importados podem ser excluídos após a conclusão da importação

**Pergunta**: qual é a diferença entre a pesquisa & ditado modelos e os modelos de conversação?

**Resposta**: há duas acústico Base e modelos de idioma de escolha no serviço de voz personalizada.
consultas de pesquisa ou ditado. O Microsoft Conversational AM é apropriado para reconhecer a fala falada em um estilo conversacional.
Esse tipo de fala normalmente é direcionado a outra pessoa e ocorre em call centers ou em reuniões.

**Pergunta**: posso atualizar meu modelo existente (modelo de empilhamento)?

**Resposta**: não possível atualizar os modelos existentes. Como uma solução alternativa combinar o conjunto de dados antigo com o novo e readapt.

Os conjuntos de dados antigos e novos devem ser combinados em um único .zip (se for dados acústicos) ou em um arquivo .txt, se forem dados de idioma. Uma vez adaptação é feita o novo modelo atualizado precisa ser implantado eliminação para obter um novo ponto de extremidade

**Pergunta**: E se eu precisar simultaneidade maior que o valor padrão ou o que é oferecido no portal. 

**Resposta**: você pode dimensionar o modelo em incrementos de 20 solicitações simultâneas. 

Entre em contato conosco se você precisar de maior escala.

**Pergunta**: Posso baixar meu modelo e executá-lo localmente?

**Resposta**: modelos não podem ser baixados e executados localmente.

**Pergunta**: Minhas solicitações registrados?

**Resposta**: você tem uma opção durante a criação de uma implantação de desativar o rastreamento, no ponto em que nenhum áudio ou transcrições serão registradas. Caso contrário, as solicitações geralmente são registradas no Azure em um armazenamento seguro. Se você tiver mais problemas de privacidade que o impedem de usar o serviço de voz personalizada, entre em contato com um dos canais de suporte.

## <a name="importing-data"></a>Importando dados

**Pergunta**: qual é o limite no tamanho do conjunto de dados? Por quê? 

**Resposta**: O limite atual de um conjunto de dados é de 2 GB, devido a restrições quanto ao tamanho de um arquivo para upload HTTP. 

**Pergunta**: eu zip Meus arquivos de texto para carregar um arquivo de texto maior? 

**Resposta**: nenhum, no momento são permitidos apenas os arquivos de texto não compactado.

**Pergunta**: O relatório de dados diz que houve falha utterances. Qual é o problema?

**Resposta**: Falha ao carregar a 100% de utterances em um arquivo não é um problema.
Se definir a maioria das utterances em um dado acústico ou idioma (por exemplo, > 95%) com êxito são importados, o conjunto de dados pode ser usado. No entanto, é recomendável que você tente entender por que o utterances falha e corrija os problemas. Os problemas mais comuns, como a formatação de erros, são difíceis de resolver. 

## <a name="creating-am"></a>Criando AM

**Pergunta**: Quantos dados acústicos são necessários?

**Resposta**: recomendamos começar de 30 minutos a uma hora de dados acústicos

**Pergunta**: Quais dados devo coletar?

**Resposta**: Colete dados que sejam o mais próximos possível do cenário do aplicativo e do caso de uso.
A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivo ou dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados de uma variedade de falantes o mais ampla possível. 

**Pergunta**: Como devo coletar isso? 

**Resposta**: Você pode criar um aplicativo de coleta de dados independente ou usar algum software de gravação de áudio de prateleira.
Você também pode criar uma versão do seu aplicativo que registre os dados de áudio e os use. 

**Pergunta**: preciso transcrever dados de adaptação? 

**Resposta**: Os dados devem ser transcritos. Você pode transcrever você mesmo ou usar um serviço profissional de transcrição. Alguns deles usam transcritores profissionais e outros usam crowdsourcing.

**Pergunta**: Quanto tempo leva para criar um modelo acústico personalizado?

**Resposta**: O tempo de processamento para criar um modelo acústico personalizado é aproximadamente o mesmo que o comprimento do conjunto de dados acústico.
Assim, um modelo acústico personalizado criado a partir de um conjunto de dados de cinco horas levará cerca de cinco horas para ser processado. 

## <a name="offline-testing"></a>Teste offline

**Pergunta**: Posso realizar testes off-line do meu modelo acústico personalizado usando um modelo de idioma personalizado?

**Resposta**: Sim, basta selecionar o modelo de idioma personalizado no menu suspenso ao configurar o teste off-line

**Pergunta**: Posso realizar o teste off-line do meu modelo de idioma personalizado usando um modelo acústico personalizado?

**Resposta**: Sim, basta selecionar o modelo acústico personalizado no menu suspenso ao configurar o teste off-line.

**Pergunta**: Qual é a taxa de erros da palavra e como ela é calculada?

**Resposta**: A taxa de erros do Word é a métrica de avaliação para o reconhecimento de voz. É contado como o número total de erros, o que inclui inserções, exclusões e substituições, dividido pelo número total de palavras na transcrição de referência.

**Pergunta**: Como determino se os resultados de um teste de precisão são bons?

**Resposta**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo personalizado.
Você deve tentar vencer o modelo de referência para tornar a personalização valiosa

**Pergunta**: Como descubro o WER dos modelos básicos, para que eu possa ver se houve melhorias? 

**Resposta**: The offline test results show accuracy of baseline accuracy of the custom model and the improvement over baseline

## <a name="creating-lm"></a>Criando LM

**Pergunta**: Quantos dados de texto preciso enviar?

**Resposta**: Depende de quão diferentes o vocabulário e as frases usadas na sua aplicação são dos modelos de idioma de partida. Para todas as novas palavras, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluir frases nos dados do idioma também é útil, pois informa ao sistema para ouvir esses termos também. É comum ter pelo menos uma 100 e tipicamente várias centenas de elocuções no conjunto de dados da linguagem ou mais. Além disso, se determinados tipos de consultas forem mais comuns do que outros, você poderá inserir várias cópias das consultas comuns no conjunto de dados.

**Pergunta**: posso apenas carregar uma lista de palavras?

**Resposta**: Fazer upload de uma lista de palavras colocará as palavras no vocabulário, mas não ensinará ao sistema como as palavras são normalmente usadas.
Ao fornecer declarações completas ou parciais (frases ou frases de coisas que os usuários provavelmente dirão), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizada é bom não apenas para obter novas palavras no sistema, mas também para ajustar a probabilidade de palavras conhecidas para sua aplicação. Fornecer declarações completas ajuda o sistema a aprender melhor. 

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
