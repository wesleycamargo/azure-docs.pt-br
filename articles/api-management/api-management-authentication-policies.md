---
title: Políticas de autenticação de Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de autenticação disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250944"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Políticas de autenticação  
  
-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.  
  
-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.  
  
##  <a name="Basic"></a> Autenticar com o Basic  
 Use a política `authentication-basic` para autenticar com um serviço de back-end usando a autenticação do Basic. Essa política define efetivamente o cabeçalho de autorização HTTP para o valor correspondente às credenciais fornecidas na política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementos  
  
|NOME|DESCRIÇÃO|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-basic|Elemento raiz.|SIM|  
  
### <a name="attributes"></a>Atributos  
  
|NOME|DESCRIÇÃO|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|Nome de Usuário|Especifica o nome de usuário da credencial do Basic.|SIM|N/D|  
|Senha|Especifica a senha da credencial do Basic.|SIM|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada  
  
-   **Escopos de política:** API  
  
##  <a name="ClientCertificate"></a> Autenticar com o certificado de cliente  
 Use a política `authentication-certificate` para autenticar com um serviço de back-end usando um certificado de cliente. O certificado precisa ser [instalado no Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=511599) primeiro e é identificado por sua impressão digital.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementos  
  
|NOME|DESCRIÇÃO|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raiz.|SIM|  
  
### <a name="attributes"></a>Atributos  
  
|NOME|DESCRIÇÃO|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|impressão digital|A impressão digital do certificado do cliente.|SIM|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada  
  
-   **Escopos de política:** API  
  

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
