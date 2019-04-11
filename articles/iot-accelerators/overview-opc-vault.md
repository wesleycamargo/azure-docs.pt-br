---
title: O que é gerenciamento de certificados da UA de OPC do Azure IoT | Microsoft Docs
description: Visão geral do OPC Vault
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759691"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>O que é gerenciamento de certificados da UA de OPC (Open Platform Communications) do Azure IoT?

O gerenciamento de certificados da UA de OPC do Azure IoT, também conhecido como OPC Vault, é um microsserviço que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos servidor e cliente da UA de OPC na nuvem. Este artigo descreve os casos de uso simples do OPC Vault.

## <a name="certificate-management"></a>Gerenciamento de certificados

Por exemplo, uma empresa de fabricação precisa conectar seu computador de servidor da UA de OPC a seu aplicativo cliente recém-criado. Quando o fabricante faz o acesso inicial ao computador do servidor, uma mensagem de erro é mostrada imediatamente no aplicativo para servidores da UA de OPC para indicar que o aplicativo cliente não é seguro. Esse mecanismo é criado no computador do servidor da UA de OPC para impedir qualquer acesso não autorizado ao aplicativo, o que impede que hackers mal-intencionados invadam o chão de fábrica.

## <a name="application-security-management"></a>Gerenciamento de segurança de aplicativo
Um profissional de segurança usa o microsserviço OPC Vault para habilitar facilmente o servidor da UA de OPC para se comunicar com qualquer aplicativo cliente, pois o OPC Vault tem todas as funções para registro, armazenamento e gerenciamento de ciclo de vida de certificado. Agora que o servidor da UA de OPC está conectado com segurança, ele pode se comunicar com o aplicativo cliente que acaba de ser criado

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura completa do OPC Vault
O diagrama a seguir ilustra a arquitetura completa do OPC Vault.

![Arquitetura do OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)