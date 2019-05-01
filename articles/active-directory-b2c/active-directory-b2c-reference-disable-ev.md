---
title: Desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C | Microsoft Docs
description: Um tópico que demonstra como desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 01d96370fc7ce601ba5626ca0f1ec28871bf311a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703326"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Desabilitar a verificação de e-mail durante a inscrição do consumidor no B2C do Azure Active Directory B2C 
Quando está habilitado, o Azure Active Directory (Azure AD) B2C fornece a um consumidor a capacidade de se inscrever em aplicativos fornecendo um endereço de email e criando uma conta local. O Azure AD B2C verifica os endereços de email válidos exigindo que os consumidores os confirmem durante o processo de inscrição. Ele também impede que um processo automatizado mal-intencionado gere contas falsas para os aplicativos.

Alguns desenvolvedores de aplicativo preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, pedem para os consumidores verificarem o endereço de email mais tarde. Para dar suporte a isso, o Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais tranquilo e oferece aos desenvolvedores a flexibilidade para diferenciar os consumidores que confirmaram seu endereço de email daqueles que não o fizeram.

Por padrão, os fluxos de usuário de inscrição têm a verificação de email ativada. Use as etapas a seguir para desativá-la:

1. Clique em **Fluxos de usuário**.
2. Clique no seu fluxo de usuário (por exemplo, "B2C_1_SiUp") para abri-la. 
3. Clique em **Layouts de página**.
4. Clique em **Página de inscrição da conta local**.
5. Clique em **Endereço de Email** na coluna **Nome** na seção **Atributos de usuário**.
6. Em **Requer uma verificação**, selecione **Não**.
7. Clique em **Salvar** na parte superior da folha. Pronto!

> [!NOTE]
> Desabilitar a verificação de email no processo de inscrição pode gerar spam. Se você desabilitar o padrão, recomendamos adicionar seu próprio sistema de verificação.
> 
>
