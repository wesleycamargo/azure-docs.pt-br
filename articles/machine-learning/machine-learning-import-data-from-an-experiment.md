---
title: Importar dados no Estúdio de Aprendizado de Máquina de outro experimento| Microsoft Docs
description: Como salvar os dados de treinamento no Estúdio de Aprendizado de Máquina do Azure e usá-los em outro experimento.
keywords: importar dados, dados, fontes de dados, dados de treinamento
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
ms.date: 09/16/2016
ms.author: garye;bradsev

---
# Importar os dados para o Estúdio de Aprendizado de Máquina do Azure de outro experimento
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Haverá momentos em que você vai querer pegar o resultado intermediário de um experimento e usá-lo como parte de outro experimento. Para fazer isso, salve o módulo como um conjunto de dados:

1. Clique na saída do módulo que você deseja salvar como um conjunto de dados.
2. Clique em **Salvar como Conjunto de Dados**.
3. Quando solicitado, insira um nome e uma descrição que permitiriam identificar o conjunto de dados facilmente.
4. Clique na marca de seleção **OK**.

Quando o salvamento for concluído, o conjunto de dados ficará disponível para uso em qualquer experimento do seu espaço de trabalho. Você pode encontrá-lo na lista **Conjuntos de Dados Salvos** na paleta de módulos.

<!---HONumber=AcomDC_0921_2016-->