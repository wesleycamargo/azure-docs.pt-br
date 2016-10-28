<properties
	pageTitle="Atividades de entrada irregulares"
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
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Atividades de entrada irregulares

Entradas irregulares são aquelas que foram identificadas por nossos algoritmos de aprendizado de máquina, com base em uma condição de “viagem impossível” combinada a um dispositivo e um local de entrada anômalos. Isso pode indicar que um hacker entrou com êxito usando essa conta. Enviaremos uma notificação por email aos administradores globais se encontrarmos 10 ou mais eventos de entrada anômalos dentro de um período de 30 dias ou menos. Inclua aad-alerts-noreply@mail.windowsazure.com em sua lista de remetentes seguros.

<!---HONumber=AcomDC_0309_2016-->