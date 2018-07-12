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
ms.openlocfilehash: 591da67e6411d0e859076f0a3c3c38afc1ebe1f5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765906"
---
Você pode verificar se a conexão foi bem-sucedida usando o cmdlet "Get-AzureVNetConnection".

1. Use o seguinte exemplo de cmdlet, configurando os valores para coincidirem com os seus. O nome da rede virtual deve ficar entre aspas se contiver espaços.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Após o cmdlet ter sido concluído, exiba os valores. No exemplo abaixo, o Estado de Conectividade é exibido como ‘Conectado’ e é possível ver os bytes de entrada e saída.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal