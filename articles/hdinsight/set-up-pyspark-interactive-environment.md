---
title: Ferramentas do Azure HDInsight - Configurar o ambiente interativo do PySpark para Visual Studio Code
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
keywords: VSCode, Ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive Interativo, Consulta Interativa
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: bf47915ba93a4a3a7dec338395cfe0ce6aa3cdf6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993802"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para o Visual Studio Code

As etapas a seguir mostram como instalar pacotes do Python, executando **HDInsight: PySpark Interativo**.

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

8. Reinicie o VS Code e, em seguida, retorne para o editor de scripts que está executando o **HDInsight: PySpark Interativo**.

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração
* HDInsight para VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Use o Azure Toolkit for IntelliJ para criar e enviar aplicativos Scala do Apache Spark](spark/apache-spark-intellij-tool-plugin.md)
* [ Use o Azure Toolkit for IntelliJ para depurar os aplicativos do Apache Spark remotamente por meio do SSH ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook do Jupyter no cluster do Apache Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Use o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](hdinsight-connect-hive-zeppelin.md)
