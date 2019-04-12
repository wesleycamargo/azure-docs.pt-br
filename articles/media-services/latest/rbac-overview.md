---
title: Controle de acesso baseado em função para contas de serviços de mídia - Azure | Microsoft Docs
description: Este artigo discute o controle de acesso baseado em função (RBAC) para contas de serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490768"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Controle de acesso baseado em função (RBAC) para contas de serviços de mídia

Atualmente, os serviços de mídia do Azure não definido qualquer específica de funções personalizadas para o serviço. Os clientes podem usar as funções internas do **proprietário** ou **Colaborador** para obter acesso completo a uma conta de serviços de mídia. A principal diferença entre essas funções é: o **proprietário** possível controlar quem tem acesso a um recurso e o **Colaborador** não é possível. A conta do leitor interno só tem acesso de leitura à conta de serviços de mídia. 

## <a name="design-principles"></a>Princípios de design

Um dos princípios de design de chave da API v3 é tornar a API mais segura. APIs v3 não retornar segredos ou as credenciais na **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separada para obter as credenciais ou segredos. O **leitor** função não é possível chamar as operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite que você defina permissões mais granulares de segurança RBAC em uma função personalizada se desejado.

Para listar as operações dos serviços de mídia dá suporte a, execute:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O [definições de função interna](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) artigo lhe mostra exatamente o que a função concede. 

Consulte os seguintes artigos para obter mais informações:

- [Funções de administrador da assinatura clássica, funções do Azure RBAC e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é o RBAC para recursos do Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Use o RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Obter política de chave de conteúdo usando o .NET de serviços de mídia](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>Próximas etapas

[Desenvolver com os serviços de mídia APIs v3](media-services-apis-overview.md)
