<properties 
	pageTitle="O que é o Data Factory? Serviço de integração de dados | Microsoft Azure" 
	description="Saiba o que é o Azure Data Factory: um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados." 
	keywords="integração de dados, integração de dados de nuvem, o que é o azure data factory"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/26/2016" 
	ms.author="spelluru"/>

# Introdução ao serviço Azure Data Factory, um serviço de integração de dados na nuvem

## O que é o Data Factory do Azure? 
O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. Assim como uma fábrica que usa equipamentos para pegar matérias-primas e transformá-las em produtos, o Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato.

O Data Factory funciona com fontes de dados e SaaS locais e na nuvem para ingerir, preparar, transformar, analisar e publicar seus dados. Use o Data Factory para transformar serviços em pipelines de fluxo de dados gerenciados e para transformar seus dados, usando serviços como [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) e [Lote do Azure](https://azure.microsoft.com/documentation/services/batch/) para suas necessidades de computação de big data, e com o [Aprendizado de Máquina do Azure](https://azure.microsoft.com/documentation/services/machine-learning/) para operacionalizar suas soluções de análise. Vá além de uma simples exibição de monitoramento tabular e use as visualizações de dados avançadas do Data Factory para exibir rapidamente a linhagem e as dependências entre os pipelines de dados. Monitore todos os seus pipelines de fluxo de dados, de uma única exibição unificada para identificar facilmente os problemas e configurar alertas de monitoramento.

![Diagrama: visão geral do Data Factory, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figura1.** Você pode coletar dados de várias fontes de dados de diferentes locais, ingerir e prepará-los, organizar e analisá-los com uma variedade de transformações e publicar dados prontos para uso para consumo.

Você pode usar o Data Factory sempre que precisar coletar dados de diferentes formas e tamanhos, transformá-los e publicá-los para extrair informações aprofundadas – tudo em um cronograma confiável. O Data Factory é usado para criar pipelines de fluxo de dados altamente disponíveis para diversos cenários em vários setores diferentes de acordo com as necessidades de pipeline de análise. Lojas online o utilizam para gerar [recomendações de produtos](data-factory-product-reco-usecase.md) personalizadas com base no comportamento de navegação do cliente. Estúdios de jogos o utilizam para compreender a [eficácia de suas campanhas de marketing](data-factory-customer-profiling-usecase.md). Aprenda diretamente com nossos clientes como e por que eles usam o Data Factory ao analisar os [Estudos de caso de cliente](data-factory-customer-case-studies.md).

> [AZURE.VIDEO azure-data-factory-overview]

## Principais Conceitos

O Azure Data Factory tem algumas entidades principais que trabalham juntas para definir os dados de entrada e saída, eventos de processamento e o cronograma e os recursos necessários para executar o fluxo de dados desejado.

![Diagrama: Data Factory, um serviço de integração de dados de nuvem - conceitos principais](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**Figura 2.** Relações entre o Conjunto de dados, Atividade, Pipeline e Serviço vinculado


### Atividades
As Atividades definem as ações a serem realizadas em seus dados. Cada atividade obtém nenhum ou mais [conjuntos de dados](data-factory-create-datasets.md) como entradas e produz um ou mais conjuntos de dados como saídas. Uma atividade é uma unidade de orquestração no Azure Data Factory. Por exemplo, você pode usar uma [atividade de Cópia](data-factory-data-movement-activities.md) para orquestrar a cópia de dados de um conjunto de dados para outro. Da mesma forma, você pode usar uma [atividade do Hive](data-factory-data-transformation-activities.md) que executará uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Azure Data Factory fornece uma ampla gama de atividades de movimentação, análise e transformação de dados.

### Pipelines
[Pipelines](data-factory-create-pipelines.md) são um agrupamento lógico de Atividades. Eles são usados para agrupar atividades em uma unidade que, em conjunto, executam uma tarefa. Por exemplo, uma sequência de várias Atividades de transformação poderá ser necessária para limpar dados do arquivo de log. Essa sequência pode ter um cronograma complexo e as dependências que precisam ser coordenadas e automatizadas. Todas essas atividades poderiam ser agrupadas em um único Pipeline chamado "CleanLogFiles". "CleanLogFiles", em seguida, poderia ser implantado, agendado ou excluído como uma única unidade em vez de gerenciar cada atividade individual de forma independente.

### Conjunto de dados
[Conjuntos de dados](data-factory-create-datasets.md) são referências/ponteiros nomeados para os dados que você deseja usar como uma entrada ou uma saída de uma Atividade. Conjuntos de dados identificam estruturas de dados em armazenamentos de dados diferentes, inclusive tabelas, arquivos, pastas e documentos.

### Serviço vinculado
Serviços vinculados definem as informações necessárias para o Data Factory se conectar a recursos externos. Serviços vinculados são usados para duas finalidades no Data Factory:

- Para representar um armazenamento de dados, incluindo, mas não se limitando a um SQL Server local, banco de dados Oracle, compartilhamento de arquivos ou uma conta de Armazenamento de Blobs do Azure. Conforme discutido anteriormente, conjuntos de dados representam as estruturas nos armazenamentos de dados conectados ao Data Factory por meio de um Serviço vinculado.
- Para representar um recurso de computação que pode hospedar a execução de uma Atividade. Por exemplo, a "Atividade HDInsightHive" é executada em um cluster Hadoop do HDInsight.

Com os quatro conceitos simples de conjuntos de dados, atividades, pipelines e serviços vinculados, você está pronto para começar! Você pode [criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) desde o início ou implantar uma amostra pronta seguindo as instruções em nosso artigo [Exemplos de Data Factory](data-factory-samples.md).

## Regiões com suporte
No momento, você pode criar fábricas de dados nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite criar fluxos controlados por dados para orquestrar a movimentação de dados entre [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores) e o processamento de dados usando [serviços de computação](data-factory-compute-linked-services.md) em outras regiões ou em um ambiente local. Ele também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de interface do usuário.

Observe que, embora o Azure Data Factory só esteja disponível nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**, o serviço que capacita a movimentação de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se um repositório de dados estiver por trás de um firewall, um [Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no ambiente local moverá os dados.

Por exemplo, digamos que seu(s) ambiente(s) de computação, como o cluster Azure HDInsight e o Aprendizado de Máquina do Azure, esteja(m) ficando sem a região Europa Ocidental. Você pode criar e tirar proveito de uma instância do Azure Data Factory na Europa Setentrional e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Leva alguns milissegundos para o serviço Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

No futuro, pretendemos ter o Azure Data Factory em todas as regiões com suporte do Azure.
  

<!---HONumber=AcomDC_0629_2016-->