---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 2: registrar dispositivo | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: Hub iot do Azure, nuvem de Internet das coisas, criar dispositivo de Hub IoT do Azure, SensorTag de TI, bli de TI
ms.assetid: 2c18f5ae-e39a-48ae-a9fe-04bb595740a0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a895f18b4cefc01f12613e701c71bd471bb369c3


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Criar seu Hub IoT do Azure e registrar seu dispositivo

## <a name="what-you-will-do"></a>O que você fará

- Criar um grupos de recursos
- Criar seu primeiro Hub IoT
- Registre seu dispositivo no seu Hub IoT usando a CLI do Azure. 

Quando você registra seu dispositivo no Hub IoT, o serviço do Hub IoT do Azure gera uma chave a ser usada pelo seu dispositivo para autenticação com o serviço. 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá a:

- Como usar a CLI do Azure para criar um Hub IoT.
- Como registrar um dispositivo em um Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, você pode [criar uma conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
- Você deve ter a CLI do Azure instalada.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Para criar um Hub IoT, execute estas etapas:

1. Faça logon em sua conta do Azure executando o comando a seguir:

   ```bash
   az login
   ```

   Todas as suas assinaturas disponíveis serão listadas após uma entrada bem-sucedida.

2. Defina a assinatura padrão do Azure que deseja usar executando o seguinte comando:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` podem ser encontrados na saída do `az login` ou do comando `az account list`.

3. Registre o provedor executando o comando a seguir. Provedores de recursos são serviços que fornecem recursos para seu aplicativo. Você deve registrar o provedor antes de implantar o recurso do Azure que o provedor oferece.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. Crie um grupo de recursos denominado `iot-gateway` na região Oeste dos EUA executando o seguinte comando:

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` é o local no qual você cria seu grupo de recursos. Se você quiser usar outro local, execute `az account list-locations -o table` para ver todos os locais com suporte do Azure.

5. Crie um Hub IoT no grupo de recursos `iot-gateway` executando o seguinte comando:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

Por padrão, a ferramenta cria um Hub IoT no tipo de preços gratuito. Para saber mais, confira [Preço do Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> O nome do hub IoT deve ser globalmente exclusivo. Você pode criar apenas uma edição F1 do Hub IoT do Azure em sua assinatura do Azure.

## <a name="register-your-device-in-your-iot-hub"></a>Registrar seu dispositivo em seu Hub IoT

Cada dispositivo que envia mensagens para/de seu Hub IoT deve ser registrado com uma ID exclusiva.
Registre seu dispositivo no Hub IoT do Azure executando o seguinte comando:

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>Resumo

Você criou um Hub IoT do Azure e registrou seu dispositivo lógico com uma identidade de dispositivo em seu Hub IoT. Você está pronto para aprender a configurar e executar um aplicativo de exemplo do gateway para enviar dados de seu dispositivo físico para o Hub IoT na nuvem.

## <a name="next-steps"></a>Próximas etapas
[Configurar e executar um aplicativo de exemplo BLE](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)


<!--HONumber=Jan17_HO4-->


