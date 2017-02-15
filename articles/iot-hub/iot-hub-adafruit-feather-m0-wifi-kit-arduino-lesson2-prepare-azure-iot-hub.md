---
title: Criar um Hub IoT do Azure e registrar o Adafruit Feather M0 WiFi | Microsoft Docs
description: Crie um grupo de recursos, crie um Hub IoT do Azure e registre o Adafruit Feather M0 WiFi no Hub IoT do Azure usando a CLI do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "conectar o arduino à nuvem, hub iot do azure, nuvem de Internet das coisas, criar dispositivo de hub iot do azure, nuvem arduino"
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 232c391e61e7e7ec053d2e7170f9abe4c79d9528


---
# <a name="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board"></a>Crie seu Hub IoT e registre sua placa do Adafruit Feather M0 WiFi Arduino

## <a name="what-you-will-do"></a>O que você fará
* Crie um grupos de recursos.
* Criar seu hub IoT do Azure no grupo de recursos.
* Adicione sua placa Arduino ao Hub IoT do Azure usando a interface de linha de comando do Azure (CLI do Azure).

Quando você usa a CLI do Azure para adicionar sua placa Arduino ao Hub IoT, o serviço gera uma chave para a autenticação da placa Arduino com o serviço. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshoot].

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como usar a CLI do Azure para criar um Hub IoT.
* Como criar uma identidade de dispositivo para sua placa Arduino no Hub IoT.

## <a name="what-you-need"></a>O que você precisa
* Uma conta do Azure
* Um computador com a CLI do Azure instalada

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
> O nome do hub IoT deve ser globalmente exclusivo.
> Você pode criar apenas uma edição F1 do Hub IoT do Azure em sua assinatura do Azure.

## <a name="register-your-arduino-board-in-your-iot-hub"></a>Registrar sua placa Arduino no Hub IoT
Cada dispositivo que envia mensagens para/de seu Hub IoT deve ser registrado com uma ID exclusiva.

Registre sua placa Arduino no Hub IoT executando o seguinte comando:

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

## <a name="summary"></a>Resumo
Você criou um Hub IoT e registrou sua placa Arduino com uma identidade de dispositivo em seu Hub IoT. Você está pronto para saber como enviar mensagens da placa Arduino para o Hub IoT.

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure para processar e armazenar mensagens do Hub IoT][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md


<!--HONumber=Dec16_HO2-->


