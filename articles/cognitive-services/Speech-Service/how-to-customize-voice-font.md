---
title: O que é Voz Personalizada? - Serviços Cognitivos do Azure
description: Este artigo descreve a personalização de voz de Conversão de Texto em Fala da Microsoft, que permite criar uma voz de marca exclusiva e reconhecível.
services: cognitive-services
author: noellelacharite
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 84493ae83515c0458bf5b9e9cf44603300a8b4f7
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284880"
---
# <a name="creating-custom-voice-fonts"></a>Criar fontes de voz personalizadas

A personalização de voz TTS (Conversão de Texto em Fala) da Microsoft permite que você crie uma voz exclusiva e reconhecível para sua marca: uma: *fonte de voz.* 

Para criar a fonte de voz, você faz uma gravação em estúdio e carrega os scripts associados como os dados de treinamento. Em seguida, o serviço cria um modelo de voz exclusivo ajustado para a gravação. É possível usar essa fonte de voz para a sintetização de voz. 

Você pode começar com uma pequena quantidade de dados para uma prova de conceito. Mas quanto mais dados você fornecer, mais natural e profissional será a voz.

A personalização de voz está disponível para inglês dos EUA (en-US) e chinês do continente (zh-CN).

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, o recurso de personalização de voz de Conversão de Texto em Fala está em versão prévia privada. [Preencha o formulário de aplicativo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) a ser considerado para acesso.

Também é necessário ter uma conta do Azure e uma assinatura do Serviço de Fala. [Crie uma](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started), se ainda não tiver. Conecte a assinatura ao portal de Voz Personalizada conforme a seguir.

