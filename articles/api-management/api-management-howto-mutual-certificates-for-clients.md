---
title: "Proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API — Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como proteger o acesso às APIs usando certificados do cliente"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API

O Gerenciamento de API fornece a capacidade de proteger o acesso às APIs (isto é, cliente para Gerenciamento de API) usando certificados do cliente. No momento, você pode verificar a impressão digital de um certificado do cliente em relação a um valor desejado. Também é possível verificar a impressão digital em relação a certificados existentes carregados no Gerenciamento de API.  

Para saber mais sobre como proteger o acesso ao serviço de back-end de uma API usando certificados do cliente (isto é, Gerenciamento de API para back-end), confira [Como garantir serviços de back-end usando autenticação de certificado do cliente](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-a-thumbprint-against-a-desired-value"></a>Verificação de uma impressão digital em relação a um valor desejado

Veja abaixo que as políticas podem ser configuradas para verificar a impressão digital de um certificado do cliente:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital em relação a certificados carregados no Gerenciamento de API

O exemplo a seguir mostra como verificar a impressão digital de um certificado do cliente em relação a certificados carregados no Gerenciamento de API: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Próxima etapa

*  [Como garantir serviços de back-end usando autenticação de certificado do cliente](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


