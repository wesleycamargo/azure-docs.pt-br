<properties
	pageTitle="Determinar opções de redundância do armazenamento de Backup do Azure | Microsoft Azure"
	description="Compreenda a diferença entre o armazenamento com redundância geográfica e o armazenamento com redundância local para determinar sua opção de redundância do armazenamento de Backup do Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Opções de redundância de armazenamento para o Backup do Azure

Sua empresa precisa determinar a opção de redundância de armazenamento apropriada para o armazenamento de Backup do Azure. Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup principal (por exemplo, estiver fazendo backup de um Windows Server no Azure), deverá considerar a possibilidade de escolher a opção de armazenamento com redundância geográfica (padrão). Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário (por exemplo, estiver usando SCDPM para ter uma cópia de backup local e usando o Azure para suas necessidades de retenção de longo prazo), deverá considerar a possibilidade de escolher armazenamento com redundância local

## Armazenamento com redundância geográfica (GRS)

O armazenamento com redundância geográfica mantém seis cópias dos seus dados. Com o GRS, seus dados são replicados três vezes na região primária e também são replicados três vezes para uma região secundária a centenas de quilômetros de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região primária, ao armazenar os dados no GRS, o Backup do Azure garante que seus dados sejam duráveis em duas regiões separadas.

## Armazenamento com redundância local (LRS)

O armazenamento com redundância local mantém três cópias dos seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege seus dados contra falhas normais de hardware, mas não contra a falha de uma instalação inteira do Azure. Isso reduz o custo de armazenamento de dados no Azure, além de oferecer um nível mais baixo de durabilidade para seus dados que pode ser aceitável para cópias terciárias.

Você pode selecionar a opção apropriada para suas necessidades da opção **Configurar** de seu cofre de backup.

## Próximas etapas

- Verifique se seu ambiente está [preparado para fazer backup de um computador cliente ou servidor do Windows](backup-configure-vault.md)
- Se você ainda tem perguntas sem resposta, confira as [Perguntas frequentes do Backup do Azure](backup-azure-backup-faq.md).
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0211_2016-->