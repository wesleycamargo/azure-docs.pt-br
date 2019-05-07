---
title: Usar o portal do Azure para acessar o blob ou fila dados - armazenamento do Azure
description: Quando você acessa o blob ou dados da fila usando o portal do Azure, o portal faz solicitações de armazenamento do Azure nos bastidores. Essas solicitações para o armazenamento do Azure podem ser autenticadas e autorizados usando sua conta do AD do Azure ou a chave de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154001"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Use o portal do Azure para acessar os dados blob ou fila

Quando você acessa o blob ou fila de dados usando o [portal do Azure](https://portal.azure.com), o portal faz solicitações de armazenamento do Azure nos bastidores. Essas solicitações para o armazenamento do Azure podem ser autenticadas e autorizados usando sua conta do AD do Azure ou a chave de acesso da conta de armazenamento. O portal indica qual método de autenticação que você está usando e permite que você alterne entre os dois, se você tiver as permissões apropriadas.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Permissões necessárias para acessar os dados de blob ou fila

Dependendo de como você deseja autenticar o acesso a dados blob ou fila no portal do Azure, você precisará de permissões específicas. Na maioria dos casos, essas permissões são fornecidas por meio do controle de acesso baseado em função (RBAC). Para obter mais informações sobre o RBAC, consulte [o que é controle de acesso baseado em função (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Chave de acesso da conta

Para acessar dados de blob e fila com a chave de acesso da conta, você deve ter uma função RBAC atribuída a você que inclui a ação de RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Essa função RBAC pode ser um interno ou uma função personalizada. Funções internas que suportam **Microsoft.Storage/storageAccounts/listkeys/action** incluem:

- O Azure Resource Manager [proprietário](../../role-based-access-control/built-in-roles.md#owner) função
- O Azure Resource Manager [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) função
- O [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) função

Quando você tenta acessar dados blob ou fila no portal do Azure, o portal verifica primeiro se você atribuiu uma função com **Microsoft.Storage/storageAccounts/listkeys/action**. Se você tiver sido atribuído a uma função com esta ação, o portal usa a chave de conta para acessar os dados de blob e fila. Se você não tiver sido atribuído uma função com esta ação, o portal tenta acessar dados usando sua conta do AD do Azure.

> [!NOTE]
> As funções de administrador de assinatura clássicos administrador de serviços e co-administrador incluem o equivalente do Azure Resource Manager [proprietário](../../role-based-access-control/built-in-roles.md#owner) função. O **proprietário** função inclui todas as ações, incluindo o **Microsoft.Storage/storageAccounts/listkeys/action**, portanto, um usuário com uma dessas funções administrativas também pode acessar dados de blob e fila com o chave da conta. Para obter mais informações, consulte [funções de administrador de assinatura clássico](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Conta do AD do Azure

Para acessar dados de blob ou fila do portal do Azure usando sua conta do Azure AD, ambas as instruções a seguir devem ser verdadeiras para você:

- Você foi atribuído o Azure Resource Manager [leitor](../../role-based-access-control/built-in-roles.md#reader) função, no mínimo, com escopo definido para o nível da conta de armazenamento ou superior. O **leitor** função concede as permissões mais restritas, mas outra função do Azure Resource Manager que concede acesso a recursos de gerenciamento de conta de armazenamento também é aceitável.
- Você foram atribuídos a uma função interna ou personalizada que fornece acesso aos dados de blob ou fila.

O **leitor** atribuição de função ou outra atribuição de função do Azure Resource Manager é necessária para que o usuário pode exibir e navegar recursos de gerenciamento de conta de armazenamento no portal do Azure. As funções RBAC que concedem acesso a dados de blob ou fila não concede acesso a recursos de gerenciamento de conta de armazenamento. Para acessar dados de blob ou fila no portal, o usuário precisa de permissões para navegar por recursos de conta de armazenamento. Para obter mais informações sobre esse requisito, consulte [atribuir a função de leitor para o acesso ao portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

As funções internas que oferecem suporte ao acesso aos seus dados de blob ou fila incluem:

- [Proprietário de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Para o controle de acesso POSIX para armazenamento do Azure Data Lake Gen2.
- [Colaborador de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Permissões de leitura/gravação/exclusão de blobs.
- [Leitor de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Permissões de somente leitura para blobs.
- [Colaborador de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Permissões de leitura/gravação/exclusão de filas.
- [Leitor de dados da fila de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Permissões de somente leitura para filas.
    
Funções personalizadas podem dar suporte a diferentes combinações das mesmas permissões fornecidas por funções internas. Para obter mais informações sobre como criar funções de RBAC personalizadas, consulte [funções personalizadas para recursos do Azure](../../role-based-access-control/custom-roles.md) e [entender as definições de função para recursos do Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Não há suporte para a listagem de filas com uma função de administrador de assinatura clássicos. Para listar as filas, um usuário deve ter atribuído a eles, o Azure Resource Manager **Reader** função, o **leitor de dados do armazenamento de fila** função, ou o **Colaborador de dados da fila de armazenamento** função.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navegue para blobs ou filas no portal

Para exibir dados de blob ou fila no portal, navegue até a **visão geral** para sua conta de armazenamento e clique nos links para **Blobs** ou **filas**. Como alternativa, você pode navegar para o **serviço Blob** e **serviço de fila** seções no menu. 

![Navegue até os dados blob ou fila no portal do Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Determinar o método de autenticação atual

Quando você navega para uma fila ou um contêiner, o portal do Azure indica se você está atualmente usando a chave de acesso de conta ou sua conta do Azure AD para autenticar.

Os exemplos nesta seção mostram acessando um contêiner e seus blobs, mas o portal exibe a mesma mensagem quando você estiver acessando suas mensagens e uma fila ou listar filas.

### <a name="account-access-key"></a>Chave de acesso da conta

Se você estiver autenticando usando a chave de acesso da conta, você verá **chave de acesso** especificado como o método de autenticação no portal:

![No momento, acessando dados do contêiner com a chave de conta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Para alternar para usar a conta do Azure AD, clique no link realçado na imagem. Se você tiver as permissões apropriadas por meio de funções RBAC que são atribuídas a você, você poderá prosseguir. No entanto, se você não tem as permissões corretas, você verá uma mensagem de erro semelhante à seguinte:

![Erro mostrado se a conta do Azure AD não dá suporte ao acesso](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Observe que nenhum BLOB aparecem na lista, se sua conta do Azure AD não tem permissões para exibi-los. Clique no **alternar para a chave de acesso** link para usar a chave de acesso para autenticação novamente.

### <a name="azure-ad-account"></a>Conta do AD do Azure

Se você estiver autenticando usando sua conta do Azure AD, você verá **conta de usuário do Azure AD** especificado como o método de autenticação no portal:

![Acessando dados do contêiner com a conta do Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Para alternar para usar a chave de acesso da conta, clique no link realçado na imagem. Se você tiver acesso à chave de conta, em seguida, você poderá continuar. No entanto, se você não tem acesso à chave de conta, você verá uma mensagem de erro semelhante à seguinte:

![Erro mostrado se você não tem acesso à chave de conta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Observe que nenhum BLOB aparecem na lista, se você não tiver acesso às chaves de conta. Clique no **alternar para a conta de usuário do Azure AD** link para usar sua conta do Azure AD para autenticação novamente.

## <a name="next-steps"></a>Próximas etapas

- [Autenticar o acesso a blobs do Azure e filas usando o Azure Active Directory](storage-auth-aad.md)
- [Conceder acesso a contêineres do Azure e filas com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC, usando o PowerShell](storage-auth-aad-rbac-powershell.md)
