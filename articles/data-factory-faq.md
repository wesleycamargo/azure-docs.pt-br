<properties title="Azure Data Factory - Frequently Asked Questions" pageTitle="Azure Data Factory - Perguntas frequentes" description="Frequently asked questions about Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure Data Factory - Perguntas frequentes

**P: O que é o Azure Data Factory?**

O Data Factory é um serviço totalmente gerenciado para os desenvolvedores comporem os serviços de processamento, movimento e armazenamento de dados em alta disponibilidade, pipelines de dados tolerantes a falhas. O Data Factory opera em armazenamento de dados em nuvem e local. Uma pipeline é um conjunto de entradas de dados, atividades de processamento e saídas de dados e é definida com o script JSON simples e ativada por meio de comandos do PowerShell. Uma vez ativada, o Data Factory orquestra e agenda as pipelines para executar no HDInsight (Hadoop) com as opções de gerenciamento de cluster automático em nome do usuário. O Data Factory também fornece um gerenciamento visual e experiência de monitoramento por meio do Portal de visualização do Azure para monitorar todas as pipelines com detalhes sobre operações e informações de integridade do serviço em um painel de controle.
 
**P: Que desafio o cliente faz com que o Data Factory resolva?**

O Azure Data Factory equilibra a agilidade de armazenamento de dados aprimorados diversificados, serviços de processamento e movimentação através do armazenamento relacional tradicional junto com dados não estruturados, com o controle e o monitoramento de recursos de um serviço totalmente gerenciado.

**P: Quem é o público-alvo do Data Factory?**


- Desenvolvedores de dados: Quem é responsável por criar serviços de integração entre o Hadoop e outros sistemas:
	- Deve acompanhar e integrar com um cenário de dados crescente e alteração continuamente
	- Deve escrever o código personalizado para a produção de informações, e ele é caro, difícil de manter e pouco disponível ou tolerante a falhas

- Profissionais de TI: Quem pretende incorporar mais dados diversificados dentro de sua infraestrutura de TI:
	- Necessário para pesquisar em todos os dados de uma organização para obter resultados de negócios sofisticados
	- Deve gerenciar recursos de computação e armazenamento para o contabilização de custo e escala local e na nuvem
	- Deve adicionar rapidamente o processamento e origens diversas para direcionar as necessidades dos novos negócios, mantendo a visibilidade em todos os ativos de computação e armazenamento

**P: Onde posso encontrar detalhes de preços do Azure Data Factory?**

Consulte a [página de detalhes de preços do Data Factory][adf-pricing-details] para os detalhes de preços para o Data Factory do Azur.  

**P. Como faço para começar a utilizar o Azure Data Factory?**

- Para obter uma visão geral do Azure Data Factory, consulte [Introdução ao Azure Data Factory][adf-introduction].
- Para obter um tutorial rápido, consulte [Introdução ao Azure Data Factory][adfgetstarted].
- Para obter a documentação abrangente, consulte a [Documentação do Azure Data Factory][adf-documentation-landingpage].
 
**P: Quais são as fontes de dados e atividades com suporte?**

- **Fontes de dados com suporte para:** armazenamento do Azure (Blob e tabelas), SQL Server, banco de dados SQL do Azure. 
- **Atividades com suporte para:**: Atividade de cópia (do local para a nuvem e da nuvem para local), atividade de HDInsight (transformações Pig e Hive) e atividades personalizadas do c#.
  
**P: Como os clientes podem acessar o Data Factory?**

Os clientes podem obter acesso ao Data Factory por meio do [Portal de visualização do Azure][azure-preview-portal].

**P: Qual é a disponibilidade regional do Data Factory?**

Em visualização pública, o Data Factory só estará disponível no Oeste dos EUA.  Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões.
 
**P: Quais são os limites de número de fábricas/pipelines/atividades/conjuntos de dados?** 


- Número de data factories dentro de uma assinatura: 50
- Número de pipelines em um data factory: 100
- Número de atividades dentro de uma pipeline: 10
- Número de conjuntos de dados em um data factory: 100

**P: Quais as regiões são compatíveis com a Atividade de cópia?**

A atividade de cópia dá suporte à cópia de dados para as seguintes áreas: Leste dos EUA 2, oeste dos EUA, norte da Europa, Europa Ocidental e sudeste da Ásia.

Também há suporte para a cópia de dados para outras regiões, utilizando uma das cinco regiões acima para rotear os dados.  A operação de cópia é medida com base na região de onde os dados são roteados.

Região de destino da cópia | Região utilizada para roteamento
-------------------------- | -----------------------
Leste dos EUA | Leste dos EUA 2
Centro dos EUA | Leste dos EUA 2
Centro norte dos EUA | Leste dos EUA 2
Centro Sul dos EUA | Oeste dos EUA
Leste da Ásia | Sudeste da Ásia
Leste do Japão | Oeste dos EUA
Oeste do Japão | Oeste dos EUA
Sul do Brasil | Leste dos EUA 2

**P: Quando utilizar um cluster HDInsight, que são as regiões com suporte para HDInsight?**

Consulte a seção de disponibilidade geográfica no seguinte artigo: ou [Detalhes de preços do HDInsight][hdinsight-supported-regions].

**P: Qual região é utilizada por um cluster de HDInsight sob demanda?**

O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.    

[adfgetstarted]: ../data-factory-get-started

[adf-introduction]: ../data-factory-introduction
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pt-br/pricing/details/hdinsight/
