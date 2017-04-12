---
title: "Dispositivo simulado e Gateway do IoT do Azure - Introdução | Microsoft Docs"
description: "Introdução ao Kit de Início do Gateway IoT, criar seu Hub IoT do Azure e conectar o Gateway ao Hub IoT"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "hub iot do azure, gateway iot, introdução à Internet das coisas, kit de ferramentas do iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 06161c2950fbe99d2e4f9c1b416695a5f9f18ca4
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>Introdução ao Kit de Início do Gateway IoT com um dispositivo simulado

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo Simulado](iot-hub-gateway-kit-c-sim-get-started.md)

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com o [Kit de Início do Gateway IoT](https://aka.ms/gateway-kit). Você estará trabalhando com a NUC da Intel executando o Linux Wind River. Você aprenderá a conectar seus dispositivos diretamente à nuvem usando o Hub IoT do Azure.

***
**Ainda não tem um kit?:** Clique [aqui](https://aka.ms/gateway-kit).
***

## <a name="lesson-1-configure-your-nuc"></a>Lição 1: Configurar sua NUC
![Diagrama de ponta a ponta da Lição&1;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

Nesta lição, você configura a NUC da Intel (próxima unidade de computação) no Kit como um Gateway IoT do Azure, instala o pacote do SDK do Gateway IoT do Azure na NUC e executa um aplicativo de exemplo para verificar a funcionalidade de gateway.

*Tempo estimado para conclusão: 15 minutos*

Vá para [Configurar a NUC da Intel como um gateway IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>Lição 2: Criar seu Hub IoT
![Diagrama de ponta a ponta da Lição&2;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

Nesta lição, você instala os softwares e as ferramentas no computador host. Em seguida, você cria sua conta gratuita do Azure, provisiona seu Hub IoT do Azure e cria seu primeiro dispositivo no Hub IoT.

Conclua a Lição 1 antes de iniciar esta lição.

### <a name="get-the-tools"></a>Obter as ferramentas
Instale os softwares e as ferramentas no computador host.

*Tempo estimado para conclusão: 20 minutos*

Acesse [Obter as ferramentas](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

### <a name="create-an-iot-hub-and-register-your-device"></a>Criar um Hub IoT e registrar seu dispositivo
Crie seu grupo de recursos, provisione seu primeiro Hub IoT do Azure e adicione seu primeiro dispositivo ao Hub IoT usando a CLI do Azure.

*Tempo estimado para conclusão: 10 minutos*

Acessar [Criar um Hub IoT e registrar seu dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>Lição 3: Receber mensagens do dispositivo simulado e ler mensagens de seu Hub IoT
Nesta lição, você usará scripts para automatizar a configuração e a execução de um aplicativo de dispositivo simulado em seu gateway. O aplicativo do dispositivo simulado gera dados de temperatura de exemplo e envia-os para um módulo de Hub IoT. O módulo de Hub IoT compacta os dados recebidos e o envia para o Hub IoT por meio da estrutura de gateway fornecida no SDK do Gateway IoT do Azure.

![Diagrama de ponta a ponta da Lição 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>Configurar e executar um dispositivo simulado
Prepare os códigos de exemplo. Em seguida, configure e execute o aplicativo de exemplo do dispositivo simulado.

*Tempo estimado para conclusão: 15 minutos*

Acessar [Configurar e executar um dispositivo simulado](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

### <a name="read-messages-from-your-iot-hub"></a>Ler mensagens de seu Hub IoT
Execute um código de exemplo no computador host para ler as mensagens de seu Hub IoT.

*Tempo estimado para conclusão: 15 minutos*

Acessar [Ler mensagens do Hub IoT](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Lição 4: salvar mensagens no Armazenamento de Tabelas do Azure
Crie um aplicativo de funções do Azure que obtenha as mensagens recebidas de seu Hub IoT e as grave-as no Armazenamento de Tabelas do Azure.

![Diagrama de ponta a ponta da Lição 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.

*Tempo estimado para conclusão: 10 minutos*

Acesse [Criar um aplicativo de funções do Azure e uma Conta de armazenamento do Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>Ler mensagens mantidas no Armazenamento de Tabelas do Azure
Monitore as mensagens do gateway para a nuvem conforme elas são gravadas no Armazenamento de Tabelas do Azure.

*Tempo estimado para conclusão: 5 minutos*

Acesse [Ler mensagens mantidas no Armazenamento de Tabelas do Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Solucionar problemas
Se tiver problemas durante as lições, procure por soluções no artigo [Solução de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="explore-more"></a>Explorar mais
Visite a [Zona do desenvolvedor de Kit do Gateway IoT da Intel](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit) para saber mais.
