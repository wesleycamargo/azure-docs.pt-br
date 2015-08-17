<properties
   pageTitle="Usar o Backup do Azure para substituir a infraestrutura de fita | Microsoft Azure"
   description="Saiba como o Backup do Azure fornece semântica semelhante à fita que permite fazer backup e restaurar dados no Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tagsms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Usar o Backup do Azure para substituir a infraestrutura de fita
Os clientes do Backup do Azure e do System Center Data Protection Manager podem: - fazer backup de dados em agendamentos que melhor se adequam às necessidades da sua organização - reter os dados de backup por períodos mais longos - tornar o Azure uma parte de suas necessidades de retenção de longo prazo (em vez da fita).

Este artigo explica como os clientes podem habilitar políticas de backup e retenção. Os clientes que usam fitas para tratar suas necessidades de retenção de longo prazo agora têm uma alternativa viável e eficiente com a disponibilidade desse recurso. O recurso está habilitado na versão mais recente do Backup do Azure (que está disponível [aqui](http://aka.ms/azurebackup_agent)). Os clientes do SCDPM precisarão migrar para UR5 antes de usar esse recurso.

## Qual é o agendamento de backup?
O agendamento de backup indica a frequência da operação de backup. Por exemplo, as configurações na tela abaixo indicam que os backups serão feitos diariamente às 18h e à meia-noite.

![Agendamento diário](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar um backup semanal. Por exemplo, as configurações na tela abaixo indicam que os backups serão feitos a cada dois domingos e a cada duas quartas-feiras às 9h30 e à 1h.

![Agendamento semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## O que é a política de retenção?
A política de retenção especifica durante quanto tempo o backup deve ser armazenado. Em vez de especificar apenas uma “política simples” para todos os pontos de backup, os clientes podem especificar diferentes políticas de retenção com base em quando o backup é feito. Por exemplo, o ponto de backup criado no final de cada trimestre talvez precise ser preservado por mais tempo para fins de auditoria, enquanto o ponto de backup feito diariamente, que funciona como um ponto de recuperação operacional, deve ser preservado por 90 dias.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de “pontos de retenção” especificado nessa política é de 90 (pontos diários) + 40 (um a cada trimestre por 10 anos) = 130.

## Exemplo – Juntando ambos

![Tela de exemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política de retenção diária**: os backups diários são armazenados por 7 dias.
2. **Política de retenção semanal**: os backups feitos aos sábados à meia-noite e às 18h serão preservados por quatro semanas
3. **Política de retenção mensal**: os backups feitos à meia-noite e às 18h no último sábado de cada mês serão preservados por 12 meses
4. **Política anual de retenção**: os backups feitos à meia-noite no último sábado de cada mês de março serão preservados por 10 anos

O número total de “pontos de retenção” (dos quais um cliente pode restaurar dados) no diagrama acima é calculado da seguinte forma:

- 2 pontos por dia por 7 dias = 14 pontos de recuperação
- 2 pontos por semana por 4 semanas = 8 pontos de recuperação
- 2 pontos por mês por 12 meses = 24 pontos de recuperação
- 1 ponto por ano por 10 anos = 10 pontos de recuperação

O número total de pontos de recuperação é 56.

## Configuração avançada
Clicando em **Modificar** na tela acima, os clientes têm mais flexibilidade para especificar agendamentos de retenção.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## Próximas etapas
Para obter mais informações sobre consulte Backup do Azure, consulte

- [Introdução ao Backup do Azure](backup-introduction-to-azure-backup.md)
- [Teste o Backup do Azure](backup-try-azure-backup-in-10-mins)

<!---HONumber=August15_HO6-->