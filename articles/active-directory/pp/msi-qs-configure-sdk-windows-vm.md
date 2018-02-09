---
title: "Como configurar uma MSI atribuída pelo usuário para uma VM do Azure usando um SDK do Azure"
description: "Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Azure usando um SDK do Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 097304162b85599acd1f4591091f986a646ebc2a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Configurar uma MSI (Identidade de Serviço Gerenciado) para uma VM usando um SDK do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure AD sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover uma MSI atribuída pelo usuário para uma VM do Azure, usando um SDK do Azure.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>SDKs do Azure com suporte para MSI 

O Azure dá suporte a várias plataformas de programação por meio de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Vários deles foram atualizados para oferecer suporte à MSI e apresentam exemplos correspondentes para demonstrar o uso. Esta lista é atualizada conforme suporte adicional é adicionado:

| . | Amostra |
| --- | ------ | 
| Python | [Criar uma VM com MSI habilitada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar uma VM do Azure com MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Próximas etapas

- Consulte os artigos relacionados em "Configurar MSI para uma VM do Azure", para saber como configurar uma MSI atribuída pelo usuário em uma VM do Azure.

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
