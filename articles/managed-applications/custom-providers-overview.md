---
title: Visão geral da visualização do Azure provedores personalizados
description: Descreve os conceitos para a criação de um provedor de recursos personalizado com o Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159849"
---
# <a name="azure-custom-providers-preview-overview"></a>Visão geral do Azure de visualização de provedores personalizados

Com provedores de personalizado do Azure, você pode estender o Azure para trabalhar com seu serviço. Você criar seu próprio provedor de recursos, incluindo tipos de recurso personalizado e ações. O provedor personalizado é integrado com o Azure Resource Manager. Você pode usar os recursos do Gerenciador de recursos, como implantações de modelo e controle de acesso baseado em função, implantar e proteger seu serviço.

Este artigo fornece uma visão geral dos provedores personalizados e seus recursos. A imagem a seguir mostra o fluxo de trabalho para recursos e as ações definidas em um provedor personalizado.

![Visão geral do provedor personalizado](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Provedores personalizados está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definir o provedor personalizado

Iniciar, permitindo que o Azure Resource Manager saber sobre o provedor personalizado. Implantar no Azure um recurso de provedor personalizado, que usa o tipo de recurso **Microsoft.CustomProviders/resourceProviders**. Esse recurso, você define os recursos e as ações para o seu serviço.

Por exemplo, se seu serviço precisar de um tipo de recurso denominado **usuários**, inclua esse tipo de recurso em sua definição de provedor personalizado. Para cada tipo de recurso, você deve fornecer um ponto de extremidade que oferece as operações de REST (PUT, GET, DELETE) para esse tipo de recurso. O ponto de extremidade pode ser hospedado em qualquer ambiente, e contém a lógica de como seu serviço lida com operações no tipo de recurso.

Você também pode definir a ações personalizadas para seu provedor de recursos. Ações representam operações POST. Use ações para operações como iniciar, parar ou reiniciar. Você fornece um ponto de extremidade que manipula a solicitação.

O exemplo a seguir mostra como definir um provedor personalizado com uma ação e um tipo de recurso.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Para **routingType**, os valores aceitos são `Proxy` e `Cache`. Proxy significa que as solicitações para o tipo de recurso ou ação são manipuladas pelo ponto de extremidade. A configuração de cache só há suporte para tipos de recursos, não ações. Para especificar o cache, você também deve especificar o proxy. Cache significa que as respostas do ponto de extremidade são armazenadas para otimizar operações de leitura. Usar a configuração de cache torna mais fácil de implementar uma API que é consistente e em conformidade com outros serviços do Gerenciador de recursos.

## <a name="deploy-your-resource-types"></a>Implantar os seus tipos de recursos

Depois de definir o provedor personalizado, você pode implantar seus tipos de recurso personalizado. O exemplo a seguir mostra o JSON que você incluir em seu modelo para implantar o tipo de recurso para o provedor personalizado. Esse tipo de recurso pode ser implantado no mesmo modelo com outros recursos do Azure.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Gerenciar acesso

Usar o Azure [controle de acesso baseado em função](../role-based-access-control/overview.md) para gerenciar o acesso ao seu provedor de recursos. Você pode atribuir [funções internas](../role-based-access-control/built-in-roles.md) como proprietário, colaborador ou leitor para os usuários. Ou, você pode definir [funções personalizadas](../role-based-access-control/custom-roles.md) que são específicos para as operações no seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

> [!div class="nextstepaction"]
> [Tutorial: Criar um provedor personalizado e implantar recursos personalizados](create-custom-provider.md)
