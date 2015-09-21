<properties
	pageTitle="O que é Automação do Azure"
	description="Saiba qual valor a Automação do Azure fornece e obtenha respostas para perguntas comuns para que você pode começar a criar e usar runbooks."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/03/2015"
	ms.author="bwren"/>

# O que é Automação do Azure?

A Automação do Microsoft Azure fornece uma maneira para os usuários automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que normalmente são executadas em um ambiente de nuvem e corporativo. Este artigo fornece respostas resumidas para perguntas comuns sobre automação do Azure. Você pode consultar outros artigos desta biblioteca para obter informações mais detalhadas sobre os diferentes tópicos.

## Que valor a Automação do Azure oferece?

Automação do Azure economiza tempo e aumenta a confiabilidade das tarefas administrativas comuns que você executa em ambientes de nuvem e corporativos. Você pode implementar esses processos como runbooks que podem executar várias tarefas sem nenhuma intervenção humana e até mesmo agendá-los para serem executados automaticamente em intervalos regulares.

## O que é um runbook?

Um runbook é um conjunto de tarefas que executa um processo automatizado na Automação do Azure. Pode ser um processo simples, como iniciar uma máquina virtual ou criar uma entrada de log, ou você pode ter um runbook complexo que combina outros runbooks menores para executar um processo complexo em vários recursos ou até mesmo várias nuvens.

Por exemplo, você pode ter um processo manual existente para o provisionamento de uma nova máquina virtual que inclui várias etapas, como criar a máquina virtual, conectá-la a uma rede, atribuir um endereço IP a ela e, em seguida, notificar o usuário que está pronta. Em vez de executar cada uma dessas etapas manualmente, você pode criar um runbook que executa todas essas tarefas como um único processo. Você inicia o runbook, fornece as informações necessárias, como o nome da máquina virtual, o endereço IP e o email do destinatário, e aguarda enquanto o processo é concluído sem você.


## O que os runbooks podem automatizar?

Os runbooks na Automação do Azure são baseados no fluxo de trabalho do PowerShell, portanto eles fazem tudo o que o PowerShell pode fazer. Se um aplicativo ou serviço tiver uma API, um runbook pode trabalhar com ele. Se você tiver um módulo do PowerShell para ele, pode carregar esse módulo na Automação do Azure e incluir esses cmdlets em seu runbook. Os runbooks da Automação do Azure são executados na nuvem do Azure para que possam acessar qualquer recurso na nuvem ou recursos externos que podem ser acessados da nuvem. Usando o [Runbook Worker Híbrido](automation-hybrid-runbook-worker.md), os runbooks podem ser executados em seu data center local para gerenciar recursos locais.


## Onde posso obter runbooks?

A [Galeria de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) contém runbooks da Microsoft e da comunidade que você pode usar inalterados em seu ambiente ou personalizá-los para suas próprias finalidades. Eles também são úteis como referências para aprender a criar seus próprios runbooks. Você ainda pode contribuir na galeria com seus próprios runbooks que acredita que os outros usuários podem considerar úteis.


## Como posso criar meus próprios runbooks?

Você pode [criar seus próprios runbooks](http://msdn.microsoft.com/library/azure/dn643637.aspx) do zero ou modificar runbooks da [Galeria de Runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) para seus próprios requisitos. Se preferir trabalhar diretamente com o código do PowerShell, você pode [editar o runbook usando o editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) no portal do Azure ou editá-lo offline. Se preferir editar um runbook sem ser exposto ao código subjacente, você pode usar o [editor gráfico](automation-graphical-authoring-intro.md) no portal de visualização do Azure.


## Como a Automação do Azure se relaciona com outras ferramentas de automação?

O [SMA (Service Management Automation)](http://technet.microsoft.com/library/dn469260.aspx) destina-se a automatizar tarefas de gerenciamento na nuvem privada. Ele é instalado localmente em seu data center como um componente do [Microsoft Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx). O SMA e a Automação do Azure usam o mesmo formato de runbook com base no fluxo de trabalho do Windows PowerShell, mas o SMA não dá suporte para [runbooks gráficos](automation-graphical-authoring-intro.md).

O [System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) destina-se à automação de recursos locais. Ele usa um formato de runbook diferente da Automação do Azure e do Service Management Automation e tem uma interface gráfica para criar runbooks sem a necessidade de qualquer script. Seus runbooks são compostos por atividades de Pacotes de Integração que são escritos especificamente para o Orchestrator.

## Onde posso obter mais informações?

Uma variedade de recursos está disponível para você aprender mais sobre a Automação do Azure e criar seus próprios runbooks.

- A **Biblioteca da Automação do Azure** é onde você está agora. Os artigos nessa biblioteca fornecem a documentação completa sobre a configuração e administração da Automação do Azure e para a criação de seus próprios runbooks.
- [Cmdlets do Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fornece informações para automatizar operações do Azure usando o Windows PowerShell. Os runbooks usam esses cmdlets para trabalhar com recursos do Azure.
- O [Blog de gerenciamento](http://azure.microsoft.com/blog/topics/management) fornece as informações mais recentes sobre a Automação do Azure e outras tecnologias de gerenciamento da Microsoft. Você deve assinar esse blog para se manter atualizado com o que há de mais recente da equipe de Automação do Azure.
- [Fórum de automação](http://go.microsoft.com/fwlink/p/?LinkId=390561) permite que você poste dúvidas sobre a Automação do Azure a serem resolvidas pela Microsoft e pela comunidade de automação.

## Posso fornecer comentários?

**Envie-nos comentários!** Se você estiver procurando por uma solução de runbook de Automação do Azure ou por um módulo de integração, poste uma Solicitação de script no Script Center. Se você tiver comentários ou solicitações de recurso para a Automação do Azure, poste-os em [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Obrigado!

<!---HONumber=Sept15_HO2-->