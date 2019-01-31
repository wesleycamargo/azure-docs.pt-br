---
title: 'Tutorial: Como criar um modelo de linguagem com o Serviço de Fala'
titlesuffix: Azure Cognitive Services
description: Saiba como criar um modelo de linguagem com o Serviço de Fala. Use esse modelo de linguagem personalizado em conjunto com os modelos de fala existentes e de última geração da Microsoft para adicionar a interação de voz ao seu aplicativo.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: bd7b957a42f28b7ded2f30ec22a7450e0106b09a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206181"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutorial: Criar um modelo de linguagem personalizado

Neste documento, você cria um modelo de linguagem personalizado. Você pode, então, usar esse modelo de linguagem personalizado em conjunto com os modelos de fala existentes e de última geração da Microsoft para adicionar a interação de voz ao seu aplicativo.

O documento discute como:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados de linguagem
> * Criar o modelo de linguagem personalizado

Se você não tiver uma conta dos Serviços Cognitivos, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para garantir que sua conta de Serviços Cognitivos esteja conectada a uma assinatura, abra a página [Assinaturas de Serviços Cognitivos](https://customspeech.ai/Subscriptions).

Para se conectar a uma assinatura de Serviços de Fala criada no portal do Azure, selecione o botão **Conectar a assinatura existente**.

Para obter informações sobre como criar uma assinatura do Serviço de Fala no portal do Azure, veja a página de [Introdução](get-started.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para criar um modelo de linguagem personalizado para seu aplicativo, você precisa fornecer uma lista de enunciados de exemplo para o sistema, por exemplo:

*   "O paciente teve urticaria na semana passada."
*   “O paciente teve uma cicatriz de herniorrafia bem curada.”

As frases não precisam estar completas nem gramaticalmente corretas, mas devem refletir com precisão a entrada falada que o sistema deve encontrar na implantação. Esses exemplos devem refletir o estilo e o conteúdo da tarefa que os usuários executarão com seu aplicativo.

Os dados de modelo de linguagem devem ser escritos em BOM para UTF-8. O arquivo de texto deve conter um exemplo (frase, enunciado ou consulta) por linha.

Se você quiser que determinados termos tenham um peso mais alto (importância), poderá adicionar aos seus dados várias declarações que incluam esses termos.

Os principais requisitos para os dados de linguagem estão resumidos na tabela a seguir.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | BOM para UTF-8|
| Nº de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 1.5 GB |
| Comentários | Evite repetir os caracteres com uma frequência maior que quatro vezes, por exemplo, "aaaaa"|
| Comentários | Não use caracteres especiais, como '\t' ou outros caracteres UTF-8 acima de U+00A1 na [tabela de caracteres Unicode](http://www.utf8-chartable.de/)|
| Comentários | URIs também serão rejeitados porque não há uma única forma para pronunciar um URI|

Quando o texto é importado, o texto é normalizado para poder ser processado pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados. Veja as [diretrizes de transcrição](prepare-transcription.md) para determinar a linguagem apropriada a ser usada ao preparar seus dados de linguagem.

## <a name="language-support"></a>Suporte ao idioma

Veja a lista completa de [idiomas com suporte](language-support.md#text-to-speech) para modelo de linguagem de **Conversão de Fala em Texto** personalizados.



## <a name="import-the-language-data-set"></a>Importar o conjunto de dados de linguagem

Selecione o botão **Importar** na linha **Conjuntos de Dados de Linguagem**; o site exibirá uma página para carregar um novo conjunto de dados.

Quando você estiver pronto para importar seu conjunto de dados de linguagem, faça logon no [portal de Serviço de Fala](https://customspeech.ai). Primeiro, selecione o menu suspenso **Fala Personalizada** na faixa de opções superior. Em seguida, selecione **Dados de Adaptação**. Na primeira vez que você tentar fazer upload dados para Serviços de Fala, verá uma tabela vazia chamada **Conjuntos de Dados**.

Para importar um novo conjunto de dados, selecione o botão **Importar** na linha **Conjuntos de Dados de Linguagem**. Em seguida, o site exibe uma página para carregar um novo conjunto de dados. Insira um **Nome** e uma **Descrição** para ajudar a identificar o conjunto de dados no futuro e então escolha a localidade.

Em seguida, use o botão **Escolher Arquivo** para localizar o arquivo de texto dos dados de linguagem. Depois disso, clique em **Importar** e o conjunto de dados será carregado. Dependendo do tamanho do conjunto de dados, a importação pode levar vários minutos.

![Experimente](media/stt/speech-language-datasets-import.png)

Depois que a importação for concluída, os dados de linguagem terão uma entrada correspondente ao seu conjunto de dados de linguagem. Observe que um GUID (ID) exclusivo foi atribuído a ela. Os dados também têm um status que reflete o estado atual. O status será **Aguardando** enquanto eles estiverem na fila para processamento; **Processando** durante a validação; e **Concluído** quando os dados estiverem prontos para uso. A validação de dados executa uma série de verificações no texto no arquivo. Também realiza alguma normalização de texto nos dados.

Quando o status for **Concluído**, você pode selecionar **Exibir Relatório** para ver o relatório de verificação dos dados de linguagem. O número de enunciados que passaram ou falharam na verificação é exibido, juntamente com detalhes sobre os enunciados com falha. No exemplo a seguir, dois exemplos falharam na verificação devido a caracteres incorretos. (Neste conjunto de dados, a primeira linha tinha dois caracteres de tabulação, a segunda tinha vários caracteres que não faziam parte do conjunto de caracteres imprimível ASCII e a terceira linha estava em branco).

![Experimente](media/stt/speech-language-datasets-report.png)

Quando o status do conjunto de dados de linguagem for **Concluído**, ele pode ser usado para criar um modelo de linguagem personalizado.

![Experimente](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Criar um modelo de linguagem personalizado

Depois que seus dados de linguagem estiverem prontos, clique em **Modelos de Linguagem** no menu suspenso **Menu** para iniciar o processo de criação de modelo de linguagem personalizado. Esta página contém uma tabela chamada **Modelos de Linguagem** com seus atuais modelos de linguagem personalizados. Se você ainda não tiver criado nenhum modelo de linguagem personalizado, a tabela estará vazia. A localidade atual é mostrada na tabela ao lado da entrada de dados relevantes.

A localidade apropriada deve ser selecionada antes de executar qualquer ação. A localidade atual é indicada no título da tabela em todas as páginas de dados, modelo e implantação. Para alterar a localidade, selecione o botão **Alterar Localidade** que está localizado sob o título da tabela.  Isso o leva para uma página de confirmação de localidade. Selecione **OK** para retornar à tabela.

Na página Criar Modelo de Linguagem, insira um **Nome** e uma **Descrição** para ajudar a manter o controle das informações pertinentes sobre esse modelo, como o conjunto de dados usado. Em seguida, selecione o **Modelo de Linguagem Base** no menu suspenso. Esse modelo é o ponto de partida para sua personalização.

Há dois modelos de linguagem base para escolher. O modelo Pesquisa e Ditado é apropriado para fala dirigida a um aplicativo, como comandos, consultas de pesquisa ou ditado. O modelo Conversacional é apropriado para reconhecimento de fala que é falada em um estilo conversacional. Esse tipo de fala normalmente é direcionado a outra pessoa e ocorre em call centers ou em reuniões. Um novo modelo chamado "Universal" também está disponível ao público geral. O modelo Universal tem como objetivo lidar com todos os cenários e, eventualmente, substituir os modelos de Pesquisa e Ditado e os modelos de Conversação.

Conforme mostrado no exemplo a seguir, depois de especificar o modelo de linguagem de base, use o menu suspenso **Dados de Linguagem** para selecionar o conjunto de dados de linguagem que você deseja usar para a personalização.

![Experimente](media/stt/speech-language-models-create2.png)

Assim como acontece com a criação de modelo acústico, você pode optar por executar teste offline de seu novo modelo quando o processamento for concluído. As avaliações de modelo exigem um conjunto de dados acústicos.

Para executar o teste offline do seu modelo de linguagem, selecione a caixa de seleção ao lado de **Teste Offline**. Depois, selecione um modelo acústico no menu suspenso. Caso não tenha criado nenhum modelo acústico personalizado, os modelos acústicos base da Microsoft serão os únicos modelos no menu. Se tiver selecionado um modelo básico de LM conversacional, você precisará usar aqui um AM conversacional. Se você usar uma pesquisa e dite o modelo de LM, deverá selecionar uma pesquisa e ditar o modelo de AM.

Por fim, selecione o conjunto de dados acústico que você deseja usar para fazer a avaliação.

Quando estiver pronto para iniciar o processamento, selecione **Criar**. Em seguida, você verá a tabela de modelos de linguagem. Haverá uma nova entrada na tabela correspondente a esse modelo. O status reflete o estado do modelo e passará por vários estados, incluindo **Aguardando**, **Processando** e **Concluído**.

Quando o modelo tiver alcançado o estado de **Concluído**, ele poderá ser implantado para um ponto de extremidade. Selecionar **Exibir Resultado** mostra os resultados de teste offline, se você o tiver executado.

Caso queira alterar o **Nome** ou a **Descrição** do modelo em algum momento, poderá usar o link **Editar** na linha apropriada da tabela de modelos de linguagem.

## <a name="next-steps"></a>Próximas etapas

- [Obter sua assinatura de avaliação de Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Como reconhecer fala no C#](quickstart-csharp-dotnet-windows.md)
- [Dados de exemplo do Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
