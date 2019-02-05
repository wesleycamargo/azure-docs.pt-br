---
title: 'Tutorial: Criar um modelo acústico com o Serviço de Fala'
titlesuffix: Azure Cognitive Services
description: Aprenda como criar um modelo acústico com o Serviço de Fala nos Serviços Cognitivos do Azure.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 8bee93c4bb932730000a06cc2bc3fe5a3e330a1f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217621"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Tutorial: Criar um modelo acústico personalizado

Criar um modelo acústico personalizado é útil se o aplicativo foi projetado para uso em um ambiente específico, como um carro, com condições ou dispositivos de gravação específicos ou pela população de determinado usuário. Os exemplos envolvem fala e ruídos de fundo específicos ou o uso de um microfone específico para gravação.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados acústico
> * Criar o modelo acústico personalizado

Se você não tiver uma conta dos Serviços Cognitivos do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se sua conta de Serviços Cognitivos está conectada a uma assinatura abrindo a página [Assinaturas de Serviços Cognitivos](https://cris.ai/Subscriptions).

Você pode se conectar a uma assinatura do Serviço de Fala criada no portal do Azure selecionando **Conectar-se a uma assinatura existente**.

Para obter informações sobre como criar uma assinatura do Serviço de Fala no portal do Azure, veja [Experimente o Serviço de Fala gratuitamente](get-started.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para personalizar um modelo acústico para um domínio específico, é necessário ter uma coleção de dados de fala. Essa coleção pode variar de dois enunciados a várias centenas de horas de fala. A coleção consiste em um conjunto de arquivos de áudio com os dados de fala e um arquivo de texto com transcrições de cada arquivo de áudio. Os dados de áudio devem ser representativos do cenário no qual você deseja usar o reconhecedor.

Por exemplo: 

* Se você quiser reconhecer melhor a fala em um ambiente de fábrica com ruídos, os arquivos de áudio deverão conter pessoas que falem em uma fábrica com ruídos.
* Se você estiver interessado em otimizar o desempenho de um único palestrante&mdash;por exemplo, você quer transcrever todos os chats fireside do FDR&mdash;os arquivos de áudio deverão consistir em muitos exemplos apenas dessa pessoa.

Um conjunto de dados acústicos para personalizar o modelo acústico é composto por duas partes: (1) um conjunto de arquivos de áudio que contém os dados de fala e (2) um arquivo que contém as transcrições de todos os arquivos de áudio.

### <a name="audio-data-recommendations"></a>Recomendações para os dados de áudio

* Todos os arquivos de áudio no conjunto de dados devem ser armazenados no formato de áudio WAV (RIFF).
* O áudio deve ter uma taxa de amostragem de 8 quilo-hertz ou 16 kHz e os valores de exemplo devem ser armazenados como inteiros com sinal de 16 bits (curtos) PCM (modulação de código de pulso) não compactados.
* Há suporte apenas para arquivos de áudio de um único canal (mono).
* Os arquivos de áudio podem ter entre 100 microssegundos e 1 minuto de comprimento, embora o ideal é que eles tenham cerca de 10 a 12 segundos. Cada arquivo de áudio deve, idealmente, começar e terminar com pelo menos 100 microssegundos de silêncio; é comum algo entre 500 microssegundos e 1 segundo.
* Se você tiver ruído de fundo em seus dados, recomendamos também ter alguns exemplos com segmentos mais longos de silêncio nos seus dados&mdash;por exemplo, alguns segundos&mdash;antes e/ou depois do conteúdo de fala.
* Cada arquivo de áudio deve consistir em um único enunciado&mdash;por exemplo, uma única frase para ditado, uma única consulta ou uma única rodada do sistema de diálogo.
* Cada arquivo de áudio no conjunto de dados deve ter um nome de arquivo exclusivo e uma extensão .wav.
* O conjunto de arquivos de áudio deve ser colocado em uma única pasta sem subdiretórios, e todo o conjunto de arquivos de áudio deve ser empacotado como um único arquivo .zip.

> [!NOTE]
> As importações de dados pelo portal da Web atualmente estão limitadas a 2 GB e, portanto, esse é o tamanho máximo de um conjunto de dados acústico. Esse tamanho corresponde a cerca de 17 horas de áudio gravadas a 16 kHz ou 34 horas de áudio gravadas a 8 kHz. Os principais requisitos para os dados de áudio estão resumidos na tabela a seguir:
>

| Propriedade | Valor |
|---------- |----------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostragem | 8.000 Hertz (Hz) ou 16.000 Hz |
| Canais | 1 (mono) |
| Formato de exemplo | PCM, inteiros de 16 bits |
| Duração do arquivo | 0,1 segundo < duração < 12 segundos | 
| Anel de silêncio | > 0,1 segundo |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2 GB |

> [!NOTE]
> Nomes de arquivo devem usar somente os caracteres latinos e seguir o formato 'filename.extention'

## <a name="language-support"></a>Suporte ao idioma

Para obter uma lista completa dos idiomas com suporte para modelos de linguagem personalizados de **Conversão de Fala em Texto**, veja [Idiomas com suporte para o Serviço de Fala](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Transcrições do conjunto de dados de áudio

As transcrições para todos os arquivos WAV devem estar contidas em um único arquivo de texto sem formatação. Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t).

  Por exemplo: 
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> A transcrição deve ser codificada como BOM (marca de ordem de byte) UTF-8.

As transcrições são normalizadas para texto para processamento pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados para o Serviço de Fala Personalizado. Para a linguagem apropriada a ser usada na preparação das transcrições, veja [Diretrizes de transcrição para usar o Serviço de Fala](prepare-transcription.md).

Execute as etapas nas próximas seções usando o [portal do Serviço de Fala](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importar o conjunto de dados acústico

Depois de ter preparado os arquivos de áudio e as transcrições, eles estarão prontos para serem importados para o portal da Web.

Para importá-los, primeiro verifique se você está conectado ao [portal do Serviço de Fala](https://cris.ai). Em seguida, na lista suspensa **Fala Personalizada** na faixa de opções, selecione **Dados de Adaptação**. Se esta for a primeira vez fazendo o carregamento de dados para o Serviço de Fala Personalizado, uma tabela vazia chamada **Conjuntos de dados** será exibida. 

Na linha **Conjuntos de Dados Acústicos**, selecione o botão **Importar** e o site exibirá uma página para carregar um novo conjunto de dados.

![A página Importar Dados Acústicos](media/stt/speech-acoustic-datasets-import.png)

Nas caixas **Nome** e **Descrição**, insira as informações apropriadas. Descrições amigáveis são úteis para manter o controle de vários conjuntos de dados que você faz upload. 

Nas caixas **Arquivo de transcrições (.txt)** e **Arquivos de áudio (.zip)**, selecione **Procurar** e, em seguida, selecione seu arquivo de transcrição de texto sem formatação e os arquivo zip dos arquivos WAV. Quando a preparação for concluída, selecione **Importar** para fazer upload dos seus dados. Seus dados serão carregados. Para conjuntos grandes de dados, o processo de importação poderá levar vários minutos.

Quando o upload for concluído, retorne para a tabela **Conjuntos de Dados Acústicos**. Uma entrada que corresponde ao seu conjunto de dados acústico é exibida. Observe que um GUID (ID exclusivo) foi atribuído. Os dados exibem seu status atual: *NotStarted* enquanto estão sendo enfileirados para processamento, *Em execução* enquanto estão em validação e *Concluído* quando estão prontos para uso.

A validação de dados inclui uma série de verificações nos arquivos de áudio para confirmar o formato do arquivo, o comprimento e a taxa de amostragem e, na transcrição arquivos, para confirmar o formato do arquivo e executar normalização do texto.

Quando o status aparece como *Êxito*, você pode selecionar **Detalhes** para exibir o relatório de verificação dos dados acústicos. O número de enunciados que passaram ou falharam na verificação é exibido, juntamente com detalhes sobre os enunciados com falha. No exemplo na imagem a seguir, dois arquivos WAV falham na verificação porque o formato de áudio é inadequado. Neste conjunto de dados, um arquivo tem uma taxa de amostragem incorreta e o outro tem um formato de arquivo incorreto.

![A página Detalhes da Adaptação de Dados](media/stt/speech-acoustic-datasets-report.png)

Se você quiser alterar o nome ou a descrição do conjunto de dados, selecione o link **Editar** e altere as suas entradas. Você não pode modificar as entradas de arquivo de áudio ou de transcrição.

## <a name="create-a-custom-acoustic-model"></a>Criar um modelo acústico personalizado

Depois que o status do conjunto de dados acústico for *Concluído*, você poderá usar o conjunto de dados para criar um modelo acústico personalizado. Para fazer isso, clique em **Modelos Acústicos** na lista suspensa **Fala Personalizada**. Uma tabela rotulada como **Seus Modelos** lista todos os seus modelos acústicos personalizados. A tabela estará vazia se esse for seu primeiro uso. O título da tabela exibe a localidade atual. No momento, você pode criar modelos acústicos para inglês dos EUA somente.

Para criar um novo modelo, selecione **Criar Novo** sob o título da tabela. Como anteriormente, insira um nome e uma descrição para ajudar a identificar esse modelo. Por exemplo, você pode usar o campo **Descrição** para registrar qual modelo inicial e conjunto de dados acústico você usou para criar o modelo. 

Em seguida, na lista suspensa **Modelo Acústico de Base**, selecione um modelo de base. O modelo de base é o ponto de partida para sua personalização. Há dois modelos acústicos de base para escolher:
* O modelo **AM de Pesquisa e Ditado da Microsoft** é apropriado para fala dirigida a um aplicativo, como comandos, consultas de pesquisa ou ditado. 
* O **Modelo de Conversa da Microsoft** é apropriado para reconhecer a fala em um estilo conversacional. Esse tipo de fala costuma ser direcionado a outra pessoa e ocorre em um call center ou em reuniões. 

A latência de resultados parciais em modelos de conversa é maior do que em modelos de pesquisa e ditado.

> [!NOTE]
> No momento, estamos desenvolvendo nosso novo modelo **Universal**, que pretende resolver todos os cenários. Os modelos mencionados anteriormente também permanecerão disponíveis publicamente.

Em seguida, na lista suspensa **Dados Acústicos**, selecione os dados acústicos que você deseja usar para realizar a personalização.

![A página Criar Modelo Acústico](media/stt/speech-acoustic-models-create2.png)

Quando o processamento for concluído, você poderá optar por executar o teste de precisão do novo modelo. Esse teste executa uma avaliação de conversão de fala em texto em um conjunto de dados acústico especificado usando o modelo acústico personalizado e, em seguida, relata os resultados. Para realizar esse teste, marque a caixa de seleção **Teste de Precisão**. Em seguida, na lista suspensa, selecione um modelo de linguagem. Se você não tiver criado nenhum modelo de linguagem personalizado, somente os modelos de linguagem de base serão exibidos na lista suspensa. Para selecionar o modelo de linguagem mais apropriado, veja [Tutorial: Criar um modelo de linguagem personalizado](how-to-customize-language-model.md).

Por fim, selecione o conjunto de dados acústico que você deseja usar para avaliar o modelo personalizado. Ao executar o teste de precisão, é importante selecionar dados acústicos diferentes daqueles usados para a criação do modelo para ter uma noção realista do desempenho do modelo. Testar a precisão com os dados de treinamento não permite que você avalie como o modelo adaptado se sai sob condições reais. O resultado será muito otimista. Observe também que o teste de precisão é limitado a 1.000 enunciados. Se o conjunto de dados acústicos para teste for maior, somente os primeiros 1.000 enunciados serão avaliados.

Quando você estiver pronto para começar a executar o processo de personalização, selecione **Criar**.

A tabela de modelos acústicos exibe uma nova entrada que corresponde a esse novo modelo. A tabela também exibe o status do processo: *Em espera*, *Em processamento* ou *Concluído*.

![A página Modelos Acústicos](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação do Serviço de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
- [Dados de exemplo do Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
