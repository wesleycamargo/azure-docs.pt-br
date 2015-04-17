<properties 
	pageTitle="Planejar seu ambiente de ciência de dados de nuvem | Azure" 
	description="Planejar seu ambiente de ciência de dados de nuvem" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# Planejar seu ambiente de Ciência de Dados do Aprendizado de Máquina do Azure

Você precisa tomar determinadas decisões ao configurar um ambiente de ciência de dados do Aprendizado de Máquina do Azure.  As opções feitas serão baseadas no tipo, tamanho e local de origem dos seus dados, bem como o destino dos dados na nuvem.  O Processo de Ciência de Dados de Nuvem é uma série de tarefas ponta a ponta que levam você dos dados originais em uma certa origem e percorrem a criação e publicação de um modelo como um serviço Web do Azure para consumo em aplicativos.

O fluxo de trabalho do Processo de Ciência de Dados de nuvem é apresentado no **[mapa do Processo de Ciência de Dados](machine-learning-data-science-how-to-create-machine-learning-service.md)**.  Para saber mais sobre as etapas individuais no Processo de Ciência de Dados, clique nos itens relevantes no [mapa](machine-learning-data-science-how-to-create-machine-learning-service.md).

Este artigo aborda as questões a considerar ao configurar seu ambiente de Ciência de Dados de Nuvem, enumera os recursos e ferramentas que são úteis para esse processo e fornece orientação sobre como usar o mapa do Processo de Ciência de Dados de Nuvem.

## Questões a serem consideradas

Antes de começar a criar seu ambiente de Ciência de Dados de Nuvem, considere as seguintes questões.

1. **Onde seus dados estão localizados?** (Esse local é conhecido como o ***fonte de dados***.) Por exemplo:
	- Os dados estão disponíveis publicamente em um endereço HTTP.
	- Os dados residem em um local de arquivo local/na rede.
	- Os dados estão em um banco de dados do SQL Server.
	- Os dados são armazenados em um contêiner de armazenamento do Azure.
2. **Como os dados estão formatados?** Por exemplo:
    - Arquivos de separados por vírgulas ou por tabulações, descompactados.
    - Arquivos de separados por vírgulas ou por tabulações, compactados.
	- Blobs do Azure compactados ou descompactados.
	- Tabelas do SQL Server.
3. **Qual o tamanho dos seus dados?**
    - Pequeno:  Menos de 2 GB
    - Médio:  Maior que 2 GB e menor que 10 GB
	- Grande:  Maior que 10 GB
	- Muito grande:  Centenas de GB
4. **O quanto você está familiarizado com os dados?**
    - Você precisa para explorar os dados para descobrir seu esquema, distribuições de variável, valores ausentes, etc? 
	- Os dados requerem pré-processamento ou limpeza antes de poderem ser transformados em uma representação tabular? 
5. **Você planeja (ou é capaz de) mover todos os dados para o Azure?**
    - Sim, planejo copiar todo o conjunto de dados para a nuvem para processamento.
	- Não, apenas um subconjunto dos dados será copiado para o Azure.
6. **Qual é o destino preferencial de nuvem do Azure?** Por exemplo:
	- Mover os dados para blobs de armazenamento do Azure.
	- Armazenar os dados em discos rígidos virtuais montáveis do Azure.
	- Carregar os dados para um banco de dados do SQL Server em uma Máquina Virtual do Azure.
	- Mapear os dados para tabelas Hive do Azure HDInsight.

## Recursos de Ciência de Dados de Nuvem no Azure

Dependendo do cenário, talvez você também precise do seguinte:

1.  Ferramentas do Azure:  [SDK do Azure PowerShell](install-configure-powershell.md), [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/), [AzCopy](storage-use-azcopy.md) e outros
2.  Máquinas Virtuais do Azure executando o SQL Server
3.  Azure HDInsight (Hadoop)
4.  Redes Virtuais do Azure do local para o compartilhamento de arquivos do Azure
5.  Fábrica de Dados do Azure para movimentação de dados agendada


## Como usar o mapa do Processo de Ciência de Dados de Nuvem

O [mapa do Processo de Ciência de Dados de Nuvem](machine-learning-data-science-how-to-create-machine-learning-service.md) apresenta uma variedade de exercícios de ciência de dados.  O mapa mostra as principais etapas envolvidas em um fluxo de trabalho de ciência de dados típico.  Nem todas as etapas são necessárias em todos os exercício de ciência de dados.  Além disso, o processo é naturalmente iterativo e a sequência de etapas pode variar em um determinado exercício.  As respostas para as perguntas acima ajudarão você a decidir as etapas que são relevantes ao seu caso, quando elas são necessárias no processo e as condições sob as quais as iterações das etapas são necessárias.

Para ver cenários de exemplo com base no tamanho dos dados originais, local de fonte de dados e repositório de destino no Azure, consulte [Processo de Ciência de Dados de Nuvem no Aprendizado de Máquina do Azure](hmachine-learning-data-science-plan-sample-scenarios.md).



<!--HONumber=49-->