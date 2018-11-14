---
title: Entenda o controle de acesso baseado em função dos Gêmeos Digitais do Azure | Microsoft Docs
description: Aprenda a autenticação em gêmeos digitais com controle de acesso baseado em função.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014781"
---
# <a name="role-based-access-control"></a>Controle de acesso baseado em função

O Azure Digital Twins permite o controle preciso do acesso a dados, recursos e ações específicos em seu gráfico espacial. Isso é feito por meio de um gerenciamento granular de funções e permissões chamado RBAC (controle de acesso baseado em função). O RBAC consiste em _funções_ e _atribuições de funções_. Funções de identificam o nível de permissões. As atribuições de função associam uma função com um usuário ou dispositivo.

Usando o RBAC, pode ser concedida permissão para:

- Um usuário.
- Um dispositivo.
- Uma entidade de serviço.
- Uma função definida pelo usuário. 
- Todos os usuários que pertencem a um domínio. 
- Um locatário.
 
O grau de acesso também pode ser ajustado.

O RBAC é único, pois as permissões são herdadas no gráfico espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor pode usar o RBAC para:

* Conceda ao usuário a capacidade de gerenciar dispositivos para um prédio inteiro ou apenas para uma sala ou andar específico.
* Conceda a um administrador acesso global a todos os nós de gráfico espacial para um gráfico inteiro ou apenas para uma seção do gráfico.
* Conceda um acesso de leitura de especialista em suporte ao gráfico, exceto para as chaves de acesso.
* Conceda a todos os membros de um domínio de acesso de leitura a todos os objetos de gráfico.

## <a name="rbac-best-practices"></a>Práticas recomendadas de RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma definição de função é uma coleção de permissões e, às vezes, é chamada de uma função. Lista de definição de função para criar as operações permitidas, que incluem, leitura, atualização e exclusão. Ele também especifica para quais tipos de objetos essas permissões se aplicam.

As seguintes funções estão disponíveis em Gêmeos Digitais do Azure:

* **Administrador de espaço**: Crie, leia, atualize e exclua a permissão para o espaço especificado e todos os nós abaixo. Permissão global.
* **Usuário administrador**: criar, ler, atualizar e excluir a permissão para usuários e objetos relacionados ao usuário. Permissão de leitura para espaços.
* **Administrador do dispositivo**: criar, ler, atualizar e excluir a permissão para dispositivos e objetos relacionados ao dispositivo. Permissão de leitura para espaços.
* **Chave Administradora**: Crie, leia, atualize e exclua a permissão para as chaves de acesso. Permissão de leitura para espaços.
* **Administrador de token**: permissão de leitura e atualização para chaves de acesso. Permissão de leitura para espaços.
* **Usuário**: permissão de leitura para usuários, sensores e espaços, que inclui suas respectivas objetos relacionados.
* **Especialista de Suporte**: Permissão de leitura para tudo, exceto chaves de acesso.
* **Instalador de Dispositivos**: Leia e atualize a permissão para dispositivos e sensores, o que inclui seus objetos relacionados correspondentes. Permissão de leitura para espaços.
* **Dispositivo de gateway**: crie permissão para sensores. Permissão de leitura para dispositivos e sensores, que inclui seus objetos relacionados correspondentes.

>[!NOTE]
> Para recuperar as definições completas para funções anteriores, consulte as sistema/funções de API.

### <a name="object-types"></a>Tipos de objeto

O `ObjectIdType` refere-se ao tipo de identidade que recebe uma função. Além do `DeviceId` e `UserDefinedFunctionId` tipos, os tipos correspondem a uma propriedade de um objeto do Active Directory do Azure (AD Azure):
  
* O `UserId` tipo atribui uma função a um usuário.
* O `DeviceId` tipo atribui uma função a um dispositivo.
* O `DomainName` tipo atribui uma função a um nome de domínio. Cada usuário com o nome de domínio especificado possui os direitos de acesso da função correspondente.
* O `TenantId` tipo atribui uma função a um locatário. Cada usuário que pertence ao ID de locatário do Microsoft Azure Active Directory especificado tem os direitos de acesso da função correspondente.
* O `ServicePrincipalId` tipo atribui uma função a uma ID de objeto de entidade de serviço.
* O `UserDefinedFunctionId` tipo atribui uma função a uma função definida pelo usuário (UDF).

> [!div class="nextstepaction"]
> [Consulta ou a ID de objeto para um usuário](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Obter a ID de objeto para uma entidade de serviço](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Recuperar a ID de objeto para um locatário do AD do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Atribuições de função

Para conceder permissões a um destinatário, crie uma atribuição de função. Para revogar permissões, remova a atribuição de função. Uma atribuição de função do Azure Digital Twins associa um objeto, como um usuário ou um locatário dos Gêmeos Digitais do Azure, a uma função e um espaço. As permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui todo o gráfico espacial abaixo dele.

Por exemplo, um usuário recebe uma atribuição de função com a função `DeviceInstaller` para o nó raiz de um gráfico espacial, que representa um edifício. O usuário pode então ler e atualizar dispositivos para esse nó e todos os outros espaços filhos no edifício.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
