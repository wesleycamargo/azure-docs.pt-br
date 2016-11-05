---
title: Estender seu experimento com R | Microsoft Docs
description: Como estender a funcionalidade do Estúdio de Aprendizado de Máquina do Azure por meio da linguagem R usando o módulo Executar o Script R.
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
# Estender seu experimento com R
Você pode estender a funcionalidade do Estúdio AM por meio da linguagem R usando o módulo [Executar o Script R][execute-r-script].

Esse módulo aceita vários conjuntos de dados de entrada e produz um único conjunto de dados como saída. Você pode digitar um script R no parâmetro **Script R** do módulo [Executar Script R][execute-r-script].

Você pode acessar cada porta de entrada do módulo usando código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Listando todos os pacotes instalados no momento
A lista de pacotes instalados pode mudar. Para obter uma lista completa e atual dos pacotes instalados, incluindo a descrição de cada pacote, digite o seguinte código para o módulo [Executar Script R][execute-r-script]\:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Isso envia a lista de pacotes para a porta de saída do módulo [Executar Script R][execute-r-script]. Para exibir a lista de pacotes, conecte um módulo de conversão, como o [Converter para CSV][convert-to-csv] na saída à esquerda do módulo [Executar Script R][execute-r-script], execute o experimento e, então, clique na saída do módulo de conversão e selecione **Baixar**.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)

<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## Importando pacotes
Você também pode importar pacotes que ainda não estiverem instalados em um repositório do Estúdio AM preparado usando os seguintes comandos no módulo [Executar Script R][execute-r-script] e no arquivo de pacote compactado:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

em que `my_favorite_package.zip` contém o .zip do seu pacote.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0914_2016-->