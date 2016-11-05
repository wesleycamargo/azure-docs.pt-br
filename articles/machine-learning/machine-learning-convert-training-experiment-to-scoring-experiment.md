---
title: Converter um teste de treinamento do Aprendizado de Máquina em um teste preditivo | Microsoft Docs
description: Como converter um teste de treinamento do Aprendizado de Máquina, usado para treinar o modelo de análise preditiva, em um teste preditivo que pode ser implantado como um serviço Web.
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: garye

---
# Converter um teste de treinamento do Aprendizado de Máquina em um teste preditivo
O Aprendizado de Máquina do Azure permite compilar, testar e implantar soluções de análise preditiva.

Depois de criá-las e iterá-las em um *experimento de treinamento* para treinar seu modelo de análise preditiva e estar pronto para usá-lo para pontuar novos dados, será necessário preparar e otimizar seu experimento de pontuação. Você pode implantar esse *teste preditivo* como um serviço Web do Azure para que os usuários possam enviar dados ao seu modelo e receber as previsões do modelo.

Ao converter em um teste preditivo, você estará preparando seu modelo para ser implantado como um serviço Web. Os usuários do serviço Web enviarão dados de entrada para seu modelo e seu modelo enviará de volta os resultados da previsão. Assim, à medida que converte em um teste preditivo, é recomendável ter em mente como você espera que seu modelo seja usado por outras pessoas.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

O processo de conversão de um teste de treinamento em um teste preditivo envolve três etapas:

1. Salvar o modelo de aprendizado de máquina que você treinou e substituir o algoritmo de aprendizado de máquina e os módulos [Treinar Modelo][train-model] salvos com o seu modelo treinado.
2. Restrinja o experimento a somente os módulos necessários para pontuação. Um experimento de treinamento inclui vários módulos que são necessárias para treinamento, mas não são necessários quando o modelo está treinado e pronto para ser usado para pontuação.
3. Defina onde em seu experimento você aceitará dados do usuário do serviço Web e quais dados serão retornados.

## Botão Configurar Serviço Web
Depois de executar seu teste (botão **EXECUTAR** na parte inferior da tela de teste), o botão **Configurar Serviço Web** (selecione a opção **Serviço Web Preditivo**) executará para você as três etapas de conversão do teste de treinamento em um teste preditivo:

1. Ele salva o modelo treinado como um módulo na seção **Modelos Treinados** da paleta do módulo (à esquerda da tela do experimento) e, em seguida, substitui o algoritmo de aprendizado de máquina e os módulos [Treinar Modelo][train-model] pelo modelo treinado salvo.
2. Ele remove módulos que claramente não são mais necessários. No nosso exemplo, isso inclui os módulos [Dividir Dados][split], o segundo [Modelo de Pontuação][score-model] e [Avaliar Modelo][evaluate-model].
3. Ele cria a entrada do serviço Web e módulos de saída e adiciona-os nos locais padrão no seu experimento.

Por exemplo, o seguinte experimento treina um modelo de árvore de decisão de duas classes aumentada usando dados de censo de exemplo:

![Experimento de treinamento][figure1]

Os módulos nesse experimento executam basicamente quatro funções diferentes:

![Funções de módulo][figure2]

Ao converter esse teste de treinamento em um teste preditivo, alguns desses módulos não serão mais necessários ou terão outra finalidade:

* **Dados** – Os dados neste conjunto de dados de exemplo não são usados durante a pontuação. O usuário do serviço Web fornecerá os dados a serem pontuados. No entanto, os metadados desse conjunto de dados, tais como tipos de dados, são usados pelo modelo treinado. Portanto, é necessário manter o conjunto de dados no teste preditivo para que ele possa fornecer esses metadados.
* **Preparação** – Dependendo dos dados que serão enviados para pontuação, esses módulos podem ou não ser necessários para processar os dados de entrada.
  
    Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores ausentes e incluir colunas que não são necessárias para treinar o modelo. Por isso, um módulo [Limpar Dados Ausentes][clean-missing-data] foi incluído para lidar com valores ausentes e um módulo [Selecionar Colunas em um Conjunto de Dados][select-columns] foi incluído para excluir essas colunas extras do fluxo de dados. Se você souber que os dados que serão enviados para pontuação por meio do serviço Web não terão valores ausentes, poderá remover o módulo [Limpar Dados Ausentes][clean-missing-data]. No entanto, como o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] ajuda a definir o conjunto de recursos que estão sendo pontuados, ele deve permanecer.
