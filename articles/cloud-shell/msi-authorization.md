---
title: Usar MSI no Azure Cloud Shell | Microsoft Docs
description: Autenticação de código com MSI no Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="use-msi-in-azure-cloud-shell"></a>Usar MSI no Azure Cloud Shell

O Azure Cloud Shell oferece suporte à autorização com as Identidades de Serviço Gerenciadas (MSI). Utilize essa opção para recuperar tokens de acesso para se comunicar com segurança com os serviços do Azure.

## <a name="about-managed-service-identity-msi"></a>Sobre a Identidade de Serviço Gerenciada (MSI)
Um desafio comum ao criar aplicativos de nuvem é como gerenciar com segurança as credenciais que precisam estar em seu código para autenticar para serviços de nuvem. No Cloud Shell você precisará autenticar a recuperação do Key Vault para uma credencial que um script possa ser necessário.

A Identidade de Serviço Gerenciado (MSI) torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir o token de acesso no Cloud Shell

Execute os seguintes comandos para definir o token de acesso do MSI como uma variável de ambiente `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Manipulando a expiração do token

O subsistema de MSI local armazena em cache os tokens. Portanto, você pode chamá-lo sempre que desejar e uma chamada durante a transmissão para resultados do Azure AD somente se:
- ocorrer um cache ignorado devido a não haver token no cache
- o token está expirado

Se você armazenar em cache o token em seu código, deverá estar preparado para lidar com cenários em que o recurso indique que o token expirou.

Para tratar erros de token, visite a [página MSI sobre tokens de acesso MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Aquisição de tokens de acesso da MSI VMs](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
