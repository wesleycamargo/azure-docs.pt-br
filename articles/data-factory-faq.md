<properties title="Azure Data Factory - Frequently Asked Questions" pageTitle="Azure Data Factory - Perguntas frequentes" description="Perguntas frequentes sobre o Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Azure Data Factory - Perguntas frequentes

**P: O que é o Data Factory do Azure?**

O Data Factory é um serviço totalmente gerenciado para os desenvolvedores comporem os serviços de processamento, movimento e armazenamento de dados em alta disponibilidade, pipelines de dados tolerantes a falhas. O Data Factory opera em armazenamento de dados em nuvem e local. Uma pipeline é um conjunto de entradas de dados, atividades de processamento e saídas de dados e é definida com o script JSON simples e ativada por meio de comandos do PowerShell. Uma vez ativada, o Data Factory orquestra e agenda as pipelines para executar no HDInsight (Hadoop) com as opções de gerenciamento de cluster automático em nome do usuário. O Data Factory também fornece um gerenciamento visual e experiência de monitoramento por meio do Portal de Visualização do Azure para monitorar todas as pipelines com detalhes sobre operações e informações de integridade do serviço em um painel de controle.
 
**P: Que desafio o cliente faz com que o Data Factory resolva?**

O Azure Data Factory equilibra a agilidade de armazenamento de dados aprimorados diversificados, serviços de processamento e movimentação através do armazenamento relacional tradicional junto com dados não estruturados, com o controle e o monitoramento de recursos de um serviço totalmente gerenciado.

**P: Quem é o público-alvo do Data Factory?**


- Desenvolvedores de Dados: são responsáveis por criar serviços de integração entre o Hadoop e outros sistemas:
	- Deve acompanhar e integrar com um cenário de dados crescente e alteração continuamente
	- Deve escrever o código personalizado para a produção de informações, e ele é caro, difícil de manter e pouco disponível ou tolerante a falhas

- Especialistas em TI: pretendem incorporar mais dados diversificados dentro de sua infraestrutura de TI:
	- Necessário para pesquisar em todos os dados de uma organização para obter resultados de negócios sofisticados
	- Deve gerenciar recursos de computação e armazenamento para o contabilização de custo e escala local e na nuvem
	- Deve adicionar rapidamente o processamento e origens diversas para direcionar as necessidades dos novos negócios, mantendo a visibilidade em todos os ativos de computação e armazenamento

**P: Onde posso encontrar detalhes de preços do Data Factory do Azure?**

Consulte a [página de Detalhes de preços do Data Factory][adf-pricing-details] para os detalhes de preços para o Data Factory do Azure.  

**P. Como faço para começar a utilizar o Data Factory do Azure?**

- Para obter uma visão geral do Data Factory do Azure, consulte [Introdução ao Data Factory do Azure][adf-introduction].
- Para obter um tutorial rápido, consulte [Introdução ao Data Factory do Azure][adfgetstarted].
- Para obter a documentação abrangente, consulte a [Documentação do Data Factory do Azure][adf-documentation-landingpage].
 
**P: Quais são as fontes de dados e atividades com suporte?**

- **Fontes de dados com suportes:** Armazenamento do Azure (Blob e Tabelas), SQL Server, Banco de dados do SQL Azure.
- **Atividades com suporte para:**: Atividade de Cópia (do local para a nuvem e da nuvem para local), Atividade de HDInsight (transformações Pig e Hive) e atividades personalizadas do C#.
  
**P: Como os clientes podem acessar o Data Factory?**

Os clientes podem obter acesso ao Data Factory por meio do [Portal de Visualização do Azure][azure-preview-portal].

**P: Qual é a disponibilidade regional do Data Factory?**

Em visualização pública, o Data Factory só estará disponível no Oeste dos EUA.  Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões.
 
**P: Quais são os limites de número de fábricas/pipelines/atividades/conjuntos de dados?** 


- Número de data factories dentro de uma assinatura: 50
- Número de pipelines em um data factory: 100
- Número de atividades dentro de uma pipeline: 10
- Número de conjuntos de dados em um data factory: 100

**P: Quais as regiões com suporte para a Atividade de Cópia?**

A atividade de cópia dá suporte à cópia de dados para as seguintes áreas: Leste dos EUA 2, oeste dos EUA, norte da Europa, Europa Ocidental e sudeste da Ásia.

Também há suporte para a cópia de dados para outras regiões, utilizando uma das cinco regiões acima para rotear os dados.  A operação de cópia é medida com base na região de onde os dados são roteados.

Região de destino da cópia | Região para roteamento
-------------------------- | -----------------------
Leste dos EUA | Leste dos EUA 2
Centro dos EUA | Leste dos EUA 2
Centro-Norte dos EUA | Leste dos EUA 2
Centro-Sul dos EUA | Oeste dos EUA
Ásia Oriental | Sudeste da Ásia
Leste do Japão | Oeste dos EUA
Oeste do Japão | Oeste dos EUA
Sul do Brasil | Leste dos EUA 2

**P: Quando utilizar um cluster HDInsight, quais são as regiões com suporte para HDInsight?**

Consulte a seção de Disponibilidade Geográfica no seguinte artigo: ou em [Detalhes de Preços do HDInsight][hdinsight-supported-regions].

**P: Qual região é utilizada por um cluster HDInsight sob demanda?**

O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.    

## Consulte também
[Introdução ao Data Factory do Azure][adf-introduction]
[Introdução ao Data Factory do Azure][adfgetstarted]
[Guia de solução de problemas do Data Factory][adf-troubleshoot]


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pt-br/pricing/details/hdinsight/

<!--HONumber=35.2-->
