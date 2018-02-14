---
title: "Desabilitar a verificação de email durante a inscrição do consumidor – Azure Active Directory B2C"
description: "Um tópico que demonstra como desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
ms.custom: seohack1
ms.openlocfilehash: f8e7e5dd8e5e5da6ff4c1e82215da019e8e101c9
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: Desabilitar a verificação de email durante a inscrição do consumidor
Quando está habilitado, o Azure Active Directory (Azure AD) B2C fornece a um consumidor a capacidade de se inscrever em aplicativos fornecendo um endereço de email e criando uma conta local. O Azure AD B2C verifica os endereços de email válidos exigindo que os consumidores os confirmem durante o processo de inscrição. Ele também impede que um processo automatizado mal-intencionado gere contas falsas para os aplicativos.

Alguns desenvolvedores de aplicativo preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, pedem para os consumidores verificarem o endereço de email mais tarde. Para dar suporte a isso, o Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais tranquilo e oferece aos desenvolvedores a flexibilidade para diferenciar os consumidores que confirmaram seu endereço de email daqueles que não o fizeram.

Por padrão, as políticas de inscrição têm a verificação de email ativada. Use as etapas a seguir para desativá-la:

1. Siga estas etapas para [navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **Políticas de inscrição** ou em **Políticas de inscrição ou entrada** dependendo do que você configurou para inscrição.
3. Clique em sua política (por exemplo, "B2C_1_SiUp") para abri-la. Clique em **Editar** na parte superior da folha.
4. Clique em **Personalização da interface do usuário da página**.
5. Clique em **Página de inscrição da conta local**.
6. Clique em **Endereço de Email** na coluna **Nome** na seção **Atributos de inscrição**.
7. Alterne a opção **Exigir verificação** para **Não**.
8. Clique em **OK** na parte inferior até chegar à folha **Editar política**.
9. Clique em **Salvar** na parte superior da folha. Pronto!

> [!NOTE]
> Desabilitar a verificação de email no processo de inscrição pode gerar spam. Se você desabilitar o padrão, recomendamos adicionar seu próprio sistema de verificação.
> 
> 

Estamos sempre abertos a comentários e sugestões! Caso você tenha alguma dúvida sobre este tópico ou recomendações para melhorar o conteúdo, agradecemos seus comentários na parte inferior da página. Para solicitações de recursos, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
