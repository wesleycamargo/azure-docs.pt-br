---
title: JavaScript e versões de contrato da página para fluxos dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como habilitar o JavaScript e usar as versões de contrato de página para personalizar um fluxo de usuário no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 02/07/2019
ms.date: 04/04/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360209"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>Sobre como usar JavaScript e a página de versões de contrato em um fluxo de usuário

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdo empacotado que contém HTML, CSS e JavaScript para os elementos de interface do usuário em seus fluxos dos usuários. Se pretende habilitar código [JavaScript](javascript-samples.md) do lado do cliente em seus fluxos dos usuários, convém que você tenha certeza de que os elementos nos quais está baseando o JavaScript são imutáveis. Caso contrário, todas as alterações podem provocar comportamento inesperado em suas páginas de fluxo de usuário. Para evitar esses problemas, você pode impor o uso de um contrato de página para um fluxo de usuário e especificar uma versão de contrato de página. Fazer isso garante que todas as definições de conteúdo nas quais você baseou seu JavaScript sejam imutáveis. Mesmo se não pretende habilitar o JavaScript para um fluxo de usuário, você poderá especificar uma versão de contrato de página para as páginas de fluxo de usuário.

> [!NOTE]
> Este artigo discute o JavaScript para fluxos dos usuários, mas você também pode usar JavaScript e selecionar versões de contrato de página quando está usando [políticas personalizadas](page-contract.md).

## <a name="enable-javascript"></a>Habilitar o JavaScript

Nas propriedades de fluxo de usuário, você pode habilitar o JavaScript, o que também impõe o uso de um contrato de página. Em seguida, você pode definir a versão do contrato de página conforme descrito na próxima seção.

![Habilitar configuração de JavaScript](./media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Especificar uma versão de contrato de página

Independentemente de pretender ou não habilitar o JavaScript nas propriedades do um fluxo de usuário, você poderá especificar uma versão de contrato de página para as páginas desse fluxo. Abra o fluxo de usuário e selecione **Layouts de página**. Em **Nome do Layout**, selecione uma página de fluxo de usuário e escolha o **Página de Versão do Contrato**.

![Habilitar configuração de JavaScript](./media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Próximas etapas
Confira os [Exemplos de JavaScript para uso no Azure Active Directory B2C](javascript-samples.md).

