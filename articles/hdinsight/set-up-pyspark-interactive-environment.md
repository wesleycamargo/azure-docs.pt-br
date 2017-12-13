---
title: Ferramentas do Azure HDInsight - Configurar o ambiente interativo do PySpark para Visual Studio Code | Microsoft Docs
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 5a64023df813262c461b9d772b722ebd613369ed
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para o Visual Studio Code

As etapas a seguir mostram como instalar pacotes do Python ao executar o **HDInsight: PySpark Interactive**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Configurar o ambiente interativo do PySpark no MacOS e Linux
Se você estiver usando o **python 3.x**, precisará usar o comando **pip3** para as seguintes etapas:

1. Garanta que **Python** e **pip** estejam instalados.
 
    ![Versão do pip do Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instale o Jupyter.
    ```
    sudo pip install jupyter
    ```
   Você verá a seguinte mensagem de erro no Linux e no macOS:

   ![Erro 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Instale o **libkrb5-dev** (somente para Linux). A seguinte mensagem de erro poderá ser exibida:

   ![Erro 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Instale o **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Verifique se **ipywidgets** está instalado corretamente executando o seguinte:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Instalar os kernels do wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Instale os kernels do wrapper. Execute **pip show sparkmagic**. A saída mostra o caminho para a instalação do **sparkmagic**. 

    ![sparkmagic location](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Vá ao local e, em seguida, execute:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Verifique o status da instalação.

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Para os kernels disponíveis: 
    - **python2** e **pysparkkernel** correspondem a **python 2.x**. 
    - **python3** e **pyspark3kernel** correspondem a **python 3.x**. 

8. Reinicie o VS Code e volte para o editor de scripts que está executando o **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração
* HDInsight para VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Usar o Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Usar o Zeppelin para executar consultas do Hive no Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
