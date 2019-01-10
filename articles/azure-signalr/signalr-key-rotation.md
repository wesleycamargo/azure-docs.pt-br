---
title: Rotação de chaves de acesso para o Serviço do Azure SignalR
description: Uma visão geral sobre o motivo pelo qual o cliente precisa girar as chaves de acesso de forma rotineira e como fazer isso com a GUI e a CLI do portal.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636054"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Rotação de chaves de acesso para o Serviço do Azure SignalR

Cada instância do Serviço do Azure SignalR tem um par de chaves de acesso: Chaves Primárias e Secundárias. Elas são usadas para autenticar clientes do SignalR ao fazer solicitações para o serviço. As chaves são associadas à URL de ponto de extremidade da instância. Proteja suas chaves e gire-as regularmente. Você recebe duas chaves de acesso, de modo que possa manter conexões usando uma chave enquanto regenera a outra.

## <a name="why-rotate-access-keys"></a>Por que girar as chaves de acesso?

Por motivo de segurança e para o requisito de conformidade, recomendamos que os desenvolvedores girem as chaves de acesso de forma rotineira.

## <a name="how-to-regenerate-access-keys"></a>Como regenerar as chaves de acesso?

1. Vá para o [portal do Azure](https://portal.azure.com/) e entre com suas credenciais.

1. Encontre a seção **Chaves** da instância do Serviço do Azure SignalR que deseja regenerar as chaves.

1. Clique em **Chaves** no menu de navegação.

1. Clique em **Regenerar Chave Primária** ou **Regenerar Chave Secundária**.

Uma nova chave e a cadeia de conexão correspondente será criada e exibida.

 ![Regenerar chaves](media/signalr-key-rotation/regenerate-keys.png)

Você também pode regenerar as chaves usando a [CLI do Azure](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Atualizar configurações com novas cadeias de conexão

1. Copie a cadeia de conexão recém-gerada.

1. Atualize todas as configurações para que usem a nova cadeia de conexão.

1. Reinicie o aplicativo, conforme necessário.

## <a name="forced-access-key-regeneration"></a>Regeneração forçada de chaves de acesso

O Serviço do Azure SignalR pode impor uma regeneração obrigatória de chaves de acesso em determinada situação. O serviço notificará os clientes por email e pela notificação no portal. Se você receber essa comunicação ou se houver uma falha do serviço devido à chave de acesso, gire as chaves seguindo este guia.

## <a name="next-steps"></a>Próximas etapas

Recomendamos girar as chaves de acesso regularmente como uma boa prática de segurança.

Neste guia, você aprendeu a regenerar as chaves de acesso. Continue para os próximos tutoriais sobre autenticação com o OAuth ou o Azure Functions.

> [!div class="nextstepaction"]
> [Integração com o ASP.NET Core Identity](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Compilar um aplicativo em tempo real sem servidor com a autenticação](./signalr-authenticate-azure-functions.md)