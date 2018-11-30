---
title: Adicionar um Certificado de Autoridade de Certificação personalizado – Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como adicionar um Certificado de Autoridade de Certificação personalizado no Gerenciamento de API do Azure.
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
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: b05713fd8acb49b25dba04781c1049c9f05115fb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444676"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Como adicionar um Certificado de Autoridade de Certificação personalizado no Gerenciamento de API do Azure

O Gerenciamento de API do Azure permite instalar Certificados de Autoridade de Certificação no computador de dentro da raiz confiável e de repositórios de certificados intermediários. Essa funcionalidade deve ser usada se os serviços exigem um Certificado de Autoridade de Certificação personalizado.

O artigo mostra como gerenciar Certificado de Autoridade de Certificação de uma instância do serviço Gerenciamento de API do Azure no portal do Azure.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Carregar um Certificado de Autoridade de Certificação

![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/00.png)

Siga as etapas abaixo para fazer upload de um novo Certificado de Autoridade de Certificação do cliente. Se você ainda não criou uma instância de serviço de Gerenciamento de API, veja o tutorial [Criar uma instância de serviço de Gerenciamento de API](get-started-create-service-instance.md).

1. Navegue até a instância de serviço do Gerenciamento de API do Azure no portal do Azure.

2. Selecione **Certificados de Autoridade de Certificação** do menu.

3. Clique no botão **+ Adicionar**.  

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/01.png)  

4. Procure o certificado e decida quanto ao repositório de certificados. Somente a chave pública é necessária, portanto, a senha não é necessária.

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/02.png)  

5. Clique em **Salvar**. Esta operação pode levar alguns minutos.

    ![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Você pode carregar um Certificado de Autoridade de Certificação usando o comando `New-AzureRmApiManagementSystemCertificate` do Powershell.

## <a name="step1a"> </a>Excluir um certificado do cliente

Para excluir um certificado, clique no menu de contexto **...** e selecione **Excluir** ao lado do certificado.

![Excluir Certificados de Autoridade de Certificação](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a