---
title: Analisando dados do sensor usando Hive e Hadoop | Microsoft Docs
description: Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o PowerView.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 80dc4e32f578bbb29ad726e99160bcb1d9d8e446
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analisar dados de sensor usando o Console de consulta Hive no Hadoop HDInsight

Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o Power View.

> [!IMPORTANT]
> As etapas neste documento só funcionam com clusters HDInsight baseados no Windows. O HDInsight está disponível somente no Windows para versões inferiores ao HDInsight 3.4. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).


Neste exemplo, usamos o Hive para processar dados históricos e identificar problemas com sistemas de aquecimento e refrigeração. Especificamente, você pode identificar sistemas não conseguem manter uma temperatura definida de maneira confiável executando as seguintes tarefas:

* Crie tabelas do HIVE para consultar dados armazenados em arquivos com valores separados por vírgulas (CSV).
* Criar consultas do HIVE para analisar os dados.
* Para recuperar os dados analisados, use o Microsoft Excel para conectar-se ao HDInsight.
* Use o Power View para visualizar os dados.

![Um diagrama da arquitetura da solução](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight (Hadoop): veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) para obter informações sobre como criar um cluster.
* Microsoft Excel 2013

  > [!NOTE]
  > O Microsoft Excel é usado para visualização de dados no [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Driver ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Para executar a amostra

1. No seu navegador da Web, navegue até a URL a seguir: 

         https://<clustername>.azurehdinsight.net

    Substitua `<clustername>` pelo nome do cluster HDInsight.

    Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar esse cluster.

2. Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Soluções com dados de amostra**, clique na amostra **Análise de dados do sensor**.

    ![Imagem da galeria de Introdução](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Siga as instruções fornecidas na página da Web para concluir a amostra.

