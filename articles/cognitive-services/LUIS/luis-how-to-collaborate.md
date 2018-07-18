---
title: Colaborar com outros colaboradores em aplicativos LUIS no Azure | Microsoft Docs
description: Saiba como colaborar com outros colaboradores em aplicativos LUIS (Reconhecimento vocal).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364393"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Colabore com outras pessoas em aplicativos LUIS (Reconhecimento vocal)  

Você pode colaborar com outras pessoas em seu aplicativo LUIS. 

## <a name="owner-and-collaborators"></a>Proprietário e colaboradores
Um aplicativo tem um único proprietário, mas pode ter vários colaboradores. 

## <a name="add-collaborator"></a>Adicionar colaborador

Para permitir que os colaboradores editem seu aplicativo LUIS, na página **Configurações** do aplicativo LUIS, insira o email do colaborador e clique em **Adicionar colaborador**.

![Adicionar colaborador](./media/luis-how-to-collaborate/add-collaborator.png)

* Os colaboradores podem entrar e editar o aplicativo LUIS ao mesmo tempo em que você está trabalhando no aplicativo. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Os colaboradores não podem adicionar outros colaboradores.

## <a name="set-application-as-public"></a>Definir o aplicativo como público
Confira [Acesso do ponto de extremidade do aplicativo público](luis-concept-security.md#public-app-endpoint-access) para obter mais informações.

### <a name="transfer-of-ownership"></a>Transferência de propriedade
Embora atualmente o LUIS não dê suporte à transferência de propriedade, você pode exportar seu aplicativo e outro usuário do LUIS pode importá-lo. Pode haver pequenas diferenças nas pontuações do LUIS entre os dois aplicativos. 
