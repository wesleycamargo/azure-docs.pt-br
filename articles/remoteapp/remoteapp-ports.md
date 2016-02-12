
<properties
    pageTitle="Lista de URLs e Portas a colocar em lista branca para o Azure RemoteApp implantado na Rede Virtual do cliente | Microsoft Azure"
    description="Saiba quais portas e URLs você precisará configurar para comunicação por meio do RemoteApp do Azure."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/29/2016"
    ms.author="elizapo" />



# Lista de URLs e Portas para permitir o acesso para o Azure RemoteApp implantado na Rede Virtual do cliente 

O listado a seguir aplica uma coleção de nuvem ou híbrida ao Azure RemoteApp se você estiver implantando-a em uma rede virtual (VNET). Para obter mais informações sobre redes virtuais, confira [Visão Geral da Rede Virtual](virtual-networks-overview.md). Se você tiver criado um NSG (grupo de segurança de rede) restringindo o tráfego para os recursos da rede virtual que você escolheu para o Azure RemoteApp, verifique se os itens a seguir estão acessíveis e são permitidos por meio de políticas de segurança na rede virtual. Para obter mais informações sobre grupos de segurança de rede, leia [o que é um Grupo de Segurança de Rede? (NSG)](virtual-networks-nsg.md).

##  A sub-rede do RemoteApp do Azure precisa de acesso a esses pontos de extremidade e URLs: 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 Saída: TCP: 443, TCP: 10101-10175 
*	 Opcional – UDP: 10201-10275  
 
## Os clientes do RemoteApp do Azure precisam de acesso a esses pontos de extremidade e URLs: 

Por clientes, quero dizer desktops, dispositivos etc. que as pessoas usam para se conectar aos aplicativos implantados na coleção de RemoteApp do Azure.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://*.remoteapp.windowsazure.com (as portas UDP opcionais são para este endereço) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://*.core.windows.net  
-  Saída: TCP: 443  
-  Opcional - UDP: 3391 

<!---HONumber=AcomDC_0204_2016-->