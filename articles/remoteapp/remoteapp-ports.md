---
title: "Lista de Portas e URLs para colocar na lista de permissões do Azure RemoteApp implantado na rede virtual do cliente | Microsoft Docs"
description: "Saiba quais portas e URLs você precisará configurar para comunicação por meio do RemoteApp do Azure."
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 79f6398b861597e3303231bc17f69be1bde947ae
ms.openlocfilehash: ef052d074b593e4bbecfdbfb3af76d1ba1249e33


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Lista de URLs e Portas para permitir o acesso para o Azure RemoteApp implantado na Rede Virtual do cliente
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Se você estiver implantando uma nuvem ou coleção híbrida do Azure RemoteApp em uma rede virtual (VNET), examine as informações sobre as portas apresentadas a seguir. Para obter mais informações sobre redes virtuais, consulte [Visão Geral da Rede Virtual](../virtual-network/virtual-networks-overview.md). Se você tiver criado um NSG (grupo de segurança de rede) restringindo o tráfego para os recursos da rede virtual na coleção, verifique se as portas a seguir estão acessíveis e têm permissão por meio das políticas de segurança na rede virtual. Para obter mais informações sobre grupos de segurança de rede, consulte [O que é um Grupo de Segurança de Rede? (NSG)](../virtual-network/virtual-networks-nsg.md).

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>A sub-rede do RemoteApp do Azure precisa de acesso a esses pontos de extremidade e URLs:
* *.servicebus.windows.net
* *.servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* Saída: TCP: 443, TCP: 10101-10175 
* Opcional – UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Os clientes do RemoteApp do Azure precisam de acesso a esses pontos de extremidade e URLs:
Por clientes, quero dizer desktops, dispositivos etc. que as pessoas usam para se conectar aos aplicativos implantados na coleção de RemoteApp do Azure.

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (as portas UDP opcionais são para esse endereço) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* Saída: TCP: 443  
* Opcional - UDP: 3391 




<!--HONumber=Dec16_HO3-->


