---
title: Esquema de função dos Serviços de Nuvem do Azure | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 2f5c657bb80ad0788bcc3dd19d962b3f21afa4a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-config-role-schema"></a>Esquema de função de configuração dos Serviços de Nuvem do Azure

O elemento `Role` do arquivo de configuração especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função.

Para obter mais informações sobre o Esquema de configuração do Serviço do Azure, consulte [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Esquema de configuração do Serviço de Nuvem (clássico)). Para obter mais informações sobre o Esquema de definição do Serviço do Azure, consulte [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Esquema de definição do Serviço de Nuvem (clássico)).

##  Elemento de função <a name="Role"></a>
O exemplo a seguir mostra o elemento `Role` e seus elementos filho.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

A tabela a seguir descreve os atributos do elemento `Role`.

| Atributo | DESCRIÇÃO |
| --------- | ----------- |
| Nome   | Obrigatório. Especifica o nome da função. O nome deve corresponder ao nome fornecido para a função no arquivo de definição de serviço.|
| vmName | Opcional. Especifica um nome DNS para uma máquina virtual. O nome deve ter dez caracteres ou menos.|

A tabela a seguir descreve os elementos filho do elemento `Role`.

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| Instâncias | Obrigatório. Especifica o número de instâncias a serem implantadas para a função. O número de instâncias é definido por um inteiro para o atributo `count`.|
| Configuração   | Opcional. Especifica um nome e valor da configuração em uma coleção de configurações para uma função. O nome da configuração é definido por uma cadeia de caracteres para o atributo `name` e o valor da configuração é definido por uma cadeia de caracteres para o atributo `value`.|
| Certificado | Opcional. Especifica o nome, a impressão digital e o algoritmo de um certificado de serviço a ser associado à função. O nome do certificado é definido por uma cadeia de caracteres para o atributo `name`. A impressão digital do certificado é definida por uma cadeia de caracteres de números hexadecimais sem espaços para o atributo `thumbprint`. Os números hexadecimais devem ser representados usando dígitos e caracteres alfa maiúsculos. O algoritmo do certificado é definido por uma cadeia de caracteres para o atributo `thumbprintAlgorithm`.|

## <a name="see-also"></a>Veja também
[Esquema de configuração do Serviço de Nuvem (clássico)](schema-cscfg-file.md)