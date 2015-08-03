<properties 
	pageTitle="Planeje um ambiente de análise avançada do Aprendizado de Máquina | Microsoft Azure" 
	description="Planeje seu ambiente de análise avançada considerando perguntas-chave." 
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
	ms.date="05/29/2015" 
	ms.author="mohabib;bradsev" />


# Planeje seu ambiente de análise avançada do Aprendizado de Máquina do Azure

Você precisa tomar determinadas decisões ao configurar um ambiente de análise avançada do Aprendizado de Máquina do Azure. As opções feitas serão baseadas no tipo, tamanho e local de origem dos seus dados, bem como o destino dos dados na nuvem. O Processo de Análise Avançada é uma série de tarefas ponta a ponta que levam você dos dados originais em uma certa origem e percorrem a criação e publicação de um modelo como um serviço Web do Azure para consumo em aplicativos.

O fluxo de trabalho do Processo e Tecnologia de Análise Avançada é apresentado em [Crie soluções de análise avançada no Azure](machine-learning-data-science-how-to-create-machine-learning-service.md). Para saber mais sobre as etapas individuais no ADAPT (processo e tecnologia de análise avançada), clique nos itens relevantes presentes no guia.

Este artigo aborda as questões a considerar ao configurar seu ambiente de análise avançada, enumera os recursos e ferramentas que são úteis para esse processo e fornece diretrizes sobre como usar o guia de Processo e Tecnologia de Análise Avançada.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Questões a serem consideradas

Antes de começar a criar seu ambiente de análise avançada, considere as questões a seguir.

1. **Onde seus dados estão localizados?** (Esse local é conhecido como a ***fonte de dados***.) Por exemplo:
	- Os dados estão disponíveis publicamente em um endereço HTTP.
	- Os dados residem em um local de arquivo local/na rede.
	- Os dados estão em um banco de dados do SQL Server.
	- Os dados são armazenados em um contêiner de armazenamento do Azure.
2. **Como seus dados estão formatados?** Por exemplo:
    - Arquivos de separados por vírgulas ou por tabulações, descompactados.
    - Arquivos de separados por vírgulas ou por tabulações, compactados.
	- Blobs do Azure compactados ou descompactados.
	- Tabelas do SQL Server.
3. **Qual o tamanho dos seus dados?**
    - Pequeno: menos de 2 GB
    - Médio: mais de 2 GB e menos de 10 GB
	- Grande: maior que 10 GB
	- Muito grande: centenas de GBs
4. **Quão familiarizado você está com os dados?**
    - Você precisa para explorar os dados para descobrir seu esquema, distribuições de variável, valores ausentes, etc? 
	- Os dados requerem pré-processamento ou limpeza antes de poderem ser transformados em uma representação tabular? 
5. **Você planeja (ou é capaz de) mover todos os dados para o Azure?**
    - Sim, planejo copiar todo o conjunto de dados para a nuvem para processamento.
	- Não, apenas um subconjunto dos dados será copiado para o Azure.
6. **Qual é seu destino preferencial de nuvem do Azure?** Por exemplo:
	- Mover os dados para blobs de armazenamento do Azure.
	- Armazenar os dados em discos rígidos virtuais montáveis do Azure.
	- Carregar os dados para um banco de dados do SQL Server em uma Máquina Virtual do Azure.
	- Mapear os dados para tabelas Hive do Azure HDInsight.

## Recursos de análise avançada no Azure

Dependendo do cenário, talvez você também precise do seguinte:

1.  Ferramentas do Azure: [SDK do Azure PowerShell](../install-configure-powershell.md), [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/), [AzCopy](../storage-use-azcopy.md) e outros
2.  Máquinas Virtuais do Azure executando o SQL Server
3.  Azure HDInsight (Hadoop)
4.  Redes Virtuais do Azure do local para o compartilhamento de arquivos do Azure
5.  Fábrica de Dados do Azure para movimentação de dados agendada


## Guia Como usar o ADAPT (Processo e Tecnologia de Análise Avançada)

O guia fornecido em [Criar soluções de análise avançada no Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) apresenta uma variedade de exercícios de ciência de dados. O mapa mostra as principais etapas envolvidas em um fluxo de trabalho de análise avançada típico. Nem todas as etapas são necessárias em todos os exercício de ciência de dados. Além disso, o processo é naturalmente iterativo e a sequência de etapas pode variar em um determinado exercício. As respostas para as perguntas acima ajudarão você a decidir as etapas que são relevantes ao seu caso, quando elas são necessárias no processo e as condições sob as quais as iterações das etapas são necessárias.

Para ver cenários de exemplo com base no tamanho dos dados originais, local de fonte de dados e repositório de destino no Azure, consulte [Cenários para Processo e Tecnologia de Análise Avançada no Aprendizado de Máquina do Azure](../machine-learning-data-science-plan-sample-scenarios.md).


 

<!---HONumber=July15_HO4-->