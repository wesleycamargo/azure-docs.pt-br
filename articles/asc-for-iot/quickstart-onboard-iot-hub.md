---
title: Habilitar o serviço Central de Segurança do Azure para IoT na Versão Prévia do Hub IoT | Microsoft Docs
description: Saiba como habilitar o serviço Central de Segurança do Azure para IoT em seu Hub IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 59021d09f2af9d430b118acdeb8aa977094e683e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862379"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Início Rápido: Habilitar o serviço no Hub IoT

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como habilitar o serviço de versão prévia da ASC (Central de Segurança do Azure) para IoT em seu Hub IoT.  

> [!NOTE]
> No momento, a Central de Segurança do Azure para IoT só é compatível com a camada standard e superior de Hubs IoT.
> A Central de Segurança do Azure para IoT é uma solução de hub único. Se você precisar de vários hubs, várias soluções serão necessárias. 

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para habilitar o serviço

- Workspace do Log Analytics
  - Dois tipos de informação são armazenados por padrão no espaço de trabalho do Log Analytics pela ASC para IoT; **alertas de segurança** e **recomendações**. 
  - Você pode optar por adicionar armazenamento de um tipo de informações adicionais **eventos brutos**. Observe que armazenar **eventos brutos** no Log Analytics transporta os custos de armazenamento adicionais. 
- Hub IoT (camada standard ou superior)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Habilitar a ASC para IoT no Hub IoT 

Para habilitar perguntas de segurança no seu Hub IoT, faça o seguinte: 

1. Abra o **Hub IoT** no portal do Azure. 
2. No menu à esquerda, selecione e abra **Segurança**. 
3. Escolha **Habilitar a segurança de IoT**. 
4. Fornecer os detalhes do seu espaço de trabalho do Log Analytics. 
   - Opte por armazenar **eventos brutos** além dos tipos de informações padrão de armazenamento, deixando o **evento bruto** alternado em **Ativar**. 
   - Opte por habilitar **coleção de gêmeos**, deixando a **coleção de gêmeos** alternada em **Ativar**. 
5. Clique em **OK**. 
6. Clique em **Save** (Salvar). 

Parabéns! Você concluiu a habilitação da ASC para IoT em seu Hub IoT. 

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender como configurar sua solução...

> [!div class="nextstepaction"]
> [Configurar sua solução](quickstart-configure-your-solution.md)