1. Faça logon no [portal de Voz Personalizada](https://customvoice.ai) usando a mesma conta Microsoft utilizada para solicitar o acesso.

2. Vá para "Assinaturas" abaixo do nome da conta no canto superior direito.

    ![Assinaturas](media/custom-voice/subscriptions.png)

3. Na página "Assinaturas", escolha "Conectar assinatura existente".

     ![Conectar assinatura existente](media/custom-voice/connect-existing-sub.png)

4. Cole a chave de assinatura na tabela, conforme mostrado abaixo. Cada assinatura tem duas chaves e você pode usar qualquer uma delas.

     ![Adicionar assinatura](media/custom-voice/add-subscription.png)

Está tudo pronto!

## <a name="prepare-recordings-and-transcripts"></a>Preparar gravações e transcrições

Um conjunto de dados de treinamento de voz consiste em um conjunto de arquivos de áudio, junto com um arquivo de texto contendo as transcrições de todos esses arquivos de áudio.

É possível preparar esses arquivos em qualquer direção: escreva um script e leia-o para o ator de voz ou use áudio disponível publicamente e transcreva-o para texto. Nesse último caso, edite as disfluências dos arquivos de áudio, como "um" e outros sons de preenchimento, gagueja, palavras murmuradas ou pronunciamentos incorretos.

Para produzir uma fonte de voz boa, é importante que as gravações sejam feitas em uma sala silenciosa com um microfone de alta qualidade. Volume consistente, velocidade de fala, densidade de fala e expressivos maneirismos da fala são essenciais para compilar uma excelente voz digital. Para criar uma voz para uso de produção, é recomendável utilizar um estúdio de gravação profissional e um talento de voz. Para mais detalhes, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única expressão (por exemplo, uma única sentença ou um único turno de um sistema de diálogo). Todos os arquivos devem estar no mesmo idioma (não há suporte para vozes personalizadas multilíngues). Os arquivos de áudio também devem ter um nome de arquivo numérico exclusivo com a extensão de nome de arquivo `.wav`.

Arquivos de áudio devem ser preparados conforme a seguir. Outros formatos não são têm suporte e serão rejeitados.

| **Propriedade** | **Valor** |
| ------------ | --------- |
| Formato de arquivo  | RIFF (WAV)|
| Taxa de amostragem| pelo menos 16.000 Hz |
| Formato de exemplo| PCM, 16 bits |
| Nome do Arquivo    | Numérico, com extensão `.wav` |
| Formato de arquivo| Zip      |
| Tamanho máximo de arquivo|200 MB|

Coloque o conjunto de arquivos de áudio em uma única pasta sem subdiretórios e empacote todo o conjunto como um único arquivo ZIP.

> [!NOTE]
> Arquivos wave com uma taxa de amostragem menor que 16.000 Hz serão rejeitados. Nos casos em que um arquivo zip contiver ondas com taxas de amostragem diferentes, apenas aquelas iguais ou superiores a 16.000 Hz serão importadas.
> Atualmente, o portal importa arquivos ZIP de até 200 MB. No entanto, vários arquivos podem ser enviados. O número máximo de conjuntos de dados permitidos é de 10 arquivos ZIP para usuários de assinatura gratuita e 50 para usuários de assinatura padrão.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um arquivo de texto simples (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Cada linha do arquivo de transcrição deve ter o nome de um arquivo de áudio, seguido por um caractere de tabulação (ponto de código 9) e, finalmente, a transcrição. Nenhuma linha em branco é permitida.

Por exemplo: 

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

O sistema de voz personalizada normaliza as transcrições convertendo o texto em minúscula e removendo a pontuação estranha. É importante que as transcrições sejam 100% precisas para as gravações de áudio correspondentes.

> [!TIP]
> Ao compilar vozes de Conversão de Texto em Fala de produção, selecione enunciados (ou grave scripts) considerando a cobertura e eficiência fonética.

## <a name="upload-your-datasets"></a>Upload de conjunto de dados

Após preparar o arquivo de áudio e transcrições, faça upload dos arquivos através do [Portal de Serviço de Voz Personalizada](https://customvoice.ai).

> [!NOTE]
> Os conjuntos de dados não poderão ser editados após o upload. Caso tenha esquecido de incluir transcrições de alguns dos arquivos de áudio, por exemplo, ou escolher acidentalmente o sexo errado, faça upload de todo o conjunto de dados novamente. Verifique o conjunto de dados e as configurações antes de começar o upload.

1. Entre no portal.

2. Escolha **Dados** em Voz Personalizada na página principal. 

    ![Meus projetos](media/custom-voice/my-projects.png)

    A tabela Meus Dados de Voz é exibida. Se você ainda não carregou nenhum conjunto de dados de voz, a tabela estará vazia.

3. Clique em **Importar dados** para abrir a página e fazer upload de um novo conjunto de dados.

    ![Importar dados de voz](media/custom-voice/import-voice-data.png)

4. Digite um Nome e uma Descrição nos campos fornecidos. 

5. Selecione o local para as fontes de voz. Verifique se as informações de localidade correspondem ao idioma dos dados de gravação e dos scripts. 

6. Selecione o sexo do falante cuja voz você está usando.

7. Escolha o script e os arquivos de áudio para upload. 

8. Clique em **Importar** para carregar os dados. Para conjuntos de dados maiores, a importação pode demorar vários minutos.

> [!NOTE]
> Usuários de assinatura gratuita podem fazer upload de dois conjuntos de dados de cada vez. Usuários de assinatura padrão podem fazer upload de cinco conjuntos de dados simultaneamente. Se o limite for alcançado, aguarde até que pelo menos um dos conjuntos de dados conclua a importação e, em seguida, tente novamente.

Quando o upload estiver concluído, a tabela Meus Dados de Voz será exibida novamente. Você deverá ver uma entrada que corresponda ao conjunto de dados carregado recentemente. 

Os conjuntos de dados serão validados automaticamente após o upload. A validação de dados inclui uma série de verificações nos arquivos de áudio para verificar o formato, o tamanho e a taxa de amostragem do arquivo. As verificações nos arquivos de transcrição verificam o formato do arquivo e realizam alguma normalização de texto. Os enunciados são transcritos usando o reconhecimento de fala, e o texto resultante é comparado com a transcrição que você forneceu.

![Meus Dados de Voz](media/custom-voice/my-voice-data.png)

A tabela a seguir mostra os estados de processamento dos conjuntos de dados importados. 

| Estado | Significado
| ----- | -------
| `NotStarted` | O conjunto de dados foi recebido e está na fila para processamento
| `Running` | O conjunto de dados está sendo validado
| `Succeeded` | O conjunto de dados foi validado e agora pode ser usado para compilar uma fonte de voz

Após a conclusão da validação, você poderá ver o número total de enunciados correspondentes para cada um dos conjuntos de dados na coluna Enunciado.

É possível baixar um relatório para verificar as pontuações de pronúncia e o nível de ruído de cada uma das gravações. A pontuação da pronúncia varia de 0 a 100; uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade de script. Um sotaque pesado pode reduzir a pontuação de pronúncia e afetar a voz digital gerada.

Uma SNR (relação de sinal de ruído) superior indica menor ruído no áudio. Normalmente, é possível alcançar uma SNR de 50+ gravando em estúdios profissionais. O áudio com uma SNR abaixo de 20 pode resultar em ruído óbvio na voz gerada.

Considere regravar quaisquer enunciados com pontuações baixas de pronúncia ou relações de sinal de ruído ruins. Se a regravação não for possível, você poderá excluir esses enunciados do conjunto de dados.

## <a name="build-your-voice-font"></a>Compilar a fonte de voz

Depois que o conjunto de dados for validado, será possível utilizá-lo para compilar a fonte de voz personalizada. 

1. Escolha **Modelos** no menu suspenso "Voz Personalizada". 
 
    A tabela Minhas Fontes de Voz é exibida, listando todas as fontes de voz personalizadas já criadas.

1. Clique em **Criar vozes** sob o título da tabela. 

    A página para criar uma fonte de voz é exibida. A localidade atual é mostrada na primeira linha da tabela. Altere a localidade para criar uma voz em outro idioma. A localidade deve ser a mesma dos conjuntos de dados que estão sendo usados para criar a voz.

1. Assim como fez durante o upload do conjunto de dados, insira um nome e uma descrição para ajudá-lo a identificar esse modelo. 

    O nome que você inserir aqui, será o nome utilizado para especificar a voz na solicitação de síntese de fala como parte da entrada SSML, portanto, escolha com cuidado. Apenas letras, números e alguns caracteres de pontuação como '-', '_' '(', ')' são permitidos.

    Um uso comum do campo Descrição é registrar os nomes dos conjuntos de dados que foram usados para criar o modelo.

1. Escolha o sexo da fonte de voz. Deve corresponder ao sexo do conjunto de dados.

1. Selecione o(s) conjunto(s) de dados que você quer usar para treinar a fonte de voz. Todos os conjuntos de dados usados devem ser do mesmo falante.

1. Clique em **Criar** para começar a criar a fonte de voz.

    ![Criar modelo](media/custom-voice/create-model.png)

O novo modelo aparece na tabela Minhas Fontes de Voz. 

![Minhas Fontes de Voz](media/custom-voice/my-voice-fonts.png)

O status mostrado reflete o processo de conversão do conjunto de dados em uma fonte de voz, conforme mostrado aqui.

| Estado | Significado
| ----- | -------
| `NotStarted` | A solicitação de criação de fontes de voz está na fila para processamento
| `Running` | A fonte de voz está sendo criada
| `Succeeded` | A fonte de voz foi criada e pode ser implantada

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Intervalos de tempo típicos variam de aproximadamente 30 minutos para centenas de enunciados a 40 horas para 20.000 enunciados.

> [!NOTE]
> Usuários de assinatura gratuita podem treinar uma fonte de voz de cada vez. Usuários de assinatura padrão podem treinar três vozes simultaneamente. Se o limite for alcançado, aguarde até que pelo menos uma das fontes de voz termine o treinamento e, em seguida, tente novamente.

## <a name="test-your-voice-font"></a>Testar a fonte de voz

Depois que a fonte de voz for compilada com êxito, será possível testá-la antes de implantá-la para uso. Clique em **Testar** na coluna Operações. A página de teste é exibida para a fonte de voz selecionada. Se você ainda não enviou solicitações de teste para a voz, a tabela estará vazia.

![Minhas Fontes de Voz, parte 2](media/custom-voice/my-voice-fonts2.png)

Clique em **Teste com texto** abaixo do título da tabela para exibir um menu pop-up para enviar solicitações de texto. É possível enviar a solicitação de teste em texto sem formatação ou SSML. O tamanho máximo de entrada é de 1.024 caracteres, incluindo todas as marcas para solicitação SSML. O idioma do texto deve ser o mesmo idioma da fonte de voz.

![Teste de Fonte de Voz](media/custom-voice/voice-font-testing.png)

Após preencher a caixa de texto e confirmar o modo de entrada, clique em **Sim** para enviar a solicitação de teste e retornar à página de teste. A tabela agora inclui uma entrada que corresponde à nova solicitação e a coluna de status familiar. Pode demorar alguns minutos para a sintetização de voz. Quando a coluna de status for lida com êxito, será possível baixar a entrada de texto (um arquivo `.txt`) e a saída de áudio (um arquivo `.wav`) e auditar o último para qualidade.

![Teste de Fonte de Voz, parte 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Criar e usar um ponto de extremidade personalizado

Depois de ter criado e testado com êxito o modelo de voz, implante-o em um ponto de extremidade de Conversão de Texto em Fala. Em seguida, você usará esse ponto de extremidade no lugar do ponto de extremidade comum ao fazer solicitações de Conversão de Texto em Fala por meio de API REST. O ponto de extremidade personalizado pode ser chamado apenas pela assinatura usada para implantar a fonte.

Para criar um novo ponto de extremidade personalizado, escolha **Pontos de Extremidade** no menu Voz Personalizada na parte superior da página. A página Minhas Vozes Implantadas será exibida, com a tabela de pontos de extremidade de voz personalizada atual, se houver. A localidade atual é refletida na primeira linha da tabela. Para criar uma implantação para um idioma diferente, altere a localidade exibida. (Deve corresponder à voz que você está implantando.)

Clique no botão **Implantar vozes** para criar um novo ponto de extremidade. Insira o Nome e a Descrição do ponto de extremidade personalizado.

No menu Assinatura, escolha a assinatura que você quer usar. Usuários de assinatura gratuita podem ter apenas um modelo implantado por vez. Usuários de assinatura padrão podem criar até 20 pontos de extremidade, cada um com a própria voz personalizada.

![Criar Ponto de Extremidade](media/custom-voice/create-endpoint.png)

Após selecionar o modelo a ser implantado, clique em **Criar**. A página Minhas Vozes Implantadas será exibida novamente, agora com uma entrada para o novo ponto de extremidade. Pode demorar alguns minutos para instanciar um novo ponto de extremidade. Quando o status da implantação for Com êxito, o ponto de extremidade estará pronto para uso.

![Minhas Vozes Implantadas](media/custom-voice/my-deployed-voices.png)

Quando o status da implantação for Com Êxito, o ponto de extremidade da fonte de voz implantada aparecerá na tabela Minhas Vozes Implantadas. É possível usar esse URI diretamente em uma solicitação HTTP.

O teste online do ponto de extremidade também está disponível no portal de voz personalizada. Para testar o ponto de extremidade, escolha **Teste de pontos de extremidade** no menu suspenso Voz Personalizada. A página de teste do ponto de extremidade é exibida. Escolha uma voz personalizada implantada e digite o texto a ser falado (em texto sem formatação ou em formato SSML) na caixa de texto.

> [!NOTE] 
> Ao usar SSML, a marca `<voice>` deverá especificar o nome que você forneceu à voz personalizada ao criá-la.

Clique em **Reproduzir** para ouvir o texto falado na fonte de voz personalizada.

![Testar ponto de extremidade](media/custom-voice/endpoint-testing.png)

O ponto de extremidade personalizado é funcionalmente idêntico ao ponto de extremidade padrão usado para solicitações de Conversão de Texto em Fala. Consulte [API REST](rest-apis.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
