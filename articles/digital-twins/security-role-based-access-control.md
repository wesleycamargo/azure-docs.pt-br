---
title: Compreendendo o controle de acesso baseado em função do Azure Digital Twins | Microsoft Docs
description: Aprenda a autenticação em gêmeos digitais com controle de acesso baseado em função.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323670"
---
# <a name="role-based-access-control"></a>Controle de acesso baseado em função

O Azure Digital Twins permite o controle preciso do acesso a dados, recursos e ações específicos em seu gráfico espacial. Isso é feito por meio de um gerenciamento granular de funções e permissões chamado _Controle de acesso baseado em função_. O Controle de Acesso Baseado em Função consiste em _Funções_ ou no nível de permissões e _Atribuições de Função_ ou na associação de uma função a um usuário ou dispositivo.

Usando o Controle de Acesso Baseado em Função, a permissão pode ser concedida a um usuário, um dispositivo, uma entidade de serviço, uma função definida pelo usuário, todos os usuários pertencentes a um domínio ou um inquilino. Além disso, o grau de acesso também pode ser ajustado.

O Controle de Acesso Baseado em Função é único, pois as permissões são herdadas no gráfico espacial.

## <a name="what-can-i-do-with-role-based-access-control"></a>O que posso fazer com o controle de acesso baseado em função?

Um desenvolvedor pode usar o Controle de Acesso Baseado em Função para:

* Conceda ao usuário a capacidade de gerenciar dispositivos para um prédio inteiro ou apenas para uma sala ou andar específico.
* Conceda a um administrador acesso global a todos os nós de gráfico espacial para um gráfico inteiro ou apenas para uma seção do gráfico.
* Conceda um acesso de leitura de especialista em suporte ao gráfico, exceto para as chaves de acesso.
* Conceda a todos os membros de um domínio de acesso de leitura a todos os objetos de gráfico.

## <a name="role-based-access-control-best-practices"></a>Práticas recomendadas de controle de acesso baseado em função

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma **definição de função** é uma coleção de permissões e às vezes é chamada de uma **função**. A definição de função lista as operações permitidas, incluindo *criar*, *ler*,  *atualizar* e *delete*. Ele também especifica para quais tipos de objetos essas permissões se aplicam.

As seguintes funções estão disponíveis em Gêmeos Digitais do Azure:

* **Administrador de espaço**: permissão Criar, Ler, Atualizar e Excluir para o espaço especificado e todos os nós abaixo. Permissão global.
* **Administrador do usuário**: permissão Criar, Ler, Atualizar e Excluir para usuários e objetos relacionados ao usuário. Permissão de leitura para espaços.
* **Administrador do Dispositivo**: permissão Criar, Ler, Atualizar e Excluir para dispositivos e objetos relacionados ao dispositivo. Permissão de leitura para espaços.
* **Chave Administradora**: Crie, leia, atualize e exclua a permissão para as chaves de acesso. Permissão de leitura para espaços.
* **Administrador de Símbolos**: Leia e atualize a permissão para as chaves de acesso. Permissão de leitura para espaços.
* **Usuário**: permissão de leitura para espaços, sensores e usuários, incluindo seus objetos relacionados correspondentes.
* **Especialista de Suporte**: Permissão de leitura para tudo, exceto chaves de acesso.
* **Instalador de Dispositivos**: permissão de Leitura e Atualização para dispositivos e sensores, incluindo seus objetos relacionados correspondentes. Permissão de leitura para espaços.
* **Dispositivo de gateway**: crie permissão para sensores. Permissão de leitura para dispositivos e sensores, incluindo seus objetos relacionados correspondentes.

>[!NOTE]
> *As definições completas para os itens acima podem ser recuperadas por meio de consulta/funções do sistema de API.*

### <a name="object-types"></a>Tipos de objeto

O `ObjectIdType` refere-se ao tipo de identidade que está sendo dada uma função. Além do `DeviceId` e `UserDefinedFunctionId` tipos, os tipos correspondem a uma propriedade de um objeto do Active Directory do Azure (AD Azure):
  
* O `UserId` tipo atribui uma função a um usuário.
* O `DeviceId` tipo atribui uma função a um dispositivo.
* O `DomainName` tipo atribui uma função a um nome de domínio. Cada usuário com o nome de domínio terá os direitos de acesso da função correspondente.
* O `TenantId` tipo atribui uma função a um locatário. Cada usuário pertencente ao ID de locatário do Azure AD especificado terá os direitos de acesso da função correspondente.
* O `ServicePrincipalId` tipo atribui uma função a uma ID de objeto de entidade de serviço.
* O tipo `UserDefinedFunctionId` atribui uma função a uma função definida pelo usuário (UDF).

> [!div class="nextstepaction"]
> [Consulta ou a ID de objeto para um usuário](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Obter a ID de objeto para uma entidade de serviço](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Recuperar a ID de objeto para um locatário do AD do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Atribuições de função

As permissões são concedidas a um destinatário criando uma atribuição de função e revogando removendo uma atribuição de função. Uma atribuição de função do Azure Digital Twins associa um objeto (usuário, locatário do Azure AD, etc.), função e um espaço. As permissões são então concedidas a todos os objetos que pertencem a esse espaço, incluindo todo o gráfico espacial abaixo dele.

Por exemplo, um usuário recebe uma atribuição de função com função `DeviceInstaller` para o nó raiz de um gráfico espacial, que representa um edifício. O usuário pode então ler e atualizar dispositivos não apenas para esse nó, mas para todos os outros espaços filhos no edifício.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança de gêmeos Digital do Azure, leia [criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
