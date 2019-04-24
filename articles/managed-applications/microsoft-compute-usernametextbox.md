---
title: Elemento de interface do usuário UserNameTextBox do Azure | Microsoft Docs
description: Descreve o elemento Microsoft.Common.UserNameTextBox da interface do usuário para o portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251372"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Compute.UserNameTextBox
Um controle de caixa de texto com validação interna para nomes de usuário do Windows e do Linux.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- Se `constraints.required` estiver definido como **true**, a caixa de texto deve ter um valor para validar com êxito. O valor padrão é **true**.
- `osPlatform` deve ser especificada e pode ser **Windows** ou **Linux**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **null**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha a validação especificada por `constraints.regex`. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **null**.
- Este elemento tem validação interna com base no valor especificado para `osPlatform`. A validação interna pode ser usada juntamente com uma expressão regular personalizada. Se um valor para `constraints.regex` for especificado, as validações internas e personalizadas serão disparadas.

## <a name="sample-output"></a>Saída de exemplo
```json
"Example name"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
