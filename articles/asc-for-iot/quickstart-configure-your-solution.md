---
title: Configurar a Versão Prévia da solução Central de Segurança do Azure para IoT | Microsoft Docs
description: Saiba como configurar sua solução de IoT de ponta a ponta usando a Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861914"
---
# <a name="quickstart-configure-your-iot-solution"></a>Início Rápido: Configure a solução de IoT

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como realizar a configuração inicial de sua solução de segurança de IoT usando a ASC para IoT. 

## <a name="azure-security-center-asc-for-iot"></a>ASC (Central de Segurança do Azure) para IoT

A ASC para IoT fornece segurança abrangente de ponta a ponta para soluções de IoT com base no Azure.

Com o ASC para IoT, é possível monitorar toda a solução de IoT em um painel, identificando todos os dispositivos IoT, as plataformas IoT e os recursos de back-end no Azure.

Uma vez habilitado em seu Hub IoT, a ASC para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu hub IoT e relacionados à solução de IoT.

Além da detecção automática de relacionamento, também é possível selecionar e escolher outros recursos do Azure para marcar como parte da solução de IoT.
Suas seleções permitem adicionar assinaturas, grupos de recursos ou recursos únicos inteiros.

Depois de definir todas as relações de recursos, a ASC para IoT aproveita a Central de Segurança do Azure para fornecer recomendações de segurança e alertas para esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à solução de IoT

Para adicionar novos recursos à solução de IoT, faça o seguinte: 

1. Abra o **Hub IoT** no portal do Azure. 
2. No menu à esquerda, selecione e abra **Recursos** em **Segurança**. 
3. Selecione **Adicionar Recursos**.
4. Escolha os recursos que pertencem à solução de IoT.
5. Clique em **Adicionar**. 

Parabéns! Você adicionou um novo recurso à solução de IoT.

Como parte da solução de IoT, a ASC para IoT agora monitora os recursos recentemente adicionados e identifica recomendações e alertas de segurança relevantes.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender como criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)