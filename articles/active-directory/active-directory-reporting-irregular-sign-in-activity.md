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

Entradas irregulares são aquelas que foram identificadas por nossos algoritmos de aprendizado de máquina, com base em uma condição de "viagem impossível" combinada a um dispositivo e um local de entrada anômalas. Isso pode indicar que um hacker entrou com êxito usando essa conta. Enviaremos uma notificação por email aos administradores globais se encontrarmos 10 ou mais eventos de entrada anômalos dentro de um período de 30 dias ou menos. Inclua aad-alerts-noreply@mail.windowsazure.com em sua lista de remetentes seguros.

<!---HONumber=August15_HO9-->