---
title: Como configurar a política do Azure Active Directory para o catálogo de dados do Azure
description: Você pode encontrar uma situação em que você pode entrar no portal do catálogo de dados do Azure, mas quando você tenta fazer logon ferramenta de registro de fonte de dados, você encontrar uma mensagem de erro.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116594"
---
# <a name="azure-active-directory-policy-configuration"></a>Configuração de política do Azure Active Directory

Pode haver uma situação em que você pode entrar no portal do Catálogo de Dados do Azure, mas ao tentar entrar na ferramenta de registro de fonte de dados, poderá receber uma mensagem de erro impedindo a entrada. Esse erro pode ocorrer quando você estiver na rede da empresa ou quando você estiver se conectando de fora da rede da empresa.

## <a name="registration-tool"></a>Ferramenta de registro

A ferramenta de registro usa a *Autenticação de Formulários* para validar logons de usuário no Azure Active Directory. Para entrar com êxito, um administrador do Azure Active Directory deve habilitar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, você pode habilitar a autenticação separadamente para conexões de intranet e extranet, conforme mostrado na imagem a seguir. Erros de entrada podem ocorrer se a autenticação de formulários não está habilitada para a rede do qual você está se conectando.

 ![Política de Autenticação Global do AzureActive Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para saber mais, confira [Configurando políticas de autenticação](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Próximas etapas

* [Criar um catálogo de dados do Azure](data-catalog-get-started.md)