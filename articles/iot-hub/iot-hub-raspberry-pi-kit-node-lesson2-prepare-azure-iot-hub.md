---
featureFlags:
- usabilla
title: "Conectar Raspberry Pi (Nó) ao IoT do Azure - Lição 2: registrar dispositivo | Microsoft Docs"
description: Crie um grupo de recursos, um Hub IoT do Azure e registre o Pi no Registro de identidade do Hub IoT usando a CLI do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: nuvem raspberry pi, pi cloud connect
ms.assetid: 736215b6-e7e4-46f9-af30-0ded9ffa5204
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: add684549056a824e5534d071a5d0215e27f7a0e


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Criar seu Hub IoT e registrar o Raspberry Pi 3
## <a name="what-you-will-do"></a>O que você fará
* Crie um grupos de recursos.
* Criar seu hub IoT do Azure no grupo de recursos.
* Adicione o Raspberry Pi 3 para o Hub IoT do Azure usando a interface de linha de comando do Azure (CLI do Azure).

Quando você usa a CLI do Azure para adicionar o Pi ao hub IoT, o serviço gera uma chave para a autenticação do Pi com o serviço. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como usar a CLI do Azure para criar um Hub IoT
* Como criar uma identidade de dispositivo para seu Pi no Hub IoT

## <a name="what-you-need"></a>O que você precisa
* Uma conta do Azure
* Um computador Mac ou Windows com a CLI do Azure instalada

## <a name="create-your-iot-hub"></a>Criar seu Hub IoT
O Hub IoT do Azure ajuda a conectar, monitorar e gerenciar milhões de ativos IoT. Para criar seu Hub IoT, execute estas etapas:

1. Faça logon em sua conta do Azure executando o comando a seguir:

   ```bash
   az login
   ```

   Todas as suas assinaturas disponíveis são listadas após um logon bem-sucedido.

2. Defina a assinatura padrão que deseja usar executando o seguinte comando:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` podem ser encontrados na saída do `az login` ou do comando `az account list`.

3. Registre o provedor executando o comando a seguir. Provedores de recursos são serviços que fornecem recursos para seu aplicativo. Você deve registrar o provedor antes de implantar o recurso do Azure que o provedor oferece.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. Crie um grupo de recursos denominado iot-sample na região Oeste dos EUA executando o seguinte comando:

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` é o local no qual você cria seu grupo de recursos. Se você quiser usar outro local, execute `az account list-locations -o table` para ver todos os locais com suporte do Azure.
 
5. Crie um hub IoT no grupo de recursos iot-sample executando o seguinte comando:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

   Por padrão, a ferramenta cria um Hub IoT no tipo de preços gratuito. Para saber mais, confira [Preço do Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE] 
> O nome do hub IoT deve ser globalmente exclusivo. Você pode criar apenas uma edição F1 do Hub IoT do Azure em sua assinatura do Azure.

## <a name="register-pi-in-your-iot-hub"></a>Registrar o Pi em seu Hub IoT
Cada dispositivo que envia mensagens para/de seu Hub IoT deve ser registrado com uma ID exclusiva. Você usará o CLI do Azure para registrar seu Pi e criar um certificado autoassinado X.509 para autenticação do dispositivo.

Execute o comando a seguir:

```bash
# For Windows command prompt
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir %USERPROFILE%\.iot-hub-getting-started
 
# For macOS or Ubuntu
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir ~/.iot-hub-getting-started
```

## <a name="summary"></a>Resumo
Você criou um Hub IoT do Azure e registrou seu Pi com uma identidade de dispositivo em seu Hub IoT. Você está pronto para saber como enviar mensagens do Pi para o Hub IoT.

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure para processar e armazenar mensagens do hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)




<!--HONumber=Jan17_HO4-->


