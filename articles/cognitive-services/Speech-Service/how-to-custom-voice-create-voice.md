---
title: Criar uma voz personalizada - serviços de fala
titlesuffix: Azure Cognitive Services
description: Quando você estiver pronto para carregar seus dados, vá para o portal de voz personalizada. Crie ou selecione um projeto de voz personalizada. O projeto deve compartilhar a direito de idioma/localidade e as propriedades de gênero como os dados você pretende usar para treinamento de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: fad69c4108d747c44eccf37b81adf2c7c615cb58
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156841"
---
# <a name="create-a-custom-voice"></a>Criar uma voz personalizadas

Na [preparar os dados de voz personalizada](how-to-custom-voice-prepare-data.md), descrevemos os tipos de dados diferentes que você pode usar para treinar uma voz personalizadas e os requisitos de formato diferente. Depois de preparar seus dados, você pode começar a carregá-los para o [portal de voz personalizada](http://aka.ms/custom-voice-portal), ou por meio da API de treinamento de voz personalizada. Neste artigo, descrevemos as etapas de uma voz personalizada por meio do portal de treinamento.

> [!NOTE]
> Essa página assume que você leu [começar com voz personalizada](how-to-custom-voice.md) e [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md)e tenha criado um projeto de voz personalizada.

Verificar os idiomas com suporte de voz personalizada: [linguagem para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Upload de conjunto de dados

Quando você estiver pronto para carregar seus dados, vá para o [portal de voz personalizada](http://aka.ms/custom-voice-portal). Crie ou selecione um projeto de voz personalizada. O projeto deve compartilhar a direito de idioma/localidade e as propriedades de gênero como os dados você pretende usar para treinamento de voz. Por exemplo, selecione `en-GB` se as gravações de áudio que você tem é feito em inglês com uma ênfase do Reino Unido.

Vá para o **dados** guia e clique em **carregar dados**. No assistente, selecione o tipo de dados correto que corresponda ao que você preparou.

Cada conjunto de dados que você carregar deve cumprir os requisitos para o tipo de dados que você escolher. É importante Formatar corretamente seus dados antes de serem carregado. Isso garante que os dados serão processados com precisão pelo serviço de voz personalizada. Vá para [preparar os dados de voz personalizada](how-to-custom-voice-prepare-data.md) e verifique se os dados tiverem sido formatados corretamente.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem carregar dois conjuntos de dados simultaneamente. Os usuários de assinatura Standard (S0) podem carregar cinco conjuntos de dados simultaneamente. Se o limite for alcançado, aguarde até que pelo menos um dos conjuntos de dados conclua a importação. Em seguida, tente novamente.

> [!NOTE]
> O número máximo de conjuntos de dados de permissão para ser importado por assinatura é 10 arquivos. zip gratuitamente arquivos de usuários de assinatura (F0) e 500 para usuários de assinatura standard (S0).

Conjuntos de dados são validados automaticamente quando você clicar no botão de upload. Validação de dados inclui uma série de verificações sobre os arquivos de áudio para verificar se seu formato de arquivo, tamanho e taxa de amostragem. Corrija os erros se houver e envie novamente. Quando a solicitação de importação de dados é iniciada com êxito, você deve ver uma entrada na tabela de dados que corresponde ao conjunto de dados que você acabou de carregar.

A tabela a seguir mostra os estados de processamento dos conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Processando | O conjunto de dados foi recebido e está sendo processado. |
| Bem-sucedida | Seu conjunto de dados tiver sido validado e agora pode ser usado para criar um modelo de voz. |
| Com falha | O conjunto de dados falhou durante o processamento devido a muitos motivos, por exemplo, erros de arquivo, problemas de dados ou problemas de rede. |

Após a conclusão da validação, você pode ver o número total de declarações correspondentes para cada um dos conjuntos de dados na **declarações** coluna. Se você tiver selecionado o tipo de dados requer segmentação de áudio longa, esta coluna reflete somente as declarações que podemos ter segmentados para você com base em suas transcrições ou por meio do serviço de transcrição de fala. Além disso, você pode baixar o conjunto de dados validado para exibir os resultados de detalhe das declarações que foram importados com êxito e suas transcrições de mapeamento. Dica: segmentação de áudio longa pode levar mais de uma hora para concluir o processamento de dados.

Conjuntos de dados de en-US e zh-CN, é possível baixar um relatório para verificar as pontuações de pronúncia e o nível de ruído para cada uma das suas gravações ainda mais. A pontuação de pronúncia varia de 0 a 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade com o script. Um sotaque pesado pode reduzir a pontuação de pronúncia e afetar a voz digital gerada.

Uma SNR (relação de sinal de ruído) superior indica menor ruído no áudio. Normalmente, é possível alcançar uma SNR de 50+ com gravação em estúdios profissionais. O áudio com uma SNR abaixo de 20 pode resultar em ruído óbvio na voz gerada.

Considere regravar quaisquer enunciados com pontuações baixas de pronúncia ou relações de sinal de ruído ruins. Caso não possa regravar, existe a possibilidade de excluir esses enunciados do conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Criar seu modelo de voz personalizadas

Depois que o conjunto de dados tiver sido validado, você pode usá-lo para criar seu modelo de voz personalizadas.

1.  Navegue até **fala > voz personalizada > treinamento**.

2.  Clique em **modelo de treinamento**.

3.  Em seguida, insira um **nome** e **descrição** para ajudá-lo a identificar esse modelo.

    Escolha um nome com cuidado. O nome que você inserir aqui será o nome utilizado para especificar a voz na solicitação de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de pontuação, como - \_e (',') são permitidos. Use nomes diferentes para modelos de voz diferente.

    Um uso comum do campo **Descrição** é registrar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  Dos **selecionar dados de treinamento** , escolha um ou vários conjuntos de dados que você deseja usar para treinamento. Verifique o número de declarações antes de enviá-los. Você pode iniciar com qualquer número de declarações para modelos de voz en-US e zh-CN. Para outras localidades, você deve selecionar mais de 2.000 declarações para poder treinar uma voz.

    > [!NOTE]
    > Nomes duplicados de áudio serão removidos com o treinamento. Certifique-se de selecionar os conjuntos de dados não contêm os mesmos nomes de áudio em vários arquivos. zip.

    > [!TIP]
    > Usar os conjuntos de dados de alto-falante do mesmo é necessário para resultados de qualidade. Quando os conjuntos de dados que você enviou para treinamento contiverem um número total de menos de 6.000 declarações distintos, você será treinar seu modelo de voz através da técnica de estatística síntese paramétrica. No caso em que seus dados de treinamento excedem o número total de 6.000 declarações distintos, você iniciará um processo de treinamento com a técnica de síntese de concatenação. Normalmente, a tecnologia de concatenação pode resultar em resultados de voz mais natural e maior fidelidade. [Entre em contato com a equipe de voz personalizada](mailto:speechsupport@microsoft.com) se você deseja treinar um modelo com a tecnologia mais recente de TTS Neural que pode produzir uma voz digital equivalente ao disponível publicamente [vozes neurais](language-support.md#neural-voices).

5.  Clique em **Train** para começar a criar seu modelo de voz.

A tabela de treinamento exibe uma nova entrada que corresponde a esse modelo de recém-criado. A tabela também exibe o status: Processamento, com êxito, com falha.

O status mostrado reflete o processo de conversão de seu conjunto de dados em um modelo de voz, conforme mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Processando | Seu modelo de voz está sendo criado. |
| Bem-sucedida | Seu modelo de voz tiver sido criado e pode ser implantado. |
| Com falha | Seu modelo de voz falhou no treinamento devido a muitos motivos, problemas de rede ou problemas de dados não vistos por exemplo. |

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Intervalos de tempo típicos variam de aproximadamente 30 minutos para centenas de enunciados a 40 horas para 20.000 enunciados. Depois que o treinamento do modelo for bem-sucedida, você pode começar a testá-lo.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem treinar uma fonte de voz simultaneamente. Os usuários de assinatura Standard (S0) podem treinar três vozes simultaneamente. Se o limite for alcançado, aguarde até que pelo menos uma das fontes de voz termine o treinamento; em seguida, tente novamente.

> [!NOTE]
> O número máximo de modelos de voz podem ser treinados por assinatura é 10 modelos para usuários de assinatura gratuita (F0) e 100 para usuários de assinatura standard (S0).

## <a name="test-your-voice-model"></a>Testar o modelo de voz

Depois que a fonte de voz for compilada com êxito, você poderá testá-la antes de implantá-la para uso.

1.  Navegue até **fala > voz personalizada > teste**.

2.  Clique em **adicionar teste**.

3.  Selecione um ou vários modelos que você deseja testar.

4.  Forneça o texto que você deseja que o voice(s) falar. Se você tiver selecionado para testar vários modelos ao mesmo tempo, o mesmo texto será usado para o teste para modelos diferentes.

    > [!NOTE]
    > O idioma do texto deve ser o mesmo idioma da fonte de voz. Somente modelos treinados com êxito podem ser testados. Apenas texto sem formatação é compatível com esta etapa.

5.  Clique em **Criar**.

Depois de enviar sua solicitação de teste, você retornará à página de teste. A tabela agora inclui uma entrada que corresponde à nova solicitação e a coluna de status. Pode demorar alguns minutos para a sintetização de voz. Quando a coluna de status diz **bem-sucedido**, você pode reproduzir o áudio, ou baixe a entrada de texto (um arquivo. txt) e (um arquivo. wav) de saída de áudio e audition ainda mais o último de qualidade.

Você também pode encontrar os resultados do teste na página de detalhes de cada modelos que você selecionou para teste. Vá para o **treinamento** guia e clique no nome do modelo para inserir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e usar um ponto de extremidade de voz personalizadas

Depois de criar e testar com êxito o modelo de voz, implante-o em um ponto de extremidade personalizado de Conversão de Texto em Fala. Em seguida, você usará esse ponto de extremidade no lugar do ponto de extremidade comum ao fazer solicitações de Conversão de Texto em Fala por meio de API REST. O ponto de extremidade personalizado pode ser chamado somente pela assinatura que você usou para implantar a fonte.

Para criar um novo ponto de extremidade de voz personalizadas, acesse **fala > voz personalizada > implantação**. Selecione **Adicionar ponto de extremidade** e insira um **nome** e **descrição** para seu ponto de extremidade personalizado. Em seguida, selecione o modelo de voz personalizada que você deseja associar a esse ponto de extremidade.

Depois que você clicou o **adicionar** botão, a tabela de ponto de extremidade, você verá uma entrada para o novo ponto de extremidade. Pode demorar alguns minutos para instanciar um novo ponto de extremidade. Quando o status da implantação for **Com êxito**, o ponto de extremidade estará pronto para uso.

> [!NOTE]
> Os usuários de assinatura gratuita (F0) podem ter apenas um modelo implantado. Os usuários de assinatura Standard (S0) podem criar até 50 pontos de extremidade, cada um com sua própria voz personalizada.

> [!NOTE]
> Para usar sua voz personalizada, especifique o nome do modelo de voz, use o URI personalizado diretamente em uma solicitação HTTP e usar a mesma assinatura de passagem, a autenticação do serviço TTS.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Clique no link para exibir informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo.

O teste online do ponto de extremidade também está disponível no portal de voz personalizada. Para testar seu ponto de extremidade, escolha **verificar o ponto de extremidade** da **detalhes do ponto de extremidade** página. A página de teste do ponto de extremidade é exibida. Insira o texto a ser falado (em texto sem formatação ou [formato SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado na fonte de voz personalizada, selecione **Reproduzir**. Esse recurso de teste será cobrado no seu uso de síntese de fala personalizado.

O ponto de extremidade personalizado é funcionalmente idêntico ao ponto de extremidade padrão utilizado para solicitações de Conversão de Texto em Fala. Consulte [API REST](rest-text-to-speech.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

* [Guia: Registre seus exemplos de voz](record-custom-voice-samples.md)
* [Referência de API de texto em fala](rest-text-to-speech.md)