* **Treinar** – Após o modelo ser treinado com êxito, você pode salvá-lo como um módulo de modelo único treinado. Substitua esses módulos individuais pelo modelo treinado salvo.
* **Pontuação** – Neste exemplo, o módulo Divisão é usado para dividir o fluxo de dados em um conjunto de dados de teste e de treinamento. No teste preditivo, isso não é necessário e pode ser removido. Da mesma forma, o segundo módulo [Modelo de Pontuação][score-model] e o módulo [Avaliar Modelo][evaluate-model] são usados para comparar os resultados dos dados de teste, de modo que esses módulos também não são necessários no teste preditivo. O módulo [Modelo de Pontuação][score-model] restante, contudo, é necessário para retornar um resultado de pontuação por meio do serviço Web.

Veja como fica nosso exemplo depois do clique em **Configurar Serviço Web**:

![Teste preditivo convertido][figure3]

Isso pode ser suficiente para preparar seu teste para implantação como um serviço Web. No entanto, convém trabalhar nos detalhes específicos do seu experimento.

### Ajustar os módulos de entrada e saída
No seu experimento de treinamento, você usou um conjunto de dados de treinamento, que foram processados para deixar os dados em um formato que o algoritmo de aprendizado de máquina necessitava. Se os dados que você espera receber por meio do serviço Web não necessitarão do processamento, você pode mover o **módulo de entrada de serviço Web** para um nó diferente no experimento.

Por exemplo, por padrão, **Configurar Serviço Web** coloca o módulo **Entrada do serviço Web** na parte superior do fluxo de dados, como mostrado na figura acima. No entanto, se os dados de entrada não necessitarem de processamento, você poderá posicionar manualmente a **Entrada do serviço Web** após os módulos de processamento de dados:

![Movendo a entrada do serviço Web][figure4]

Os dados de entrada fornecidos pelo serviço Web agora passarão diretamente para o módulo do Modelo de Pontuação sem qualquer pré-processamento.

Da mesma forma, por padrão, **Configurar Serviço Web** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço Web retornará ao usuário a saída do módulo do [Modelo de Pontuação][score-model], que inclui o vetor de dados de entrada completo, além dos resultados de pontuação.

No entanto, se desejar retornar algo diferente, como apenas os resultados da pontuação e não todo o vetor de dados de entrada, você poderá inserir um módulo [Selecionar Colunas do Conjunto de Dados][select-columns] para excluir todas as colunas, exceto os resultados de pontuação. Mova o módulo **Saída do serviço Web** para a saída do módulo [Selecionar Colunas do Conjunto de Dados][select-columns]\:

![Movendo a saída do serviço Web][figure5]

### Adicionar ou remover módulos de processamento de dados adicionais
Se houver mais módulos no seu experimento que você sabe que não será necessário durante a pontuação, eles podem ser removidos. Por exemplo, como mudamos o módulo de **Entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo [Limpar Dados Ausentes][clean-missing-data] do teste preditivo.

Nosso teste preditivo ficou assim:

![Removendo o módulo adicional][figure6]

### Adicionar parâmetros de serviço Web opcionais
Em alguns casos, talvez você queira permitir que o usuário do serviço Web altere o comportamento dos módulos quando o serviço é acessado. Os *Parâmetros de serviço Web* permitem que você faça isso.

Um exemplo comum é a configuração do módulo [Importar Dados][import-data] para que o usuário do serviço Web implantado possa especificar outra fonte de dados quando o serviço Web é acessado. Ou então, configurar o módulo [Exportar Dados][export-data] para que um destino diferente possa ser especificado.

Você pode definir os Parâmetros de Serviço Web e associá-los a um ou mais parâmetros de módulo, podendo também especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web pode então fornecer valores para esses parâmetros quando o serviço é acessado e as ações de módulo serão modificadas de acordo.

Para obter mais informações sobre Parâmetros de Serviço Web, consulte [Usando os parâmetros do serviço Web de Aprendizado de Máquina do Azure][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## Implantar o teste preditivo como um serviço Web
Agora que o teste preditivo foi devidamente preparado, você pode implantá-lo como um serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para saber mais sobre o processo completo de implantação, consulte [Implantar um serviço Web de Aprendizado de Máquina do Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

<!---HONumber=AcomDC_0914_2016-->