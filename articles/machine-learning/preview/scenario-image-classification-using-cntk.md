---
title: "Classificação de imagens usando o CNTK no Azure Machine Learning Workbench | Microsoft Docs"
description: "Treinar, avaliar e implantar um modelo de classificação de imagens personalizada usando o Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: mawah, marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 53d182d84c8f28c7b4055780a5b41df00fdc8583
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Classificação de imagens usando o Azure Machine Learning Workbench

Abordagens de classificação de imagens podem ser usadas para resolver um grande número de problemas da Pesquisa Visual Computacional.
Isso inclui a criação de modelos, que responde a perguntas como: *Um OBJETO está presente na imagem?*, em que OBJETO poderia ser, por exemplo, *cachorro*, *carro* ou *navio*. Ou perguntas mais complexas como: *Qual classe de gravidade de doença ocular é evidenciada pelo exame de retina desse paciente?*.

Este tutorial aborda como resolver esses problemas. Mostramos como treinar, avaliar e implantar seu próprio modelo de classificação de imagens usando o [CNTK (Microsoft Cognitive Toolkit) ](https://docs.microsoft.com/cognitive-toolkit/) para aprendizagem profunda.
Imagens de exemplo são fornecidas, mas o leitor também pode trazer seu próprio conjunto de dados e treinar seus próprios modelos personalizados.

Tradicionalmente, as soluções de Pesquisa Visual Computacional exigiam conhecimento especializado para identificar e implementar manualmente os chamados *recursos*, que realçam as informações desejadas em imagens.
Essa abordagem manual foi alterada em 2012 com o famoso artigo sobre Aprendizagem Profunda em [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] e, no momento, as DNNs (Redes Neurais Profundas) são usadas para localizar esses recursos automaticamente.
As DNNs levaram a uma grande melhoria na área, não apenas para a classificação de imagens, mas também para outros problemas de Pesquisa Visual Computacional, como a detecção de objetos e a similaridade de imagens.


## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Visão geral

Este tutorial é dividido em três partes:

- A Parte 1 mostra como treinar, avaliar e implantar um sistema de classificação de imagens usando uma DNN pré-treinada como um recurso e treinando uma SVM em seu resultado.
- Em seguida, a Parte 2 mostra como melhorar a precisão para, por exemplo, refinar a DNN em vez de usá-la como um recurso fixo.
- A Parte 3 aborda como usar seu próprio conjunto de dados em vez de imagens de exemplo fornecidas e, se necessário, como gerar seu próprio conjunto de dados pela captura de imagens da rede.

Embora a experiência anterior com o aprendizado de máquina e o CNTK não seja necessária, ela é útil para compreensão dos princípios subjacentes. Os números de precisão, o horário de treinamento, etc., relatados no tutorial são apenas para referência e os valores reais durante a execução do código quase certamente são diferentes.


## <a name="prerequisites"></a>pré-requisitos

Os pré-requisitos para executar este exemplo são os seguintes:

1. Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
2. O [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.  
3. Um computador Windows. O sistema operacional Windows é necessário, pois o Workbench dá suporte apenas ao Windows e MacOS, enquanto o Cognitive Toolkit da Microsoft (que usamos como uma biblioteca de aprendizagem profunda) dá suporte apenas ao Windows e Linux.
4. Uma GPU dedicada não é necessária para executar o treinamento de SVM na parte 1. No entanto, ela é necessária para refinar a DNN descrita na parte 2. Caso você não tenha uma GPU forte, deseje treinar em várias GPUs ou não tenha um computador Windows, considere a possibilidade de usar uma Máquina Virtual de Aprendizagem Profunda do Azure com o sistema operacional Windows. Consulte [aqui](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) para obter um guia de implantação com um clique. Depois de implantada, conecte-se à VM por meio de uma conexão de área de trabalho remota, instale o Workbench nesse local e execute o código localmente na VM.
5. Várias bibliotecas do Python como OpenCV precisam ser instaladas. Clique em *Abrir Prompt de Comando* do menu *Arquivo* no Workbench e execute os seguintes comandos para instalar essas dependências:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` depois de baixar a roda de OpenCV em http://www.lfd.uci.edu/~gohlke/pythonlibs/ (a versão e o nome de arquivo exatos podem variar)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`

### <a name="troubleshooting--known-bugs"></a>Solução de problemas/bugs conhecidos
- Uma GPU é necessária para a parte 2. Caso contrário, o erro “O treinamento de normalização em lotes na CPU ainda não foi implementado” é gerado durante a tentativa de refinar a DNN.
- Erros de memória insuficiente durante o treinamento de DNN podem ser evitados com a redução do tamanho do minilote (variável `cntk_mb_size` em `PARAMETERS.py`).
- O código foi testado usando o CNTK 2.2 e também deve ser executado em versões mais antigas (até v2.0) e mais recentes sem nenhuma alteração ou com apenas alterações secundárias.
- No momento da gravação, o Azure Machine Learning Workbench teve problemas em mostrar blocos de anotações maiores que 5 MB. Podem ocorrer blocos de anotações desse tamanho grande se o bloco de anotações for salvo com toda a saída de célula exibida. Caso você encontre esse erro, abra o prompt de comando no menu Arquivo, dentro do Workbench, execute `jupyter notebook`, abra o bloco de anotações, limpe toda a saída e salve o bloco de anotações. Depois de executar essas etapas, o bloco de anotações será aberto corretamente no Azure Machine Learning Workbench novamente.
- Todos os scripts fornecidos neste exemplo precisam ser executados localmente e não, por exemplo, em ambiente de docker remoto. Todos os blocos de anotações precisam ser executados com kernel definido para o kernel do projeto local com o nome "PROJECTNAME local" (por exemplo, "myImgClassUsingCNTK local").

    
## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de workbench

Para criar um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench.
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**.
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto.
4.  Na caixa de pesquisa **Pesquisar Modelos de Projeto**, digite “Classificação de imagens” e selecione o modelo.
5.  Clique em **Criar**.

A execução dessas etapas cria a estrutura do projeto mostrada abaixo. O diretório do projeto é restrito a ter menos de 25 MB, pois o Azure Machine Learning Workbench cria uma cópia dessa pasta após cada execução (para habilitar o histórico de execuções). Portanto, todas as imagens e todos os arquivos temporários são salvos no diretório *~/Desktop/imgClassificationUsingCntk_data* (conhecido como *DATA_DIR* neste documento).

  Pasta| DESCRIÇÃO
  ---|---
  aml_config/|                           Diretório que contém os arquivos de configuração do Azure Machine Learning Workbench
  libraries/|                              Diretório que contém todas as funções auxiliares do Python e do Jupyter
  notebooks/|                              Diretório que contém todos os blocos de anotações
  resources/|                              Diretório que contém todos os recursos (por exemplo, URL de imagens de moda)
  scripts/|                              Diretório que contém todos os scripts
  PARAMETERS.py|                       Script do Python que especifica todos os parâmetros
  readme.md|                           Este documento Leiame


## <a name="data-description"></a>Descrição dos dados

Este tutorial usa como exemplo funcional um conjunto de dados de textura de vestuário para a parte superior do corpo que consiste em até 428 imagens. Cada imagem é anotada como uma das três texturas diferentes (bolinhas, listrada e estampa de leopardo). Mantivemos o número de imagens pequeno para que este tutorial pudesse ser executado rapidamente. No entanto, o código é bem testado e funciona com dezenas de milhares de imagens ou mais. Todas as imagens foram capturadas usando a Pesquisa de Imagem do Bing e anotadas manualmente, conforme explicado na [Parte 3](#using-a-custom-dataset). As URLs da imagem com seus respectivos atributos são listadas no arquivo */resources/fashionTextureUrls.tsv*.

O script `0_downloadData.py` baixa todas as imagens no diretório *DATA_DIR/images/fashionTexture/*. Algumas das 428 URLs estão provavelmente desfeitas. Isso não é um problema e apenas significa que temos um pouco menos imagens para treinamento e teste. Todos os scripts fornecidos neste exemplo precisam ser executados localmente e não, por exemplo, em ambiente de docker remoto.

A figura a seguir mostra exemplos dos atributos bolinhas (esquerda), listrada (intermediárias) e estampa de leopardo (direita). As anotações foram feitas de acordo com o item de vestuário para a parte superior do corpo.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Parte 1 – Treinamento e avaliação de modelo

Na primeira parte deste tutorial, estamos treinando um sistema que usa, mas que não modifica, uma rede neural profunda pré-treinada. Esta DNN pré-treinada é usada como um recurso e uma SVM linear é treinada para prever o atributo (bolinhas, listrada ou estampa de leopardo) de determinada imagem.

Agora, descrevemos essa abordagem em detalhes, passo a passo e mostramos quais scripts precisam ser executados. Após cada etapa, recomendamos inspecionar quais arquivos são gravados e em que local eles são gravados.

Todos os parâmetros importantes são especificados e uma breve explicação é fornecida em um único lugar: no arquivo `PARAMETERS.py`.




### <a name="step-1-data-preparation"></a>Etapa 1: Preparação de dados
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

O bloco de anotações `showImages.ipynb` pode ser usado para visualizar as imagens e corrigir sua anotação, conforme necessário. Para executar o bloco de anotações, abra-o no Azure Machine Learning Workbench, clique em “Iniciar o Servidor do Notebook” caso essa opção seja mostrada, mude para o kernel do projeto local com o nome "PROJECTNAME local" (por exemplo, "myImgClassUsingCNTK local") e, em seguida, execute todas as células no bloco de anotações. Consulte a seção de solução de problemas deste documento caso você receba um erro indicando que o bloco de anotações é muito grande para ser exibido.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Agora execute o script chamado `1_prepareData.py`, que atribui todas as imagens ao conjunto de treinamento ou ao conjunto de teste. Essa atribuição é mutuamente exclusiva – nenhuma imagem de treinamento também é usada para teste ou vice-versa. Por padrão, um percentual aleatório de 75% das imagens de cada classe de atributos é atribuído ao treinamento e os 25% restantes são atribuídos ao teste. Todos os dados gerados pelo script são salvos na pasta *DATA_DIR/proc/fashionTexture/*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Etapa 2: Refinando a Rede Neural Profunda
`Script: 2_refineDNN.py`

Como explicamos na parte 1 deste tutorial, a DNN pré-treinada é mantida fixa (ou seja, não é refinada). No entanto, o script chamado `2_refineDNN.py` ainda é executado na parte 1, pois carrega um modelo [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] pré-treinado e o modifica, por exemplo, para permitir uma resolução mais alta de imagem de entrada. Essa etapa é rápida (segundos) e não exige uma GPU.

Na parte 2 do tutorial, uma modificação no arquivo PARAMETERS.py faz com que o script `2_refineDNN.py` também refine a DNN pré-treinada. Por padrão, executamos 45 épocas de treinamento durante o refinamento.

Em ambos os casos, o modelo final é então gravado no arquivo *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Etapa 3: Avaliar a DNN em todas as imagens
`Script: 3_runDNN.py`

Agora, podemos usar a DNN (possivelmente refinada) da última etapa para personalizar nossas imagens. Considerando uma imagem como entrada para a DNN, a saída é o vetor de 512 flutuações da penúltima camada do modelo. Esse vetor é um dimensional muito menor do que a própria imagem. No entanto, ela deve conter (e até mesmo realçar) todas as informações na imagem relevante para reconhecer o atributo da imagem, ou seja, se o item de vestuário tem uma textura de bolinhas, listrada ou de estampa de leopardo.

Todas as representações de imagem da DNN são salvas no arquivo *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Etapa 4: Treinamento de Computador de Vetor de Suporte
`Script: 4_trainSVM.py`

As representações de 512 flutuações calculadas na última etapa agora são usadas para treinar um classificador de SVM: considerando uma imagem como entrada, a SVM gera uma pontuação para que cada atributo esteja presente. Em nosso conjunto de dados de exemplo, isso significa uma pontuação para “listrada”, “bolinhas” e “estampa de leopardo”.

O script `4_trainSVM.py` carrega as imagens de treinamento, treina uma SVM para diferentes valores do parâmetro C de regularização (margem de atraso) e mantém a SVM com a precisão mais alta. A precisão de classificação é impressa no console e plotada no Workbench. Para os dados de textura fornecidos, esses valores devem estar em torno de 100% e 88%, respectivamente. Por fim, a SVM treinada é gravada no arquivo *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Etapa 5: Avaliação e visualização
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

A precisão do classificador de imagens treinado pode ser medida com o script `5_evaluate.py`. O script pontua todas as imagens de teste usando o classificador de SVM treinado, atribui a cada imagem o atributo com a pontuação mais alta e compara os atributos previstos com as anotações de verdade terrestre.

O resultado do script `5_evaluate.py` é mostrado abaixo. A precisão de classificação de cada classe individual é calculada, bem como a precisão do conjunto de teste completo (“precisão geral”) e a média sobre as precisões individuais (“precisão geral média da classe”). 100% corresponde à melhor precisão possível e 0% à pior. A previsão aleatória em média produziria uma precisão de média da classe igual a 1 sobre o número de atributos: em nosso caso, essa precisão seria de 33,33%. Esses resultados melhoram consideravelmente ao usar uma resolução mais alta de entrada como `rf_inputResoluton = 1000`, porém, à custa de tempos de computação de DNN mais longos.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Além da precisão, a curva ROC é plotada com a respectiva área abaixo da curva (à esquerda); e a matriz de confusão é mostrada (à direita):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Por fim, o bloco de anotações `showResults.py` é fornecido para percorrer as imagens de teste e visualizar suas respectivas pontuações de classificação. Conforme explicado na etapa 1, cada bloco de anotações neste exemplo precisa usar o kernel do projeto local com o nome "PROJECTNAME local":
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Etapa 6: Implantação
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

O sistema treinado agora pode ser publicado como uma API REST. A implantação é explicada no bloco de anotações `deploy.ipynb` e é baseada na funcionalidade no Azure Machine Learning Workbench (lembre-se de definir, como kernel, o kernel do projeto local com o nome "PROJECTNAME local"). Consulte também a excelente seção de implantação do [tutorial do IRIS](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3) para obter mais informações relacionadas à implantação.

Depois de implantado, o serviço Web pode ser chamado usando o script `6_callWebservice.py`. Observe que o endereço IP (local ou na nuvem) do serviço Web precisa ser definido primeiro no script. O bloco de anotações `deploy.ipynb` explica como encontrar esse endereço IP.








## <a name="part-2---accuracy-improvements"></a>Parte 2 – Melhorias de precisão

Na parte 1, mostramos como classificar uma imagem treinando um Computador de Vetor de Suporte linear na saída de 512 flutuações de uma Rede Neural Profunda. Essa DNN foi pré-treinada em milhões de imagens e a penúltima camada foi retornada como um vetor de recurso. Essa abordagem é rápida, pois a DNN é usada no estado em que se encontra, mas, mesmo assim, geralmente oferece bons resultados.

Agora, apresentamos várias maneiras de melhorar a precisão do modelo da parte 1. O mais importante é que refinamos a DNN em vez de mantê-la fixa.

### <a name="dnn-refinement"></a>Refinamento de DNN

Em vez de uma SVM, uma pessoa pode fazer a classificação diretamente na rede neural. Isso é feito adicionando uma nova última camada à DNN pré-treinada, que usa as 512 flutuações da penúltima camada como entrada. A vantagem de fazer a classificação na DNN é que agora toda a rede pode ser treinada novamente usando a propagação de retorno. Essa abordagem geralmente leva a melhores precisões de classificação comparado ao uso da DNN pré-treinada no estado em que se encontra, porém, à custa de um tempo de treinamento muito mais longo (mesmo com a GPU).

O treinamento da Rede Neural em vez de uma SVM é feito com a alteração da variável `classifier` em `PARAMETERS.py` de `svm` para `dnn`. Em seguida, conforme descrito na parte 1, todos os scripts, exceto a preparação de dados (etapa 1) e o treinamento de SVM (etapa 3) precisam ser executados novamente. O refinamento de DNN exige uma GPU. Se nenhuma GPU foi encontrada ou se a GPU estiver bloqueada (por exemplo, por uma execução anterior do CNTK), o script `2_refineDNN.py` gerará um erro. O treinamento de DNN pode gerar um erro de memória insuficiente em algumas GPUs, o que pode ser evitado com a redução do tamanho do minilote (variável `cntk_mb_size` em `PARAMETERS.py`).

Após a conclusão do treinamento, o modelo refinado é salvo em *DATA_DIR/proc/fashionTexture/cntk_refined.model* e um gráfico desenhado que mostra como os erros de classificação de treinamento e teste mudam durante o treinamento. Observe nesse gráfico que o erro no conjunto de treinamento é muito menor do que no conjunto de teste. Esse suposto comportamento de ajuste excessivo pode ser reduzido, por exemplo, usando um valor mais alto para a taxa de abandono `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Como pode ser visto no gráfico abaixo, a precisão que usa o refinamento de DNN no conjunto de dados fornecido é de 92,35% versus os 88,92% anteriores (parte 1). Em particular, as imagens de “bolinhas” melhoram de forma significativa, com uma área sob a curva de ROC de 0,98 com refinamento vs. 0,94 anteriormente. Estamos usando um pequeno conjunto de dados e, portanto, as precisões reais que executam o código são diferentes. Essa discrepância ocorre devido a efeitos aleatórios, como a divisão aleatória das imagens em conjuntos de treinamento e teste.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Acompanhamento do histórico de execuções

O Azure Machine Learning Workbench armazena o histórico de cada execução no Azure para permitir a comparação de duas ou mais execuções que têm uma diferença de até mesmo semanas. Isso é explicado em detalhes no [tutorial do Iris](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). Isso também é ilustrado nas capturas de tela a seguir, nas quais comparamos duas execuções do script `5_evaluate.py`, usando o refinamento de DNN, ou seja, `classifier = "dnn"`(número de execução 148) ou o treinamento de SVM, ou seja, `classifier = "svm"` (número de execução 150).

Na primeira captura de tela, o refinamento de DNN leva a melhores precisões do que o treinamento de SVM para todas as classes. A segunda captura de tela mostra todas as métricas que estão sendo rastreadas, incluindo qual foi o classificador. Esse acompanhamento é feito no script `5_evaluate.py` chamando o agente do Azure Machine Learning Workbench. Além disso, o script também salva a curva ROC e a matriz de confusão na pasta *outputs*. Essa pasta *outputs* é especial pois seu conteúdo também é rastreado pelo recurso de histórico do Workbench e, portanto, os arquivos de saída podem ser acessados a qualquer momento, independentemente se as cópias locais foram substituídas.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Ajuste de parâmetro
Assim como ocorre na maioria dos projetos de aprendizado de máquina, a obtenção de bons resultados para um novo conjunto de dados exige um ajuste cuidadoso de parâmetros, bem como a avaliação de diferentes decisões de design. Para ajudar com essas tarefas, todos os parâmetros importantes são especificados e uma breve explicação é fornecida em um único lugar: no arquivo `PARAMETERS.py`.

Estas são algumas das oportunidades mais promissoras de melhorias:

- Qualidade de dados: verifique se os conjuntos de treinamento e teste têm alta qualidade. Ou seja, as imagens são anotadas corretamente, as imagens ambíguas são removidas (por exemplo, itens de vestuário com listras e bolinhas) e os atributos são mutuamente exclusivos (ou seja, escolhidos de modo que cada imagem pertença a exatamente um atributo).
- Se o objeto de interesse for pequeno na imagem, as abordagens de Classificação de imagens serão conhecidas por não funcionarem bem. Nesses casos, considere a possibilidade de usar uma abordagem de detecção de objeto, conforme descrito neste [tutorial](https://github.com/Azure/ObjectDetectionUsingCntk).
- Refinamento de DNN: o parâmetro indiscutivelmente mais importante a ser ajustado corretamente é a taxa de aprendizagem `rf_lrPerMb`. Se a precisão no conjunto de treinamento (primeira figura da parte 2) não estiver próxima a 0-5%, provavelmente, isso é devido a uma taxa de aprendizagem incorreta. Os outros parâmetros que começam com `rf_` são menos importantes. Normalmente, o erro de treinamento deve diminuir exponencialmente e estar próximo a 0% depois do treinamento.
- Resolução de entrada: a resolução de imagem padrão é de 224x224 pixels. O uso de uma resolução de imagem mais alta (parâmetro: `rf_inputResoluton`) de, por exemplo, 448x448 ou 896x896 pixels, geralmente melhora de forma significativa a precisão, mas reduz o refinamento de DNN. O **uso de uma resolução de imagem mais alta não tem praticamente nenhum custo e quase sempre aumenta a precisão**.
- Ajuste excessivo de DNN: evite ter uma lacuna grande entre a precisão do treinamento e do teste durante o refinamento de DNN (primeira figura da parte 2). Essa lacuna pode ser reduzida usando taxas de abandono `rf_dropoutRate` de 0,5 ou mais e aumentando o peso do recurso `rf_l2RegWeight`. O uso de uma taxa alta de abandono pode ser especialmente útil se a resolução de imagem de entrada de DNN é alta.
- Tente usar DNNs mais profundas alterando `rf_pretrainedModelFilename` de `ResNet_18.model` para `ResNet_34.model` ou `ResNet_50.model`. O modelo Resnet-50 não é apenas mais profundo, mas a saída da penúltima camada tem o tamanho de 2.048 flutuações (vs. 512 flutuações dos modelos ResNet-18 e ResNet-34). Essa dimensão maior pode ser especialmente útil durante o treinamento de um classificador de SVM.

## <a name="part-3---custom-dataset"></a>Parte 3 – Conjunto de dados personalizado

Nas partes 1 e 2, treinamos e avaliamos um modelo de classificação de imagens usando as imagens de texturas de vestuário para a parte superior do corpo fornecidas. Agora mostramos como usar um conjunto de dados personalizado fornecido pelo usuário. Ou, se ele não estiver disponível, como gerar e anotar um conjunto de dados como esse usando a Pesquisa de Imagem do Bing.

### <a name="using-a-custom-dataset"></a>Usando um conjunto de dados personalizado

Primeiro, vamos dar uma olhada na estrutura de pastas dos dados de textura de vestuário. Observe como todas as imagens para os diferentes atributos estão nas respectivas subpastas *dotted*, *leopard e *striped* em *DATA_DIR/images/fashionTexture/*. Observe ainda como o nome da pasta da imagem também ocorre no arquivo `PARAMETERS.py`:
```python
datasetName = "fashionTexture"
```

O uso de um conjunto de dados personalizado é tão simples quanto reproduzir essa estrutura de pastas, nas quais todas as imagens estão em subpastas de acordo com seu atributo e copiar essas subpastas para um novo diretório especificado pelo usuário *DATA_DIR/images/newDataSetName/*. A única alteração de código necessária é definir a variável `datasetName` como *newDataSetName*. Os scripts 1 a 5 podem então ser executados na ordem e todos os arquivos intermediários são gravados em *DATA_DIR/proc/newDataSetName/*. Nenhuma outra alteração de código é necessária.

É importante que cada imagem possa ser atribuída a exatamente um atributo. Por exemplo, seria errado ter atributos para “animal” e “leopardo”, pois uma imagem de “leopardo” também pertenceria a “animal”. Além disso, é melhor remover imagens ambíguas e, portanto, difíceis de serem anotadas.



### <a name="image-scraping-and-annotation"></a>Captura de imagem e anotação

Pode ser difícil coletar um número suficientemente grande de imagens anotadas para treinamento e teste. Uma maneira de resolver esse problema é capturar imagens da Internet. Por exemplo, veja abaixo os resultados da Pesquisa de Imagem do Bing para a consulta *camiseta listrada*. Conforme esperado, a maioria das imagens na verdade são camisetas listradas. Algumas imagens incorretas ou ambíguas (como a coluna 1, linha 1; ou a coluna 3, linha 2) podem ser identificadas e removidas facilmente:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Para gerar um conjunto de dados grande e diversificado, várias consultas devem ser usadas. Por exemplo, 7\*3 = 21 consultas podem ser sintetizadas automaticamente usando todas as combinações de itens de vestuário {blusa, capuz, pulôver, suéter, camiseta, colete} e atributos {listrado, bolinhas e estampa de leopardo}. O download das 50 principais imagens por consulta levaria a um máximo de 21*50=1050 imagens.

Em vez de baixar manualmente as imagens da Pesquisa de Imagem do Bing, é muito mais fácil de usar a [API de Pesquisa de Imagem do Bing dos Serviços Cognitivos](https://www.microsoft.com/cognitive-services/bing-image-search-api) que retorna um conjunto de URLs de imagem considerando uma cadeia de consulta.

Algumas imagens baixadas são exatas ou quase duplicatas (por exemplo, diferem apenas por resolução de imagem ou artefatos de jpg). Essas duplicatas devem ser removidas para que a divisão de teste e treinamento não contenha as mesmas imagens. A remoção de imagens duplicadas pode ser obtida usando uma abordagem baseada em hash, que funciona em duas etapas: (i) primeiro, a cadeia de caracteres de hash é calculada para todas as imagens; (ii) em uma segunda passagem sobre as imagens, somente essas imagens são mantidas com uma cadeia de caracteres de hash que ainda não foi vista. Todas as outras imagens são descartadas. Descobrimos que a abordagem `dhash` na biblioteca do Python `imagehash` e descrita neste [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) tem um bom desempenho, com o parâmetro `hash_size` definido como 16. Não há problemas em remover incorretamente algumas imagens não duplicadas, desde que a maioria das duplicatas reais seja removida.





## <a name="conclusion"></a>Conclusão

Alguns dos principais destaques desse exemplo são:
- Código para treinar, avaliar e implantar modelos de classificação de imagens.
- Imagens de demonstração fornecidas, mas facilmente adaptáveis (alteração de uma linha) para usar seu próprio conjunto de dados de imagem.
- Recursos de especialistas de última geração implementados para treinar modelos de alta precisão com base no Aprendizado de Transferência.
- Desenvolvimento de modelos interativos com o Azure Machine Learning Workbench e o Jupyter Notebook.


## <a name="references"></a>Referências

[1] Alex Krizhevsky, Ilya Sutskever e Geoffrey E. Hinton, [_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren e Jian Sun, [_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
