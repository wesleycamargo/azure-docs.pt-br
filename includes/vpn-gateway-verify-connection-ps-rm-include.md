---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da682e3e947c3f8643610e42ad22fb9d742796f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
Você pode verificar se a conexão foi bem-sucedida usando o cmdlet ‘Get-AzureRmVirtualNetworkGatewayConnection’, com ou sem ‘-Debug’. 

1. Use o seguinte exemplo de cmdlet, configurando os valores para coincidirem com os seus. Quando solicitado, selecione ‘A’ para executar ‘Todos’. No exemplo, "-Name" refere-se ao nome da conexão que você criou e deseja testar.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Após o cmdlet ter sido concluído, exiba os valores. No exemplo abaixo, o status da conexão é exibido como ‘Conectado’ e é possível ver os bytes de entrada e saída.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```