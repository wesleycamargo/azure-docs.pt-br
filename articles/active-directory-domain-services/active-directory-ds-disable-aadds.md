---
title: Desabilitar o Azure Active Directory Domain Services | Microsoft Docs
description: Desabilitar o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: ergreenl
ms.openlocfilehash: ac5c7023206050b668c7ad67e9bc8842dae9f0a4
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847774"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Desabilitar o Azure Active Directory Domain Services usando o portal do Azure
Este artigo mostra como usar o portal do Azure para desabilitar o Azure Active Directory (AD) Domain Services para seu diretório do Azure AD.

> [!WARNING]
> **A exclusão é permanente e não pode ser revertida.**
> Continue com cuidado! Quando você excluir o domínio gerenciado:
  * Controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
  * Os dados em um domínio gerenciado serão excluídos permanentemente. Isso inclui UOs, GPOs, registros de DNS personalizados, entidades de serviço, GMSAs e outros itens personalizados que você criou no domínio gerenciado.
  * Os computadores que ingressaram no domínio gerenciado perdem sua relação de confiança com o domínio e precisam ser retirados do domínio.
  * Você não pode entrar nesses computadores usando credenciais corporativas do AD. Em vez disso, use as credenciais de administrador local do computador.
Excluir o domínio gerenciado não exclui seu diretório do Azure AD ou afeta negativamente o diretório.
>

Execute as seguintes etapas para excluir seu domínio gerenciado do Azure AD Domain Services:
1. Navegue até a [extensão do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no nome do seu domínio gerenciado.

    ![Selecionar o domínio a ser excluído](./media/getting-started/domain-services-delete-select-domain.png)

3. Na página **Visão Geral**, clique no botão **Excluir**.

    ![Excluir domínio](./media/getting-started/domain-services-delete-domain.png)

4. Para confirmar a exclusão, digite o nome de domínio DNS do domínio gerenciado. Clique no botão **Excluir** quando terminar.

    ![Confirmação de exclusão do domínio](./media/getting-started/domain-services-delete-domain-confirm.png)

O domínio gerenciado é excluído em cerca de 15 a 20 minutos.

Considere [compartilhar comentários](active-directory-ds-contact-us.md) para nos ajudar a entender quais recursos ajudariam você a escolher os Azure AD Domain Services no futuro. Esses comentários nos ajudam a desenvolver o serviço a fim de atender melhor às suas necessidades de implantação e casos de uso.
