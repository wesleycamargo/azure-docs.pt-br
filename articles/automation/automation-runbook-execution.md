<properties
   pageTitle="Execução de runbook na Automação do Azure"
   description="Descreve os detalhes de como um runbook na Automação do Azure é processado."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Execução de runbook na Automação do Azure


Quando você inicia um runbook na Automação do Azure, um trabalho é criado. Um trabalho é uma instância única de execução de um runbook. Um trabalhador da Automação do Azure é atribuído para executar cada tarefa. Enquanto os trabalhadores são compartilhados por várias contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador atenderá a solicitação do seu trabalho. Um único runbook pode ter vários trabalhos em execução ao mesmo tempo. Quando você exibir a lista de runbooks no portal do Azure, ela listará o status do último trabalho iniciado para cada runbook. Você pode exibir a lista de trabalhos para cada runbook a fim de acompanhar o status de cada um. Para obter uma descrição das opções de status de trabalho diferentes, confira [Status de trabalho](automation-viewing-the-status-of-a-runbook-job.md#job-statuses).

![Status do Trabalho](./media/automation-runbook-execution/job-statuses.png)


Os trabalhos terão acesso aos recursos do Azure fazendo uma conexão à sua assinatura do Azure. Eles só terão acesso aos recursos no seu data center se estes puderem ser acessados da nuvem pública.

## Permissões

Os runbooks na Automação do Azure normalmente exigem acesso a recursos em sua assinatura do Azure. [Autenticação do Azure usando o Active Directory do Azure](http://aka.ms/runbookauthor/authentication) descreve como configurar o Active Directory do Azure e [credenciais]() na Automação do Azure para autenticar em recursos do Azure. Este tópico também inclui informações sobre como usar o [cmdlet Add-AzureAccount](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) para acessar recursos do Azure em runbooks que você criar. Veja os requisitos de segurança na documentação dos runbooks individuais que você importar.

## Fração justa

Para compartilhar recursos entre todos os runbooks na nuvem, a Automação do Azure irá descarregar temporariamente qualquer trabalho depois de ser executado por 3 horas e reiniciá-lo de seu último [ponto de verificação](http://aka.ms/runbookauthor/checkpoints). Durante esse tempo, o trabalho mostrará o status Executando, aguardando recursos. Se o runbook não tiver nenhum ponto de verificação ou o trabalho não atingir o primeiro ponto de verificação antes de ser descarregado, ele será reiniciado desde o início.

Se o runbook reiniciar a partir do mesmo ponto de verificação ou desde o início do runbook três vezes consecutivas, ele será encerrado com um status Falha, aguardando recursos. Isso serve para proteger contra runbooks em execução indefinidamente sem conclusão, já que não são capazes alcançar o próximo ponto de verificação sem serem descarregados novamente. Nesse caso, você receberá a exceção com falha abaixo.

O trabalho não pode continuar a execução porque foi removido repetidamente do mesmo ponto de verificação. Verifique se o Runbook não executa operações demoradas sem persistir o estado.

Quando você criar um runbook, verifique se o tempo para executar atividades entre dois pontos de verificação não ultrapassa 3 horas. Talvez seja necessário adicionar pontos de verificação ao seu runbook para que ele não atinja esse limite de 3 horas. Você também precisa dividir operações de longa execução. Por exemplo, o runbook pode executar uma reindexação em um grande banco de dados SQL. Se essa operação única não for concluída dentro do limite de fração justa, o trabalho será descarregado e reiniciado do princípio. Nesse caso, você deve dividir a operação de reindexação em várias etapas, como a reindexação de uma tabela de cada vez, e inserir um ponto de verificação após cada operação para que o trabalho possa continuar após a conclusão da última operação.

## Artigos relacionados

- [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook)
- [Exibindo o status de um trabalho de runbook na Automação do Azure](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=58_postMigration-->