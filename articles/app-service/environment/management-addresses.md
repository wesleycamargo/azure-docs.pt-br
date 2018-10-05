---
title: Endereços de gerenciamento de Ambiente do Serviço de Aplicativo do Azure
description: Lista os endereços de gerenciamento usados para comandar um Ambiente de Serviço de Aplicativo
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: 590679daff20f9c469fb8fcfcc0fbbad77f91b5b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162841"
---
# <a name="app-service-environment-management-addresses"></a>Endereços de gerenciamento de Ambiente de Serviço de Aplicativo

O ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da VNet (Rede Virtual) do Azure.  É necessário que o ASE possa ser acessado usando o plano de gerenciamento usado pelo Serviço de Aplicativo do Azure.  Esse tráfego de gerenciamento de ASE atravessa a rede controlada pelo usuário. Se o tráfego estiver bloqueado ou estiver encaminhado erroneamente, o ASE será suspenso. Para obter detalhes sobre as dependências da rede do ASE, leia [Considerações sobre a rede e o ambiente do Serviço de Aplicativo][networking]. Para obter informações gerais sobre o ASE, você pode iniciar com [Introdução ao Ambiente do Serviço de Aplicativo][intro].

Este documento lista os endereços de origem do Serviço de Aplicativo para o tráfego de gerenciamento para o ASE e serve a duas finalidades importantes.  

1. É possível usá-los para criar Grupos de Segurança de Rede para bloquear o tráfego de entrada.  
2. Você pode criar rotas com esses endereços para dar suporte a implantações de túnel forçado. Para obter detalhes sobre como configurar o ASE para operar em um ambiente em que o tráfego de saída é enviado localmente, leia [Configurar ASE com túnel forçado][forcedtunnel]

Todos os ASEs têm um VIP público no qual o tráfego de gerenciamento entra. O tráfego de gerenciamento de entrada desses endereços é proveniente das portas 454 e 455 no VIP público de seu ASE.  

## <a name="list-of-management-addresses"></a>Lista de endereços de gerenciamento ##

| Região | Endereços |
|--------|-----------|
| Todas as regiões públicas | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure governamental (Fairfax ou MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Obter seus endereços de gerenciamento de API ##

Você pode listar os endereços de gerenciamento que correspondem ao seu ASE com a seguinte chamada à API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

A API retorna um documento JSON que inclui todos os endereços de entrada do ASE. A lista de endereços inclui os endereços de gerenciamento, o VIP usado pelo seu ASE e o intervalo de endereços de sub-rede do ASE em si.  

Para chamar a API com o [armclient](http://github.com/projectkudu/ARMClient) use os seguintes comandos, mas substitua a ID da assinatura, o grupo de recursos e o nome do ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
