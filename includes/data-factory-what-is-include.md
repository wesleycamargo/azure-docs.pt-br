
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

O Azure Data Factory é um serviço de integração de dados. Ele permite que você crie fluxos de trabalho orientados a dados na nuvem. Um fluxo de trabalho é implementado como um ou mais *pipelines*. Os pipelines orquestram e automatizam a movimentação de dados e a transformação de dados. Os pipelines podem executar a seguinte sequência com seus dados:

1. Ingerir de dados de armazenamentos de dados diferentes.
2. Transformar ou processar os dados por meio de serviços de computação, como os seguintes:
    - Azure HDInsight para Hadoop
    - Spark
    - Análise Azure Data Lake
    - Azure Machine Learning
3. Publicar dados de saída para armazenamento de dados.
    - A publicação de destino pode ser um SQL Data Warehouse do Azure para que estes sejam consumidos por aplicativos de business intelligence (BI). 

Você pode agendar pipelines usando o Data Factory.

