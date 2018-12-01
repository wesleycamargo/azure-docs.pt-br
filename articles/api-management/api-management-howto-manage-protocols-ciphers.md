---
title: Gerenciar protocolos e cifras no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como gerenciar protocolos (TLS e SSL) e cifras (DES) no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441105"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerenciar protocolos e cifras no Gerenciamento de API do Azure

O Gerenciamento de API do Azure dá suporte a várias versões do protocolo TLS para os lados do cliente e do back-end, bem como para a cifra 3DES.

Este guia mostra como gerenciar a configuração de protocolos e cifras para uma instância de Gerenciamento de API do Azure.

![Gerenciar protocolos e cifras no APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você precisa ter:

* Uma instância de Gerenciamento de API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerenciar protocolos TLS e a cifra 3DES

1. Navegue até sua **instância de Gerenciamento de API** no portal do Azure.
2. Selecione **SSL** no menu.  
    ![Gerenciar protocolos e cifras no APIM – menu](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Habilite ou desabilite os protocolos ou cifras desejados.
4. Clique em **Salvar**. As alterações serão aplicadas em uma hora.  
    ![Gerenciar protocolos e cifras no APIM – salvar](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.