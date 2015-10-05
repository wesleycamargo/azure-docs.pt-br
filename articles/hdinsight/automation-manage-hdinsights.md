<properties
	pageTitle="Gerenciar o Azure HDInsight usando a Automação do Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Azure HDInsight."
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="elcooper"/>



#Gerenciando o Azure HDInsight usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus clusters e automatizar tarefas comuns no Azure HDInsight.

## O que é Automação do Azure?
[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas com frequência, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza a sobrecarga operacional e libere a equipe de IT e DevOps para se concentrar no trabalho que agrega valor comercial com o agendamento de suas tarefas de gerenciamento de nuvem para a execução automática na Automação do Azure.


## Como Automação do Azure ajudar a gerenciar o Azure HDInsight?

O HDInsight pode ser gerenciado na Automação do Azure usando os [cmdlets do Azure HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx) que estão disponíveis nas [ferramentas do Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). A Automação do Azure tem esses cmdlets disponíveis imediatamente, para que você possa executar suas tarefas de gerenciamento do HDInsight no serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Com os cmdlets do Azure HDInsight, você pode automatizar tarefas como provisionamento de clusters do HDInsight no Windows ou Linux, dimensionamento de clusters, gerenciamento de clusters e envio de trabalhos MapReduce. Essas são apenas algumas das muitas tarefas que você pode automatizar usando o PowerShell na Automação do Azure.


## Próximas etapas
Agora que você aprendeu as noções básicas da Automação do Azure e como ela pode ser usada para gerenciar o Azure HDInsight, siga este link para saber mais sobre a Automação do Azure.

* Consulte o [Tutorial de introdução](../automation-create-runbook-from-samples.md) da Automação do Azure.
* Veja exemplos no [Script Center](http://aka.ms/scriptcentergallery).  

 

<!---HONumber=Sept15_HO4-->