---
title: 'Azure Data Factory: perguntas frequentes | Microsoft Docs'
description: Obtenha resposta a perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045942"
---
# <a name="azure-data-factory-faq"></a>Perguntas frequentes sobre o Azure Data Factory
Este artigo fornece respostas a perguntas frequentes sobre o Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure? 
O Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado que automatiza a movimentação e a transformação dos dados. Assim como uma fábrica que usa equipamentos para transformar matérias-primas em produtos, o Azure Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato. 

Usando o Azure Data Factory, você pode criar fluxos de trabalho conduzidos por dados para mover dados entre armazenamentos de dados locais e na nuvem. E você pode processar e transformar dados usando serviços de computação como tempo de execução de integração do SSIS (SQL Server Integration Services), Azure Data Lake Analytics e Azure HDInsight. 

Com o Data Factory, você pode executar o processamento de dados em um serviço de nuvem baseado no Azure ou em seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Após criar um pipeline que executa a ação necessária, será possível agendá-lo para executar periodicamente (por exemplo, por hora, diariamente ou semanalmente), por agendamento de janela de tempo ou disparar o pipeline a partir de uma ocorrência de evento. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controle e escala 
Para dar suporte a diversos padrões e fluxos integração no data warehouse moderno, o Data Factory permite modelagem flexível de pipeline de dados que inclui paradigmas de programação de fluxo de controle total, incluindo execução condicional, ramificação em pipelines de dados e passar parâmetros explicitamente dentro e entre esses fluxos. O fluxo de controle também abrange a transformação de dados através de expedição de atividade para mecanismos de execução externos e recursos de fluxo de dados, incluindo a movimentação de dados em escala, por meio de Copiar Atividade.

O Data Factory fornece liberdade para modelar qualquer estilo de fluxo que seja necessário para integração de dados e que possa ser enviado sob demanda ou repetidamente em um agendamento. Alguns fluxos comuns que esse modelo permite são:   

- Fluxos de controle:
    - Encadear atividades em uma sequência em um pipeline.
    - Ramificar atividades em um pipeline.
    - parâmetros
        - Definir parâmetros no nível do pipeline e passar argumentos enquanto você invoca o pipeline sob demanda ou de um gatilho.
        - As atividades podem consumir os argumentos passados para o pipeline.
    - Passagem de estado personalizada
        - As saídas da atividade incluindo o estado podem ser consumidas por uma atividade posterior no pipeline.
    - Contêineres de looping
        - For-each 
- Fluxos baseados em gatilho:
    - Os pipelines podem ser acionados sob demanda ou por hora.
- Fluxos delta:
    - Use parâmetros e defina seus limites para cópia de delta ao mover tabelas de referência ou dimensão de um relational store local ou na nuvem para carregar os dados no lake. 

