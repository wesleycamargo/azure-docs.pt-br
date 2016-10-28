<properties
	pageTitle="Entradas de várias regiões geográficas"
	description="Um relatório que indica aos usuários quando duas entradas parecem ter sido originadas de diferentes regiões e a diferença de tempo entre as duas entradas torna impossível para o usuário ter viajado entre tais regiões."
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

# Entradas de várias geografias

Este relatório inclui entradas bem-sucedidas de um usuário, em que duas entradas parecem ter sido originadas de diferentes regiões e a diferença de tempo entre as duas entradas torna impossível para o usuário ter viajado entre tais regiões. Entre as possíveis causas estão:

- O usuário está compartilhando sua senha com outros usuários

- O usuário está usando uma área de trabalho remota para iniciar um navegador da Web para a entrada

- Um hacker entrou na conta de um usuário em um país diferente

- O usuário está usando uma VPN ou um proxy

- O usuário está conectado em vários dispositivos ao mesmo tempo, como uma área de trabalho e um telefone celular, e o endereço IP do telefone celular é incomum.

Os resultados desse relatório mostrarão os eventos de entrada bem-sucedida, junto com a diferença de tempo entre as entradas, as regiões em que as entradas parecem ter se originado e o tempo de viagem estimado entre tais regiões. O tempo de viagem mostrado é apenas uma estimativa e pode ser diferente do tempo de viagem real entre os locais.


![Entradas de várias regiões geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=AcomDC_0309_2016-->