<properties 
	pageTitle="Planeje um ambiente de análise avançada do Aprendizado de Máquina | Microsoft Azure" 
	description="Planeje seu ambiente de análise avançada considerando perguntas-chave." 
	services="machine-learning" 
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
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Planeje seu ambiente de análise avançada do Aprendizado de Máquina do Azure

Qual cenário corresponde ao seu problema de análise quando você está se preparando para configurar um ambiente para fazer análises avançadas com o Aprendizado de Máquina do Azure? As opções que você faz com relação aos recursos que são necessários são baseadas no tipo, tamanho e local de origem dos seus dados, bem como no destino dos dados. Este artigo discute as perguntas que ajudarão você a identificar seu cenário.

Depois de ter identificado o cenário relevante, o fluxo de trabalho do ADAPT (Processo e Tecnologia de Análise Avançada) que é apresentado em [Roteiro de aprendizagem: crie soluções de análise avançadas no Azure](machine-learning-data-science-how-to-create-machine-learning-service.md). Orienta você por uma série de tarefas, da obtenção de um conjunto de dados até a criação e publicação de um modelo como um serviço Web do Azure que os aplicativos podem consumir.

Este tópico também enumera alguns dos recursos e ferramentas que são usados por esse processo de análise avançada.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Responda a essas perguntas
Responda a essas perguntas para determinar com qual você está trabalhando antes de criar seu ambiente de análises avançadas.

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
5. **Você planeja (ou é capaz de) mover todos os dados para o armazenamento do Azure?**
    - Sim, planejo copiar todo o conjunto de dados para a nuvem para processamento.
	- Não, apenas um subconjunto dos dados será copiado para o Azure.
6. **Qual é seu destino preferencial de nuvem do Azure?** Por exemplo:
	- Mover os dados para blobs de armazenamento do Azure.
	- Armazenar os dados em discos rígidos virtuais montáveis do Azure.
	- Carregar os dados para um banco de dados do SQL Server em uma Máquina Virtual do Azure.
	- Mapear os dados para tabelas Hive do Azure HDInsight.

## Qual é seu cenário?
Depois de responder às perguntas na seção anterior, você está pronto para determinar qual cenário melhor se adapta a seu caso. Os cenários de exemplo são descritos em [Cenários para análises avançadas no Aprendizado de Máquina do Azure](../machine-learning-data-science-plan-sample-scenarios.md).

## Recursos de análise avançada no Azure
Dependendo do cenário, você pode precisar de algumas das ferramentas e recursos a seguir.

1.  Ferramentas do Azure: 
	* 	[SDK do Azure PowerShell](../install-configure-powershell.md), 
	* 	[Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  Máquinas Virtuais do Azure executando o SQL Server
3.  Azure HDInsight (Hadoop)
4.  Redes Virtuais do Azure do local para o compartilhamento de arquivos do Azure
5.  Fábrica de Dados do Azure para movimentação de dados agendada






 

<!---HONumber=July15_HO5-->