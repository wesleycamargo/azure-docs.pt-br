<properties 
	pageTitle="Testando um runbook na Automação do Azure | Microsoft Azure"
	description="Antes de publicar um runbook na Automação do Azure, você pode testá-lo para garantir que ele funcione conforme o esperado. Este artigo descreve como testar um runbook e exibir sua saída."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/12/2016"
	ms.author="magoedte;bwren" />

# Testando um runbook na Automação do Azure
Quando você testa um runbook, a [Versão de rascunho](automation-creating-importing-runbook.md#publishing-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [Saída](automation-runbook-output-and-messages.md#output-stream) e [Aviso e erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no Painel de saída de Teste. Mensagens para o [Fluxo detalhado](automation-runbook-output-and-messages.md#message-streams) são exibidas no Painel de saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) estiver definida para Continuar.

Mesmo que a versão de rascunho esteja sendo executada, o runbook ainda executa o fluxo de trabalho normalmente e executa qualquer ação em relação aos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks em recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo e não há nenhuma diferença entre testar o editor de texto e o editor gráfico no Portal do Azure.


## Para testar um runbook no Portal do Azure

Você pode trabalhar com qualquer [tipo de runbook](automation-runbook-types.md) no Portal do Azure.

1. Abra a versão de Rascunho do runbook no [editor de texto](automation-editing-a-runbook.md#Portal) ou [editor gráfico](automation-graphical-authoring-intro.md).
2. Clique no botão **Testar** para abrir a folha de Teste.
3. Se o runbook tiver parâmetros, eles serão listados no painel à esquerda, em que você pode fornecer valores a serem usados para o teste.
4. Se você quiser executar o teste em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), altere **Configurações de Execução** para **Hybrid Worker** e selecione o nome do grupo de destino. Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
5. Clique no botão **Iniciar** para iniciar a origem.
6. Se o runbook for [Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [Gráfico](automation-runbook-types.md#graphical-runbooks), você pode interromper ou suspendê-lo enquanto está sendo testado com os botões abaixo do Painel de saída. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
7. Inspecione a saída do runbook no painel de saída.


## Próximas etapas

- Para saber como criar ou importar um runbook, veja [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
- Para saber mais sobre a Criação Gráfica, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
- Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre como configurar runbooks para retornar mensagens de status e erros, incluindo práticas recomendadas, confira [Saída e mensagens de runbook na Automação do Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0914_2016-->