---
title: Perguntas frequentes sobre o Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Aqui estão as respostas para as perguntas mais populares sobre o Serviço de Fala Personalizada.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: 8e3d5e0e2b70d8f97099103ed369e48dd74d56e2
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341354"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Perguntas Frequentes sobre o Serviço de Fala Personalizada

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade do Serviço de Fala Personalizada em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Geral

**Pergunta**: Como saberei quando o processamento do meu conjunto de dados ou modelo está completo?

**Resposta**: Atualmente, o status do modelo ou conjunto de dados na tabela é o único que você deseja saber.
Quando o processamento estiver concluído, o status será "Pronto".
Estamos trabalhando em métodos aprimorados para status de processamento de comunicação, como notificação por email.

**Pergunta**: Posso criar mais de um modelo por vez?

**Resposta**: Não há limite para quantos modelos estão em sua coleção, mas apenas um pode ser criado por vez em cada página.
Por exemplo, você não pode iniciar um processo de criação de modelo de idioma se houver atualmente um modelo de idioma no estágio do processo.
Você pode, no entanto, ter um modelo acústico e um processamento de modelo de linguagem ao mesmo tempo. 

**Pergunta**: Percebi que cometi um erro. Como cancelar a importação de dados ou modelo de criação em andamento? 

**Resposta**: Atualmente você não pode reverter um processo de adaptação acústica ou de linguagem.
Dados importados podem ser excluídos após a conclusão da importação

**Pergunta**: Qual é a diferença entre os modelos pesquisa & ditado e os modelos de conversação?

**Resposta**: Há dois modelos base acústico e de linguagem para escolher no Serviço de Fala Personalizada.
consultas de pesquisa ou ditado. O Microsoft Conversational AM é apropriado para reconhecer a fala falada em um estilo conversacional.
Esse tipo de fala normalmente é direcionado a outra pessoa e ocorre em call centers ou em reuniões.

**Pergunta**: Posso atualizar meu modelo existente (modelo de empilhamento)?

**Resposta**: Não oferecemos a capacidade de atualizar um modelo existente com novos dados.
Se você tiver um novo conjunto de dados e deseja personalizar um modelo existente, você deve adaptá-lo novamente com os novos dados e o conjunto de dados antigos que você usou.
Os conjuntos de dados novos e antigos devem ser combinados em um único arquivo .zip (se forem dados acústicos) ou um arquivo .txt, se for dados de linguagem. Após feita a adaptação, o novo modelo atualizado precisa ser implantado novamente para obter um novo ponto de extremidade

**Pergunta**: E se eu precisar simultaneidade maior que o valor padrão. 

**Resposta**: Você pode dimensionar o modelo em incrementos de 5 solicitações simultâneas que chamamos de unidade de escala. Cada unidade de escala garante que o seu modelo pode processar 5 fluxos de áudio simultaneamente. Você pode comprar 100 unidades de escala (ou 500 solicitações simultâneas).

Entre em contato conosco se você precisar de mais que isso.

**Pergunta**: Posso baixar meu modelo e executá-lo localmente?

**Resposta**: Não podemos habilitar modelos para serem baixados e executados localmente.

**Pergunta**: Minhas solicitações são registradas?

**Resposta**: Você tem uma opção durante a criação de uma implantação de desativar o rastreamento, neste momento nenhum áudio ou transcrições serão registradas. Caso contrário, as solicitações normalmente são registradas no Azure no armazenamento seguro. Se você tiver mais problemas de privacidade que o impedem de usar o Serviço de Fala Personalizada, entre em contato conosco.

## <a name="importing-data"></a>Importação de dados

**Pergunta**: Qual é o limite no tamanho do conjunto de dados? Por quê? 

**Resposta**: O limite atual de um conjunto de dados é de 2 GB, devido a restrições quanto ao tamanho de um arquivo para upload HTTP. 

**Pergunta**: Posso compactar meus arquivos de texto para carregar um arquivo de texto maior? 

**Resposta**: Não, no momento são permitidos apenas os arquivos de texto não compactados.

**Pergunta**: O relatório de dados diz que houve falha de expressões. Isso é um problema?

**Resposta**: Se apenas algumas expressões não puderam ser importadas com êxito, isso não é um problema.
Se definir a maioria das expressões em um conjunto de dados acústico ou de linguagem (por exemplo, > 95%) são importados com êxito, o conjunto de dados pode ser usado. No entanto, é recomendável que você tente entender por que as expressões falharam e corrigir os problemas.
Os problemas mais comuns, como a formatação de erros, são difíceis de resolver. 

