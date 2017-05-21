---
title: "Elemento de interface do usuário CredentialsCombo de aplicativo gerenciado do Azure | Microsoft Docs"
description: "Descreve o elemento Microsoft.Compute.CredentialsCombo da interface do usuário para aplicativos gerenciados do Azure"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 80f35b5df784985afcf8b8f70cea7cd0886d5007
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de interface do usuário Microsoft.Compute.CredentialsCombo
Um grupo de controles com validação interna para chaves públicas SSH e senhas Windows e Linux.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Esquema
Se `osPlatform` é **Windows**, o seguinte esquema é usado:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Se `osPlatform` é **Linux**, o seguinte esquema é usado:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- `osPlatform` deve ser especificada e pode ser **Windows** ou **Linux**.
- Se `constraints.required` é definido como **true**, as caixas de texto da chave pública SSH ou senha devem conter valores para serem validados com êxito. O valor padrão é **true**.
- Se `options.hideConfirmation` for definido como **true**, a segunda caixa de texto para confirmar a senha do usuário ficará oculta. O valor padrão é **false**.
- Se `options.hidePassword` é definido como **true**, a opção para usar a autenticação de senha fica oculta. Ele pode ser usado apenas quando `osPlatform` é **Linux**. O valor padrão é **false**.
- Outras restrições sobre as senhas permitidas podem ser implementadas usando a propriedade `customPasswordRegex`. A cadeia de caracteres em `customValidationMessage` é exibida quando uma senha falha a validação personalizada. O valor padrão para ambas as propriedades é **null**.

## <a name="sample-output"></a>Saída de exemplo
Se `osPlatform` é **Windows**, ou o usuário forneceu uma senha em vez de uma chave pública SSH,a seguinte saída é esperada:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Se o usuário forneceu uma chave pública SSH, a seguinte saída é esperada:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](managed-application-createuidefinition-elements.md).

