---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 508685954e23a357fa5fc48b0e89c5e7daedb5c6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
Você pode verificar se a conexão foi bem-sucedida usando o cmdlet ‘Get-AzureRmVirtualNetworkGatewayConnection’, com ou sem ‘-Debug’. 

1. Use o seguinte exemplo de cmdlet, configurando os valores para coincidirem com os seus. Quando solicitado, selecione ‘A’ para executar ‘Todos’. No exemplo, "-Name" refere-se ao nome da conexão que você criou e deseja testar.

  ```azurepowershell-interactive
  Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. Após o cmdlet ter sido concluído, exiba os valores. No exemplo abaixo, o status da conexão é exibido como ‘Conectado’ e é possível ver os bytes de entrada e saída.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```