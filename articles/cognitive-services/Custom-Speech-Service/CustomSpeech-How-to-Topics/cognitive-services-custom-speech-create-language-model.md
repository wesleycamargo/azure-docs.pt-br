---
title: Tutorial para criar um modelo de linguagem com o Serviço de Fala Personalizado – Serviços Cognitivos da Microsoft | Microsoft Docs
description: Neste tutorial, você aprenderá como criar um modelo de linguagem com o Serviço de Fala Personalizado nos Serviços Cognitivos da Microsoft.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 599302cbf614f800d35a9a8c6a401c9692fc2e39
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268273"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutorial: criar um modelo de linguagem personalizado

Neste tutorial, você criará um modelo de linguagem personalizado para consultas de texto ou enunciados que você espera que os usuários digam ou digitem em um aplicativo. Você pode, então, usar esse modelo de linguagem personalizado em conjunto com os modelos de fala existentes e de última geração da Microsoft para adicionar a interação de voz ao seu aplicativo.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados de linguagem
> * Criar o modelo de linguagem personalizado

Se você não tiver uma conta dos Serviços Cognitivos, crie uma [conta gratuita](https://cris.ai) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Verifique se sua conta de Serviços Cognitivos está conectada a uma assinatura abrindo a página [Assinaturas de Serviços Cognitivos](https://cris.ai/Subscriptions).

Se nenhuma assinatura estiver listada, você pode solicitar que os Serviços Cognitivos criem uma conta para você clicando no botão **Obter assinatura gratuita**. Ou você pode se conectar a uma assinatura do Serviço de Pesquisa Personalizado criada no portal do Azure clicando no botão **Conectar-se a uma assinatura existente**.

Para obter informações sobre como criar uma assinatura do Serviço de Pesquisa Personalizado no portal do Azure, consulte [Criar uma conta de API de Serviços Cognitivos no portal do Azure](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para criar um modelo de linguagem personalizado para seu aplicativo, você precisa fornecer uma lista de enunciados de exemplo para o sistema, por exemplo:

*   “Ele teve urticaria na semana passada.”
*   “O paciente teve uma cicatriz de herniorrafia bem curada.”

As frases não precisam ser frases completas ou estar gramaticalmente corretas e devem refletir com precisão a entrada falada que você espera que o sistema encontre na implantação. Esses exemplos devem refletir o estilo e o conteúdo da tarefa que os usuários executarão com seu aplicativo.

Os dados de modelo de linguagem devem ser gravados no arquivo de texto sem formatação usando a US-ASCII ou UTF-8, dependendo da localidade. Para en-US, há suporte para ambas as codificações. Para zh-CN, há suporte apenas para UTF-8 (BOM é opcional). O arquivo de texto deve conter um exemplo (frase, enunciado ou consulta) por linha.

Caso deseje que algumas frases tenham um peso mais alto (importância), você pode adicioná-la várias vezes para seus dados. Um bom número de repetições fica entre 10 e 100. Se você normalizá-la a 100, você pode ponderar a sentença em relação a isso facilmente.

Os principais requisitos para os dados de linguagem estão resumidos na tabela a seguir.

| Propriedade | Valor |
|----------|-------|
| Codificação de Texto | en-US: US-ACSII ou UTF-8 ou zh-CN: UTF-8|
| n° de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 200 MB |
| Comentários | evite repetir os caracteres mais frequentemente do que 4 vezes, por exemplo “aaaaa”|
| Comentários | não há caracteres especiais, como '\t' ou outros caracteres UTF-8 acima de U+00A1 em [tabela de caracteres Unicode](http://www.utf8-chartable.de/)|
| Comentários | URIs também serão rejeitados porque não há uma única forma para pronunciar um URI|

Quando o texto é importado, o texto é normalizado para poder ser processado pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados. Consulte as [Diretrizes de transcrição](cognitive-services-custom-speech-transcription-guidelines.md) para determinar a linguagem apropriada ao preparar seus dados de linguagem.

## <a name="import-the-language-data-set"></a>Importar o conjunto de dados de linguagem

Clique no botão “Importar” na linha “Conjuntos de dados acústicos”; o site exibirá uma página para carregar um novo conjunto de dados.

Quando você estiver pronto para importar seu conjunto de dados de linguagem, faça logon na [Portal de Serviço de Fala Personalizado](https://cris.ai).  Depois, clique no menu suspenso de “Fala Personalizada” na faixa de opções e selecione “Dados de Adaptação”. Se esta for a primeira vez fazendo o carregamento de dados para o Serviço de Fala Personalizado, você verá uma tabela vazia chamada “Conjuntos de dados”.

Para importar um novo conjunto de dados, clique no botão “Importar” na linha “Conjuntos de dados de linguagem”; o site exibirá uma página para carregar um novo conjunto de dados. Insira um Nome e uma Descrição para ajudar a identificar o conjunto de dados no futuro. Em seguida, use o botão “Escolher Arquivo” para localizar o arquivo de texto dos dados de linguagem. Depois disso, clique em “Importar” e o conjunto de dados será carregado. Dependendo do tamanho do conjunto de dados, isso pode levar vários minutos.

![experimentar](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Quando a importação for concluída, você retornará para a tabela de dados de linguagem e verá uma entrada que corresponde ao seu conjunto de dados de linguagem. Observe que uma id exclusiva (GUID) foi atribuída a ela. Os dados também terão um status que reflete o estado atual. O status será: “Aguardando”, enquanto estiver na fila para processamento; “Processando”, durante a validação; e “Concluído”, quando os dados estiverem prontos para uso. A validação de dados executa uma série de verificações sobre o texto no arquivo e algumas normalizações de texto dos dados.

Quando o status for “Concluído”, você pode clicar em “Exibir Relatório” para ver o relatório de verificação dos dados de linguagem. O número de enunciados que passaram ou falharam na verificação é exibido, juntamente com detalhes sobre os enunciados com falha. No exemplo a seguir, dois exemplos falharam na verificação devido a caracteres incorretos (nesse conjunto de dados, o primeiro tinha dois emoticons e o segundo tinha vários caracteres fora do conjunto de caracteres imprimíveis ASCII).

![experimentar](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Quando o status do conjunto de dados de linguagem for “Concluído”, ele pode ser usado para criar um modelo de linguagem personalizado.

![experimentar](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Criar um modelo de linguagem personalizado

Quando seus dados de linguagem estiverem prontos, clique em “Modelos de Linguagem” no menu suspenso “Menu” para iniciar o processo de criação de modelo de linguagem personalizado. Esta página contém uma tabela chamada “Modelos de Linguagem” com seus modelos personalizados de linguagem atual. Se você ainda não tiver criado nenhum modelo de linguagem personalizado, a tabela estará vazia. A localidade atual é refletida no título da tabela. Caso deseje criar um modelo de linguagem para uma linguagem diferente, clique em “Alterar Localidade”. Informações adicionais sobre linguagens com suporte podem ser encontradas na seção em [Alterar Localidade](cognitive-services-custom-speech-change-locale.md). Para criar um novo modelo, clique no link “Criar Novo” abaixo do título da tabela.

Na página “Criar o Modelo de Linguagem”, insira um “Nome” e uma “Descrição” para ajudar a manter o controle das informações pertinentes sobre esse modelo, como o conjunto de dados usado. Em seguida, selecione o “Modelo de Linguagem Base” no menu suspenso. Esse modelo será o ponto de partida para sua personalização. Há dois modelos de linguagem base para escolher. O _Microsoft Search e Dictation LM_ é apropriado para fala dirigida a um aplicativo, como comandos, consultas de pesquisa ou ditado. O _Microsoft Conversational LM_ é apropriado para o reconhecimento de fala em um estilo de conversação. Esse tipo de fala normalmente é direcionado a outra pessoa e ocorre em call centers ou em reuniões.

Depois de ter especificado o modelo de linguagem base, selecione o conjunto de dados de linguagem que você deseja usar para a personalização usando o menu suspenso “Dados de Linguagem”

![experimentar](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Assim como acontece com a criação de modelo acústico, você pode optar por executar testes offline de seu novo modelo quando o processamento for concluído. Como esta é uma avaliação do desempenho de conversão de fala em texto, o teste offline requer um conjunto de dados acústico.

Para executar o teste offline do seu modelo de linguagem, selecione a caixa de seleção ao lado de “Teste Offline”. Depois, selecione um modelo acústico no menu suspenso. Caso não tenha criado nenhum modelo acústico personalizado, os modelos acústicos base da Microsoft serão os únicos modelos no menu. Caso tenha selecionado um modelo básico de LM conversacional, você precisa usar aqui um AM conversacional. Caso você use uma pesquisa e dite o modelo de LM, você deve selecionar uma pesquisa e ditar o modelo de AM.

Por fim, selecione o conjunto de dados acústicos que você deseja usar para executar a avaliação.

Quando você estiver pronto para iniciar o processamento, pressione “Criar”. Isso levará você de volta para a tabela de modelos de linguagem. Haverá uma nova entrada na tabela correspondente a esse modelo. O status reflete o estado do modelo e passará por vários estados, incluindo “Aguardando”, “Processando” e “Concluído”.

Quando o modelo tiver alcançado o estado de “Concluído”, ele pode ser implantado para um ponto de extremidade. Clicar em “Exibir Resultado” mostrará os resultados de teste offline, se executado.

Caso queira alterar o “Nome” ou a “Descrição” do modelo em algum momento, você pode usar o link “Editar” na linha apropriada da tabela de modelos de linguagem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolveu um modelo de linguagem personalizado para usar com o texto. Para criar um modelo acústico personalizado para usar com arquivos de áudio e transcrições, continue o tutorial sobre como criar um modelo acústico.

> [!div class="nextstepaction"]
> [Criar um modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md)
