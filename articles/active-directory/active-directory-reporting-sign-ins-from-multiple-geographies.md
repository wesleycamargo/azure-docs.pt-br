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
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Entradas de várias regiões geográficas
<p>Este relatório inclui entradas bem-sucedidas por um usuário, quando duas entradas parecem ter sido originadas de diferentes regiões e a diferença de tempo entre as duas entradas torna impossível para o usuário ter viajado entre tais regiões. Causas possíveis incluem:</p><ul><li>O usuário está compartilhando sua senha com outros usuários</li><li>O usuário está usando uma área de trabalho remota para iniciar um navegador da Web para entrar</li><li>Um hacker entrou na conta de um usuário em um país diferente</li><li>O usuário está usando uma VPN ou um proxy</li><li>O usuário está conectado através de vários dispositivos ao mesmo tempo, como um desktop e um telefone celular, e o endereço IP do celular é incomum.</li></ul><p>Os resultados desse relatório mostrarão os eventos de entrada com êxito, juntamente com o tempo entre as entradas, as regiões das quais as entradas parecem se originar e o tempo de viagem estimado entre as regiões.</p><p>O tempo de viagem mostrado é apenas uma estimativa e pode ser diferente do tempo de viagem real entre os locais.</p>


![Entradas de várias regiões geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=Oct15_HO3-->