Para obter mais informações, consulte [Tutorial: fluxo de controle](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transformar os dados em escala com pipelines sem código
A nova experiência de ferramentas baseada em navegador fornece criação e implantação de pipeline sem código, com uma experiência moderna e interativa baseada na Web.

Para desenvolvedores de dados visuais e engenheiros de dados, a interface do usuário da Web do ADF é o ambiente de design sem código que você usará para pipelines de build. Ele é totalmente integrado ao Git do Visual Studio Online e fornece integração para CI/CD e desenvolvimento iterativo com opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs de plataforma cruzada avançada para usuários avançados
Se você for um usuário avançado e estiver procurando por uma interface programática, o ADF V2 fornece um conjunto avançado de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines usando seu IDE favorito
1.  SDK do Python
2.  CLI do Powershell
3.  Usuários de SDK do C# também podem aproveitar as APIs REST documentadas para fazer interface com ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Desenvolvimento iterativo e depuração usando ferramentas visuais
As ferramentas visuais do ADF (Azure Data Factory) permitem que você faça depuração e desenvolvimento interativo. É possível criar os pipelines e fazer execuções de teste usando o recurso Depurar na tela do pipeline sem gravar uma única linha de código. Você pode visualizar os resultados das execuções de teste na janela Saída da tela de pipeline. Após a execução de teste ser realizada com êxito, você poderá adicionar mais atividades ao pipeline e continuar a depuração de uma maneira iterativa. Além disso, é possível cancelar as execuções de teste quando estiverem em andamento. Não é necessário publicar as alterações no serviço de data factory antes de clicar em Depurar. Isso é útil nos cenários em que você quer garantir que as novas adições ou alterações funcionem conforme o esperado, antes de atualizar os fluxos de trabalho de data factory em ambientes de desenvolvimento, teste ou produção. 

### <a name="deploy-ssis-packages-to-azure"></a>Implantar pacotes do SSIS no Azure 
Se você quiser mover as cargas de trabalho do SSIS, poderá criar um Data Factory e provisionar um tempo de execução de integração do Azure-SSIS. O tempo de execução de integração do SSIS do Azure é um cluster totalmente gerenciado de VMs do Azure (nós) dedicadas à execução dos seus pacotes de SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial [Implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDKs
Se você for um usuário avançado e estiver procurando uma interface programática, o ADF fornece um conjunto avançado de SDKs que pode ser usado para criar, gerenciar ou monitorar pipelines, usando o IDE favorito. O suporte à linguagem inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoramento
É possível monitorar os Data Factories por meio do PowerShell, SDK ou das Ferramentas de Monitoramento Visual na interface do usuário do navegador. Você pode monitorar e gerenciar fluxos personalizados controlados por clock e com base em gatilho sob demanda, de maneira eficiente e eficaz. Cancele as tarefas existentes, visualize rapidamente as falhas, faça uma busca detalhada para obter mensagens de erro detalhadas e depure os problemas a partir de um único painel sem alternar o contexto ou navegar entre as telas. 

### <a name="new-features-for-ssis-in-adf"></a>Novos recursos para SSIS no ADF
Desde o lançamento inicial da Visualização Pública em 2017, o Data Factory adicionou os seguintes recursos para SSIS:

-   Suporte para mais três configurações/variantes do Banco de Dados (BD) SQL do Azure para hospedar o catálogo do SSIS de projetos/pacotes (SSISDB):
-   BD SQL do Azure com pontos de extremidades de serviço de VNet
-   MI (Instância Gerenciada)
-   Pool elástico
-   Suporte para VNet (Rede Virtual) do Azure Resource Manager sobre a VNet Clássica que será preterida no futuro – Isso permite injetar/unir o IR (tempo de execução de integração) do Azure-SSIS a uma VNet configurada para BD SQL do Azure com pontos de extremidades de serviço de VNet/MI/acesso a dados locais, consulte: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Suporte para AAD (Azure Active Directory) sobre a autenticação do SQL para conexão com SSISDB - Isso permite usar a autenticação do AAD com a MSI (Identidade de Serviço Gerenciada) do ADF
-   Suporte para trazer sua própria licença do SQL Server local para obter economias substanciais de custos com a opção do AHB (Benefício Híbrido do Azure)
-   Suporte para Enterprise Edition do IR do Azure-SSIS que permite usar recursos premium/ avançados, configuração personalizada para instalar extensões/componentes adicionais e ecossistema de terceiros, consulte: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Integração profunda do SSIS no ADF que permite invocar/disparar atividades Executar Pacote SSIS de primeira classe em pipelines do ADF e agendá-las via SSMS, consulte: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>O que é o tempo de execução de integração?
O tempo de execução de integração é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre vários ambientes de rede:

- **Movimentação de dados**: para a movimentação de dados, o Integration Runtime movimenta os dados entre os armazenamentos de dados de origem e de destino, dando suporte a conectores internos, à conversão de formato, ao mapeamento de coluna e à transferência de dados escalonável e com bom desempenho.
- **Atividades de expedição**: para a transformação, o Integration Runtime fornece a funcionalidade de executar pacotes do SSIS de maneira nativa.
- **Executar pacotes SSIS**: executa nativamente pacotes SSIS em um ambiente de computação do Azure gerenciado. O Integration Runtime também dá suporte à expedição e ao monitoramento de atividades de transformação executadas em uma variedade de serviços de computação, como Azure HDInsight, Azure Machine Learning, Banco de Dados SQL do Azure, SQL Server e muitos mais.

Você pode implantar uma ou mais instâncias do tempo de execução de integração conforme necessário para movimentar e transformar os dados. O tempo de execução de integração pode ser executado na rede pública do Azure ou em uma rede privada (local, Rede Virtual do Azure ou VPC (nuvem privada virtual) do Amazon Web Services). 

Para obter mais informações, consulte [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite do número de tempos de execução de integração?
Não há nenhum limite rígido quanto ao número de instâncias de tempo de execução de integração que você pode ter em um data factory. Entretanto, há um limite de número de núcleos de VM que o tempo de execução de integração pode usar por assinatura para a execução de pacotes do SSIS. Para saber mais, confira [Limites do Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de nível superior do Azure Data Factory?
Uma assinatura do Azure pode ter uma ou mais instâncias (ou data factories) do Azure Data Factory. O Azure Data Factory contém quatro componentes principais que trabalham juntos como uma plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. O benefício é que você pode usar um pipeline para gerenciar as atividades como um conjunto, em vez de gerenciar cada atividade individualmente. Você pode encadear as atividades em um pipeline para operá-las em sequência ou pode operá-las de modo independente, em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, é possível usar uma atividade de *Cópia* para copiar dados de um armazenamento de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam para ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense desta maneira: um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. E um conjunto de dados de Blob do Azure especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados têm duas finalidades no Data Factory:

- Para representar um *armazenamento de dados* que inclui, mas não é limitado a, uma instância do SQL Server local, uma instância de banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista de repositórios de dados com suporte, consulte [Atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode hospedar a execução de uma atividade. Por exemplo, a atividade do Hive do HDInsight é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, consulte [Transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Gatilhos
Gatilhos representam as unidades de processamento que determinam quando a execução de um pipeline é iniciada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da uma execução do pipeline. Normalmente, você instancia uma execução do pipeline passando argumentos para os parâmetros definidos no pipeline. Você pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor em uma configuração somente leitura. Você define os parâmetros em um pipeline e passa os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um gatilho ou de um pipeline que você executa manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente tipado e uma entidade que você pode reutilizar ou consultar. Uma atividade pode referenciar conjuntos de dados e consumir as propriedades estabelecidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Também é uma entidade que você pode reutilizar ou consultar.

### <a name="control-flows"></a>Fluxos de controle
Fluxos de controle coordenam as atividades de pipeline, incluindo encadeamento de atividades em uma sequência, ramificação, parâmetros que você define no nível do pipeline e argumentos passados conforme você invoca o pipeline sob demanda ou de um gatilho. Fluxos de controle também incluem transmissão de estado personalizada e contêineres de looping (ou seja, iteradores for-each).


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços Data Factory?
Para os detalhes de preço do Azure Data Factory, consulte [Detalhes de preço do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso me manter informado sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="how-can-i-schedule-a-pipeline"></a>Como fazer para agendar um pipeline? 
Você pode usar o gatilho do agendador ou gatilho de janela de tempo para agendar um pipeline. O gatilho usa uma agenda de calendário do relógio e você pode usá-la para agendar pipelines periodicamente ou usando os padrões recorrentes com base em calendário (por exemplo, semanalmente nas segundas-feiras, às 6 horas, e nas quintas-feiras, às 21 horas). Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar parâmetros para uma execução de pipeline?
Sim, os parâmetros são conceito de primeira classe de nível superior no ADF. Você pode definir parâmetros no nível do pipeline e passar argumentos durante a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>É possível definir valores padrão para os parâmetros do pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos passados para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para o pipeline e ser executada com o constructo `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de uma atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com o constructo `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como tratar normalmente o valores nulos em uma saída de atividade? 
Você pode usar o constructo `@coalesce` nas expressões para manipular valores nulos normalmente. 

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory, consulte os tutoriais a seguir:

- [Início rápido: criar um data factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copiar os dados na nuvem](tutorial-copy-data-dot-net.md)
