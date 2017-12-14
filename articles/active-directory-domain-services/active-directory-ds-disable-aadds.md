---
title: Desabilitar o Azure Active Directory Domain Services | Microsoft Docs
description: Desabilitar o Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: f61f6df85e47bec32e147990d956a4409429a60c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
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
