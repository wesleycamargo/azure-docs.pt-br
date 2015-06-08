<properties
	pageTitle="Use o Backup do Azure para substituir a infra-estrutura de fitas"
	description="Saiba como Backup do Azure fornece semântica semelhante de fita que permite fazer backup e restaurar dados no Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Use o Backup do Azure para substituir a infra-estrutura de fitas

Os clientes de Backup e o System Center Data Protection Manager do Azure podem:

+ Dados de backup em agendas que melhor atendem às suas necessidades da organização

+ Manter os dados de backup por mais longas

+ Verifique o Azure precisa de uma parte de sua retenção de longo prazo (em vez de fitas).

Este artigo explica como os clientes podem habilitar políticas de backup e retenção. Os clientes que usam as fitas para abordar sua longo-prazo retenção agora precisa ter uma alternativa viável e eficiente com a disponibilidade desse recurso. O recurso está habilitado na versão mais recente do Backup do Azure (que está disponível [aqui](http://aka.ms/azurebackup_agent)). Os clientes SCDPM precisaria mover para UR5 antes de usar esse recurso.

## Qual é a agenda de Backup?
Agendamento de backup indica a frequência (ou com que freqüência) da operação de backup por exemplo, as configurações de tela abaixo indica que backup será feito diariamente às 18: 00 e à meia-noite. <br/>

![Agenda diária][1]

Os clientes também podem agendar um backup semanal, por exemplo, as configurações na tela a seguir indica que backup será feito todas as alternativo domingo e quarta-feira 9h30min e 1h00. <br/>

![Agenda semanal][2]

## Qual é a política de retenção?
Política de retenção Especifica a duração para a qual o backup deve ser armazenado. Em vez de especificar apenas uma "política simples" para todos os pontos de backup, os clientes podem especificar diferentes políticas de retenção com base em quando o backup é feito. Para, por exemplo, o ponto de backup feito no final de cada trimestre talvez precisem ser preservados por mais tempo para fins de auditoria, enquanto o ponto de backup feito diariamente (que funciona como uma recuperação operacional ponto) deve ser preservado por 90 dias. <br/>

![Política de retenção][3]

O número total de pontos de retenção"" especificada na diretiva é 90 (pontos de diários) + 40 (um para cada trimestre há 10 anos) = 130.

## Exemplo – juntando ambos
<br/> ![Tela de exemplo][4]

1. **a política de retenção diária**: os Backups diários são armazenados por 7 dias.
2. **a política de retenção semanal**: Backups feitos cada à meia-noite e 18: 00 Sábado será preservado por 4 semanas
3. **a política de retenção mensal**: Backups feitos em meia-noite e 18: 00 no último sábado de cada mês serão preservados para 12months
4. **Anual política de retenção**: Backups feitos à meia-noite no último sábado de março, todos serão preservados por 10 anos

O número total de pontos de retenção"" (dos quais um cliente pode restaurar dados pontos) no diagrama acima é calculado da seguinte forma:

+ 2 pontos por dia para recuperação de 7 dias = 14 pontos

+ 2 pontos por semana por 4 semanas = 8 pontos de recuperação

+ 2 pontos por mês para 12 meses = 24 pontos de recuperação

+ 1 ponto por ano e por recuperação de 10 anos = 10 pontos

O número total de pontos de recuperação é 56.

## Configuração avançada

Clicando em **modificar** na tela anterior, os clientes têm mais flexibilidade ao especificar agendamentos de retenção. <br/>

![Modificar][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png

<!---HONumber=GIT-SubDir-->