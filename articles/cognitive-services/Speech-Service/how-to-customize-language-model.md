---
title: Como criar um modelo de linguagem com o Serviço de Fala – Serviços Cognitivos da Microsoft | Microsoft Docs
description: Aprenda como criar um modelo de linguagem com o Serviço de Fala nos Serviços Cognitivos da Microsoft.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 85e67be406b3d9723476821adfb09fc4db8dc1d1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068566"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutorial: Criar um modelo de linguagem personalizado

Neste documento você criará um modelo de linguagem personalizado que você poderá usar em conjunto com os modelos de fala existentes e de última geração da Microsoft para adicionar a interação de voz ao seu aplicativo.

O documento discute como:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados de linguagem
> * Criar o modelo de linguagem personalizado

Se você não tiver uma conta dos Serviços Cognitivos, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se sua conta de Serviços Cognitivos está conectada a uma assinatura abrindo a página [Assinaturas de Serviços Cognitivos](https://customspeech.ai/Subscriptions).

Você pode se conectar a uma assinatura do Serviço de Fala criada no portal do Azure clicando no botão **Conectar-se a uma assinatura existente**.

Para obter informações sobre como criar uma assinatura do Serviço de Fala no portal do Azure, consulte a página de [introdução](get-started.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para criar um modelo de linguagem personalizado para seu aplicativo, você precisa fornecer uma lista de enunciados de exemplo para o sistema, por exemplo:

*   “o paciente teve urticaria na semana passada.”
*   “O paciente teve uma cicatriz de herniorrafia bem curada.”

As frases não precisam ser frases completas ou estar gramaticalmente corretas e devem refletir com precisão a entrada falada que o sistema deve encontrar na implantação. Esses exemplos devem refletir o estilo e o conteúdo da tarefa que os usuários executarão com seu aplicativo.

Os dados de modelo de linguagem devem ser escritos em BOM para UTF-8. O arquivo de texto deve conter um exemplo (frase, enunciado ou consulta) por linha.

Se desejar que alguns termos tenham um peso mais alto (importância), você pode adicionar vários enunciados que incluam esse termo aos seus dados. 

Os principais requisitos para os dados de linguagem estão resumidos na tabela a seguir.

| Propriedade | Valor |
|----------|-------|
| Codificação de Texto | BOM para UTF-8|
| n° de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 1.5 GB |
| Comentários | evite repetir os caracteres mais frequentemente do que quatro vezes, por exemplo “aaaaa”|
| Comentários | não há caracteres especiais, como '\t' ou outros caracteres UTF-8 acima de U+00A1 em [tabela de caracteres Unicode](http://www.utf8-chartable.de/)|
| Comentários | URIs também serão rejeitados porque não há uma única forma para pronunciar um URI|

Quando o texto é importado, o texto é normalizado para poder ser processado pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados. Consulte as [Diretrizes de transcrição](prepare-transcription.md) para determinar a linguagem apropriada ao preparar seus dados de linguagem.

## <a name="language-support"></a>Suporte ao idioma

Os idiomas a seguir têm suporte para modelos de linguagem de **Conversão de fala em texto** personalizados.

Clique para obter uma lista completa de [linguagens com suporte](supported-languages.md)

## <a name="import-the-language-data-set"></a>Importar o conjunto de dados de linguagem

Clique no botão “Importar” na linha “Conjuntos de dados de linguagem”; o site exibirá uma página para carregar um novo conjunto de dados.

Quando você estiver pronto para importar seu conjunto de dados de linguagem, faça logon na [Portal de Serviço de Fala](https://customspeech.ai).  Depois, clique no menu suspenso de “Fala Personalizada” na faixa de opções e selecione “Dados de Adaptação”. Na primeira tentativa de carregar os dados para o Serviço de Fala, você verá uma tabela vazia chamada “Conjuntos de dados”.

Para importar um novo conjunto de dados, clique no botão “Importar” na linha “Conjuntos de dados de linguagem”; o site exibirá uma página para carregar um novo conjunto de dados. Insira um Nome e uma Descrição para ajudar a identificar o conjunto de dados no futuro e escolha a localidade. Em seguida, use o botão “Escolher Arquivo” para localizar o arquivo de texto dos dados de linguagem. Depois disso, clique em “Importar” e o conjunto de dados será carregado. Dependendo do tamanho do conjunto de dados, a importação pode levar vários minutos.

![experimentar](media/stt/speech-language-datasets-import.png)

Quando a importação for concluída, você retornará para a tabela de dados de linguagem e verá uma entrada que corresponde ao seu conjunto de dados de linguagem. Observe que uma id exclusiva (GUID) foi atribuída a ela. Os dados também terão um status que reflete o estado atual. O status será: “Aguardando”, enquanto estiver na fila para processamento; “Processando”, durante a validação; e “Concluído”, quando os dados estiverem prontos para uso. A validação de dados executa uma série de verificações sobre o texto no arquivo e algumas normalizações de texto dos dados.

Quando o status for “Concluído”, você pode clicar em “Exibir Relatório” para ver o relatório de verificação dos dados de linguagem. O número de enunciados que passaram ou falharam na verificação é exibido, juntamente com detalhes sobre os enunciados com falha. No exemplo a seguir, dois exemplos falharam na verificação devido a caracteres incorretos (nesse conjunto de dados, a primeira linha tinha dois caracteres TAB e a segunda tinha vários caracteres fora do conjunto de caracteres imprimíveis ASCII, já a terceira linha estava em branco).

![experimentar](media/stt/speech-language-datasets-report.png)

Quando o status do conjunto de dados de linguagem for “Concluído”, ele pode ser usado para criar um modelo de linguagem personalizado.

![experimentar](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Criar um modelo de linguagem personalizado

Quando seus dados de linguagem estiverem prontos, clique em “Modelos de Linguagem” no menu suspenso “Menu” para iniciar o processo de criação de modelo de linguagem personalizado. Esta página contém uma tabela chamada “Modelos de Linguagem” com seus modelos personalizados de linguagem atual. Se você ainda não tiver criado nenhum modelo de linguagem personalizado, a tabela estará vazia. A localidade atual é mostrada na tabela ao lado da entrada de dados relevantes.

A localidade apropriada deve ser selecionada antes de executar qualquer ação. A localidade atual é indicada no título da tabela em todas as páginas de dados, modelo e implantação. Para alterar a localidade, clique no botão "Alterar Localidade", localizado abaixo do título da tabela que levará você para a página e confirmação de localidade. Clique em “OK” para retornar à tabela.

Na página “Criar o Modelo de Linguagem”, insira um “Nome” e uma “Descrição” para ajudar a manter o controle das informações pertinentes sobre esse modelo, como o conjunto de dados usado. Em seguida, selecione o “Modelo de Linguagem Base” no menu suspenso. Esse modelo será o ponto de partida para sua personalização. Há dois modelos de linguagem base para escolher. O modelo Pesquisa e Ditado é apropriado para fala dirigida a um aplicativo, como comandos, consultas de pesquisa ou ditado. O modelo de Conversação é apropriado para reconhecer a fala falada em um estilo conversacional. Esse tipo de fala normalmente é direcionado a outra pessoa e ocorre em call centers ou em reuniões. Um novo modelo chamado “Universal” também está disponível publicamente. O modelo Universal tem como objetivo lidar com todos os cenários e, eventualmente, substituir os modelos de Pesquisa e Ditado e os modelos de Conversação.

5.  No exemplo abaixo, depois de ter especificado o modelo de linguagem base, selecione o conjunto de dados de linguagem que você deseja usar para a personalização usando o menu suspenso “Dados de Linguagem”

![experimentar](media/stt/speech-language-models-create2.png)

Assim como acontece com a criação de modelo acústico, você pode optar por executar testes offline de seu novo modelo quando o processamento for concluído. As avaliações de modelo exigem um conjunto de dados acústicos.

Para executar o teste offline do seu modelo de linguagem, selecione a caixa de seleção ao lado de “Teste Offline”. Depois, selecione um modelo acústico no menu suspenso. Caso não tenha criado nenhum modelo acústico personalizado, os modelos acústicos base da Microsoft serão os únicos modelos no menu. Caso tenha selecionado um modelo básico de LM conversacional, você precisa usar aqui um AM conversacional. Caso você use uma pesquisa e dite o modelo de LM, você deve selecionar uma pesquisa e ditar o modelo de AM.

Por fim, selecione o conjunto de dados acústicos que você deseja usar para executar a avaliação.

Quando você estiver pronto para iniciar o processamento, pressione “Criar”, que o levará para a tabela de modelos de linguagem. Haverá uma nova entrada na tabela correspondente a esse modelo. O status reflete o estado do modelo e passará por vários estados, incluindo “Aguardando”, “Processando” e “Concluído”.

Quando o modelo tiver alcançado o estado de “Concluído”, ele pode ser implantado para um ponto de extremidade. Clicar em “Exibir Resultado” mostrará os resultados de teste offline, se executado.

Caso queira alterar o “Nome” ou a “Descrição” do modelo em algum momento, você pode usar o link “Editar” na linha apropriada da tabela de modelos de linguagem.

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de fala](https://azure.microsoft.com/try/cognitive-services/)
- [Como reconhecer fala no C#](quickstart-csharp-dotnet-windows.md)
- [Dados de exemplo do Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
