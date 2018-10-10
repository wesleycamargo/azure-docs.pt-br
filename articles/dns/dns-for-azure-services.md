---
title: Usando o DNS do Azure com outros serviços do Azure | Microsoft Docs
description: Noções básicas sobre como usar o DNS do Azure a fim de resolver o nome para outros serviços do Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989134"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Como o Azure DNS funciona com outros serviços do Azure

O DNS do Azure é um serviço hospedado de gerenciamento de DNS e resolução de nomes. Isso permite que você crie nomes DNS públicos para outros aplicativos e serviços implantados no Azure. A criação de um nome para um serviço do Azure no seu domínio personalizado é tão simples quanto adicionar um registro do tipo correto ao seu serviço.

* Para endereços IP alocados dinamicamente, você pode criar um registro DNS CNAME que mapeia para o nome DNS que o Azure criou para seu serviço. Padrões DNS o impedem de usar um registro CNAME para o ápice da zona, mas você pode usar um registro de alias em vez disso. Para obter mais informações, veja [Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md).
* Para endereços IP alocados estaticamente, você pode criar um registro DNS A usando qualquer nome, incluindo um nome de *domínio raiz* no apex da zona.

A tabela a seguir descreve os tipos de registro com suporte que podem ser usados para vários serviços do Azure. Como você pode ver nessa tabela, o DNS do Azure dá suporte apenas a registros DNS para recursos de rede voltados para a Internet. O DNS do Azure não pode ser usado para a resolução de nome de endereços internos, privadas.

| Serviço do Azure | Interface de rede | DESCRIÇÃO |
| --- | --- | --- |
| Gateway de Aplicativo |[IP público de front-end](dns-custom-domain.md#public-ip-address) |Você pode criar um registro CNAME ou DNS A. |
| Load Balancer |[IP público de front-end](dns-custom-domain.md#public-ip-address)  |Você pode criar um registro CNAME ou DNS A. O Balanceador de Carga pode ter um endereço IP público do IPv6 que é atribuído dinamicamente. Portanto, você deve criar um registro CNAME para um endereço IPv6. |
| Gerenciador de Tráfego |Nome público |Você pode criar um registro de alias que mapeia para o nome trafficmanager.net atribuído ao seu perfil do Gerenciador de Tráfego. Para obter mais informações, veja o [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio do apex com o Gerenciador de Tráfego](tutorial-alias-tm.md). |
| Serviço de Nuvem |[IP público](dns-custom-domain.md#public-ip-address) |Para endereços IP alocados estaticamente, você pode criar um registro DNS A. Para endereços IP alocados dinamicamente, você deve criar um registro CNAME que mapeia para o nome *cloudapp.net* .|
| Serviço de Aplicativo | [IP externo](dns-custom-domain.md#app-service-web-apps) |Para endereços IP externos, você pode criar um registro DNS A. Caso contrário, você deve criar um registro CNAME que mapeia para o nome azurewebsites.net. Para saber mais, confira [Mapear um nome de domínio personalizado para um aplicativo do Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| VMs do Resource Manager |[IP público](dns-custom-domain.md#public-ip-address) |As VMs do Gerenciador de Recursos pode ter endereços IP públicos. Uma VM com um endereço IP público também pode estar por trás de um balanceador de carga. Você pode criar um DNS A, o CNAME ou o registro de alias para o endereço Público. Esse nome personalizado pode ser usado para ignorar o VIP no balanceador de carga. |
| VMs clássicas |[IP público](dns-custom-domain.md#public-ip-address) |VMs clássicas criadas usando o PowerShell ou a CLI podem ser configuradas com um endereço virtual (reservado) dinâmico ou estático. Você pode criar um registro DNS CNAME ou A, respectivamente. |
