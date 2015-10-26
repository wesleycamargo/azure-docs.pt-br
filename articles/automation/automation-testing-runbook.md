<properties 
	pageTitle="Testando um runbook na Automação do Azure"
	description="Antes de publicar um runbook na Automação do Azure, você pode testá-lo para garantir que ele funcione conforme o esperado. Este artigo descreve como testar um runbook e exibir sua saída."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />

# Testando um runbook na Automação do Azure
Quando você testa um runbook, a [Versão de rascunho](automation-creating-importing-runbook.md#publishing-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [Saída](automation-runbook-output-and-messages.md#output-stream) e [Aviso e erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no Painel de saída de Teste. Mensagens para o [Fluxo detalhado](automation-runbook-output-and-messages.md#message-streams) são exibidas no Painel de saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) estiver definida para Continuar.

Mesmo que a versão de rascunho esteja sendo executada, o runbook ainda executa o fluxo de trabalho normalmente e executa qualquer ação em relação aos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks em recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo e não há nenhuma diferença entre estar o editor de texto e o editor gráfico no portal de visualização do Azure.


## Para testar um runbook no portal de visualização do Azure

Você pode trabalhar com qualquer [tipo de runbook](automation-runbook-types.md) no portal de visualização do Azure.

1. Abra a versão de Rascunho do runbook no [editor de texto](automation-editing-a-runbook#Portal) ou [editor gráfico](automation-graphical-authoring-intro.md).
2. Clique no botão **Testar** para abrir a folha de Teste.
3. Se o runbook tiver parâmetros, eles serão listados no painel à esquerda, em que você pode fornecer valores a serem usados para o teste.
4. Se você quiser executar o teste em um [Hybrid Runbook Worker](automation-hybrid), altere **Configurações de Execução** para **Hybrid Worker** e selecione o nome do grupo de destino. Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
5. Clique no botão **Iniciar** para iniciar a origem.
6. Se o runbook for [Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [Gráfico](automation-runbook-types.md#graphical-runbooks), você pode interromper ou suspendê-lo enquanto está sendo testado com os botões abaixo do Painel de saída. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
7. Inspecione a saída do runbook no painel de saída.



## Para testar um runbook no Portal do Azure

No portal do Azure, você só poderá trabalhar com os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).


1. [Abra a versão de rascunho do runbook](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Clique no botão **Testar** para iniciar o teste. Se o runbook tiver parâmetros, você receberá uma caixa de diálogo para fornecer os valores a serem usados para o teste.
6. Você pode interromper ou suspender o runbook enquanto ele está sendo testado com os botões abaixo do Painel de saída. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
7. Inspecione a saída do runbook no painel de saída.


## Artigos relacionados

- [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
- [Runbooks gráficos na Automação do Azure](automation-graphical-authoring-intro.md)
- [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md)
- [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO3-->