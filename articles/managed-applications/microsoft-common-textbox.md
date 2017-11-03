---
title: "Elemento de interface do usuário TextBox de aplicativo gerenciado do Azure | Microsoft Docs"
description: "Descreve o elemento Microsoft.Common.TextBox da interface do usuário para aplicativos gerenciados do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: d3fae42ae202fe720761382e1020fa8bd8c62b44
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBox
Um controle que pode ser usado para editar texto não formatado. Use esse elemento ao [criar um Aplicativo Gerenciado do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- Se `constraints.required` é definido como **true**, a caixa de texto deve conter um valor a ser validado com êxito. O valor padrão é **false**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **null**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **null**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` é definido como **false**. Nesse cenário, um valor não é necessário para a caixa de texto validar com êxito. Se um valor for especificado, ele deve corresponder ao padrão da expressão regular.

## <a name="sample-output"></a>Saída de exemplo

```json
"foobar"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](overview.md).
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
