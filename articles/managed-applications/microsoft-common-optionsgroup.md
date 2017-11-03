---
title: "Elemento de interface do usuário OptionsGroup de aplicativo gerenciado do Azure | Microsoft Docs"
description: "Descreve o elemento Microsoft.Common.OptionsGroup da interface do usuário para aplicativos gerenciados do Azure"
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
ms.openlocfilehash: 0ba820176e0231437e878922cb508cd3b97d46c3
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento de interface do usuário Microsoft.Common.OptionsGroup
Um controle de seleção com uma linha de opções disponíveis. Use esse elemento ao [criar um Aplicativo Gerenciado do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- O rótulo de `constraints.allowedValues` é o texto exibido para um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente em `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado por padrão. O valor padrão é **null**.
- `constraints.allowedValues` deve conter pelo menos um item.
- Esse elemento não dá suporte à propriedade `constraints.required`; um item deve ser selecionado para validar com êxito.

## <a name="sample-output"></a>Saída de exemplo
```json
"Bar"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](overview.md).
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
