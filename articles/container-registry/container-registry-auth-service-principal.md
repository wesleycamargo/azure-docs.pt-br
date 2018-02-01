---
title: "Autenticação do Registro de Contêiner do Azure com entidades de serviço"
description: "Saiba como fornecer acesso a imagens em seu registro de contêiner particular usando uma entidade de serviço do Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 97036ecabceb12b87b76c6ecb7e521157cbef827
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do Registro de Contêiner do Azure com entidades de serviço

Use uma entidade de serviço do Azure AD (Azure Active Directory) para fornecer à imagem de contêiner o acesso `docker push` e `pull` ao seu registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a serviços e aplicativos “sem periféricos”.

## <a name="what-is-a-service-principal"></a>O que é uma entidade de serviço?

As *entidades de serviço* do Azure AD fornecem acesso aos recursos do Azure em sua assinatura. Você pode considerar uma entidade de serviço como uma identidade do usuário de um serviço, em que o “serviço” é qualquer aplicativo, serviço ou plataforma que precisa de acesso aos recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas nos recursos especificados. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do Registro de Contêiner do Azure, você pode criar uma entidade de serviço do Azure AD com permissões de pull, push e pull ou proprietário em seu Registro particular do Docker no Azure.

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner particular. Você pode criar entidades de serviço diferentes para cada um dos aplicativos ou serviços, cada uma com direitos de acesso adaptadas ao registro. Já que você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar as credenciais ou revogar o acesso somente à entidade de serviço (e, portanto, ao aplicativo) de sua escolha.

Por exemplo, o aplicativo Web pode usar uma entidade de serviço que fornece somente o acesso `pull` à imagem, enquanto o sistema de build pode usar uma entidade de serviço que fornece o acesso `push` e `pull`. Se o desenvolvimento de seu aplicativo for transferido para outra pessoa, você poderá girar as credenciais da entidade de serviço sem afetar o sistema de build.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer o acesso de registro em **cenários “sem periféricos”**. Em outras palavras, qualquer aplicativo, serviço ou script que precisa enviar por push ou efetuar pull de imagens de contêiner de maneira automatizada ou autônoma.

Para acesso individual a um registro, como quando você efetua pull manualmente de uma imagem de contêiner para sua estação de trabalho de desenvolvimento, você deve usar sua própria [identidade do Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) para acesso de registro (por exemplo, com [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="next-steps"></a>Próximas etapas

Quando você tiver uma entidade de serviço para a qual você concedeu o acesso ao registro de contêiner, poderá usar suas credenciais nos aplicativos e serviços para interação com o registro.

Embora a configuração de aplicativos individuais para usar as credenciais da entidade de serviço esteja fora do escopo deste artigo, você poderá encontrar instruções para alguns serviços e plataformas específicas aqui:

* [Autenticar no Registro de Contêiner do Azure por meio do AKS (Serviço de Contêiner do Azure)](container-registry-auth-aks.md)
* [Autenticar no Registro de Contêiner do Azure por meio do ACI (Instâncias de Contêiner do Azure)](container-registry-auth-aci.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login