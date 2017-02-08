---
title: "Informações de dimensionamento de uma VNET no Azure RemoteApp | Microsoft Docs"
description: "Saiba mais sobre os requisitos de endereço IP para o RemoteApp do Azure em execução com uma VNET"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b6e1c4ba-0236-42b2-bced-69353bf211be
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 0639e19423adc842a708982997852fb8d10df779


---
# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Dimensionamento de informações de um VNET no RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Quando você usa o RemoteApp do Azure com uma rede virtual (VNET), o RemoteApp usa endereços IP dentro da sub-rede. Com base na escala de seu serviço do RemoteApp, você precisa garantir que sua sub-rede tenha endereços IP suficientes disponíveis para máquinas virtuais do RemoteApp. Embora essa orientação de dimensionamento não seja perfeita, visto o modo como o RemoteApp faz a rotação dinâmica de máquinas virtuais para cima e para baixo dentro de uma coleção, ele ajudará a calcular o intervalo de sub-rede. Isso é especialmente importante já que, quando um serviço do RemoteApp é colocado em uma VNET, você não pode aumentar o tamanho da sub-rede sem remover o RemoteApp.

Para cada coleção de RemoteApp que você deseja executar em capacidade máxima você deve ter 100 endereços IP disponíveis. Por exemplo, se você tiver uma coleção do RemoteApp no plano Padrão desejar ter no máximo 500 usuários, você deve ter 100 endereços IP para essa coleção. Do mesmo modo, você precisa de 100 endereços IP para uma coleção de RemoteApp no plano Básico com 800 usuários. Se você planeja ter menos usuários (menos que o máximo), você pode reduzir os endereços IP necessários por coleção. O requisito de tamanho mínimo de sub-rede é 30 endereços IP (/27).

Confira as informações a seguir para confirmar se sua VNET está configurada e funcionando corretamente:

* [Migrar de uma VNET pessoal para uma VNET do Azure](remoteapp-migratevnet.md)
* [Validar o VNET do Azure para usar com o RemoteApp do Azure](remoteapp-vnet.md)




<!--HONumber=Dec16_HO2-->