## <a name="creating-am"></a>Criar AM

**Pergunta**: Quantos dados acústicos são necessários?

**Resposta**: Recomendamos começar de 30 minutos a uma hora de dados acústicos

**Pergunta**: Que tipo de dados devo coletar?

**Resposta**: Você deve coletar dados que sejam o mais próximos possível do cenário do aplicativo e do caso de uso.
Isto significa que a coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivo ou dispositivos, ambientes e tipos de falantes. Em geral, você deve coletar dados de uma variedade de falantes o mais ampla possível. 

**Pergunta**: Como devo coletar isso? 

**Resposta**: Você pode criar um aplicativo de coleta de dados independente ou usar algum software de gravação de áudio da plataforma.
Você também pode criar uma versão do seu aplicativo que registre os dados de áudio e usá-los. 

**Pergunta**: Preciso transcrever dados de adaptação? 

**Resposta**: Os dados devem ser transcritos. Você pode transcrever você mesmo ou usar um serviço profissional de transcrição. Alguns deles usam transcritores profissionais e outros usam crowdsourcing. Podemos recomendar também um serviço de transcrição mediante solicitação.

**Pergunta**: Quanto tempo leva para criar um modelo acústico personalizado?

**Resposta**: O tempo de processamento para criar um modelo acústico personalizado é aproximadamente o mesmo que o comprimento do conjunto de dados acústico.
Portanto, um modelo acústico personalizado criado a partir de um conjunto de dados de cinco horas levará cerca de cinco horas para ser processado. 

## <a name="offline-testing"></a>Teste offline

**Pergunta**: Posso realizar testes off-line do meu modelo acústico personalizado usando um modelo de linguagem personalizado?

**Resposta**: Sim, basta selecionar o modelo de linguagem personalizado no menu suspenso ao configurar o teste off-line

**Pergunta**: Posso realizar o teste off-line do meu modelo de linguagem personalizado usando um modelo acústico personalizado?

**Resposta**: Sim, basta selecionar o modelo acústico personalizado no menu suspenso ao configurar o teste off-line.

**Pergunta**: Qual é a taxa de erros de palavra e como ela é calculada?

**Resposta**: A taxa de erros de palavra é a métrica de avaliação para o reconhecimento de fala. É contado como o número total de erros, o que inclui inserções, exclusões e substituições, dividido pelo número total de palavras na transcrição de referência.

**Pergunta**: Agora sei os resultados do texto do meu modelo personalizado, isso é um número bom ou ruim?

**Resposta**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo personalizado.
Você deve tentar atingir o modelo de referência para que a personalização seja útil

**Pergunta**: Como descubro o WER dos modelos básicos, para que eu possa ver se houve melhorias? 

**Resposta**: Os resultados do teste off-line mostram a exatidão da precisão da linha de base do modelo personalizado e a melhoria em relação à linha de base

## <a name="creating-lm"></a>Criar LM

**Pergunta**: Qual quantidade de dados de texto é necessário carregar?

**Resposta**: Esta é uma pergunta difícil de dar uma resposta precisa, pois depende de quão diferentes são o vocabulário e as frases usadas na sua aplicação dos modelos de linguagem de partida. Para todas as palavras novas, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, também é útil incluir essas nos dados de linguagem, pois informa ao sistema para ouvir esses termos também.
É comum ter pelo menos 100 e normalmente várias centenas de expressões ou mais no conjunto de dados de linguagem.
Também se determinados tipos * de consultas são mais comuns do que outras pessoas, você pode inserir várias cópias das consultas comuns no conjunto de dados.

**Pergunta**: Posso carregar apenas uma lista de palavras?

**Resposta**: Carregar uma lista de palavras levará as palavras para o vocabulário mas não ensina o sistema como as palavras são usadas normalmente.
Ao fornecer expressões completas ou parciais (orações ou frases de coisas que os usuários provavelmente dirão), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizada é bom não apenas para obter novas palavras no sistema, mas também para ajustar a probabilidade de palavras conhecidas para sua aplicação. Fornecer expressões completas ajuda o sistema a aprender isso. 

-----

 * [Visão geral](cognitive-services-custom-speech-home.md)
 * [Iniciado](cognitive-services-custom-speech-get-started.md)
 * [Glossário](cognitive-services-custom-speech-glossary.md)
