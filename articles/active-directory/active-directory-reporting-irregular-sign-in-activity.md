<properties
	pageTitle="Atividade de conexão anômala"
	description="Um relatório que inclui entradas identificadas como anômalas pelos nossos algoritmos de aprendizado de máquina."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Atividade de conexão anômala

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Este relatório inclui entradas identificadas como "anômalas" pelos nossos algoritmos de aprendizado de máquina. Os motivos para marcar uma tentativa de entrada como irregular incluem locais de entrada inesperados, horários e locais inesperados ou uma combinação desses elementos. Isso pode indicar que um hacker tentou entrar usando essa conta. O algoritmo de aprendizado de máquina classifica os eventos como "anômalos" ou "suspeitos", em que "suspeito" indica alta probabilidade de uma violação de segurança.</p><p>Os resultados desse relatório mostrarão essas entradas, junto com a classificação, local e um carimbo de data/hora associado a cada entrada.</p><p>Nós enviaremos um email de notificação para os administradores globais se encontrarmos 10 ou mais eventos anômalos de entrada em um período de 30 dias ou menos. Certifique-se de incluir aad-alerts-noreply@mail.windowsazure.com na sua lista de remetentes seguros.</p> | Diretório > guia Relatórios |

<!---HONumber=August15_HO8-->