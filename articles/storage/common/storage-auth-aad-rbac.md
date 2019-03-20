---
title: Usar o portal do Azure para gerenciar direitos de acesso do AD do Azure para contêineres e filas com o RBAC (visualização) – armazenamento do Azure | Microsoft Docs
description: Use o controle de acesso baseado em função (RBAC) do portal do Azure para atribuir acesso aos contêineres e filas para entidades de segurança. O armazenamento do Azure dá suporte a funções RBAC internas e personalizadas para a autenticação por meio do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/01/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8050027bac3d2f13ac457edcdad9275c040305da
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431849"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>Conceder acesso a contêineres do Azure e filas com o RBAC no portal do Azure (visualização)

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, grupo, uma entidade de serviço de aplicativo, ou um [identidade de recursos do Azure gerenciado](../../active-directory/managed-identities-azure-resources/overview.md).

Este artigo descreve como usar o portal do Azure para atribuir funções RBAC. O portal do Azure fornece uma interface simples para atribuir funções RBAC e gerenciando o acesso a seus recursos de armazenamento. Você também pode atribuir funções RBAC para recursos de blob e fila usando ferramentas de linha de comando do Azure ou as APIs de gerenciamento de armazenamento do Azure. Para obter mais informações sobre as funções RBAC para recursos de armazenamento, consulte [autenticar o acesso ao Azure blobs e filas usando o Azure Active Directory (visualização)](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar o escopo do recurso 

Antes de atribuir uma função RBAC para uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder o escopo mais restrito possível.

A lista a seguir descreve os níveis em que você pode definir o escopo de acesso aos recursos de blob e fila do Azure, começando com o escopo mais restrito:

- **Um contêiner individual.** A este escopo, uma entidade de segurança tem acesso a todos os blobs no contêiner, bem como propriedades do contêiner e metadados.
- **Uma fila individual.** A este escopo, uma entidade de segurança tem acesso às mensagens na fila, bem como propriedades da fila e metadados.
- **A conta de armazenamento.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres e seus blobs, ou para todas as filas e suas mensagens.
- **O grupo de recursos.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** A este escopo, uma entidade de segurança tem acesso a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.

Depois de determinar o escopo desejado para uma atribuição de função, navegue até o recurso apropriado no portal do Azure. Exibição de **controle de acesso (IAM)** configurações para o recurso e siga as instruções nas seções subsequentes para gerenciar atribuições de função.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções de RBAC no portal do Azure

Conceder acesso aos recursos de blob e fila com credenciais do Azure AD no portal do Azure envolve as seguintes etapas: 

1. Atribua a função apropriada de RBAC do armazenamento do Azure para conceder acesso a filas ou contêineres. Acesso de leitura, atribua o **leitor de dados de Blob (versão prévia)** ou **dados de Queue Reader (versão prévia)** função. Para acesso de leitura, gravação e delete, atribuir a **Colaborador de dados de Blob (versão prévia)** ou **colaborador da fila de dados (visualização)** função. Você também pode atribuir uma função personalizada.

1. Atribuir o Azure Resource Manager [leitor](../../role-based-access-control/built-in-roles.md#reader) função aos usuários que precisam acessar contêineres ou filas por meio do portal do Azure usando suas credenciais do AD do Azure. 

As seções a seguir descrevem cada uma dessas etapas mais detalhadamente.

### <a name="assign-a-built-in-rbac-role"></a>Atribuir uma função RBAC interna

Antes de você atribuir uma função a uma entidade de segurança, certifique-se de considerar o escopo das permissões que você está concedendo. Examine os [determinar o escopo do recurso](#determine-resource-scope) seção para decidir o escopo apropriado.

O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila: 

1. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento e exiba a **Visão geral** para a conta.
1. Em Serviços, selecione **Blobs**. 
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner. 
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso do contêiner](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. No **Adicionar atribuição de função** janela, selecione a função de armazenamento do Azure que você deseja atribuir. Pesquise para localizar a entidade de segurança ao qual você deseja atribuir essa função.

    ![Captura de tela que mostra como atribuir uma função do RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    ![Captura de tela mostrando a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac/container-scoped-role.png)

Você pode seguir etapas semelhantes para atribuir uma função no escopo para a conta de armazenamento, grupo de recursos ou assinatura.

> [!NOTE]
> Como proprietário da conta do Armazenamento do Microsoft Azure, você não recebe permissões automaticamente para acessar dados. Você deve atribuir explicitamente por conta própria uma função do RBAC para Armazenamento do Microsoft Azure. Você pode atribuí-la no nível da sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.
> 
> Você não pode atribuir uma função no escopo para uma fila ou um contêiner, se sua conta de armazenamento tem um namespace hierárquico habilitado.

### <a name="assign-the-reader-role-for-portal-access"></a>Atribuir a função de leitor para o acesso ao portal

Quando você atribui uma função interna ou personalizada para o armazenamento do Azure para uma entidade de segurança, você está concedendo permissões a essa entidade de segurança para executar operações nos dados em sua conta de armazenamento. Interna **leitor de dados** as funções fornecem as permissões de leitura para os dados em um contêiner ou fila, enquanto internos na **Colaborador de Data** as funções fornecem ler, gravar e excluir permissões para um contêiner ou fila. As permissões estão no escopo para o recurso especificado.  

Por exemplo, se você atribuir a **Colaborador de dados de Blob de armazenamento (versão prévia)** função de usuário de Mary no nível de um contêiner denominado **contêiner de exemplo**, em seguida, Mary tem permissões de leitura, gravar e excluir o acesso a todos os os blobs nesse contêiner.

No entanto, se Mary deseja exibir um blob no portal do Azure, em seguida, a **Colaborador de dados de Blob de armazenamento (visualização)** função por si só não fornecerá permissões suficientes para navegar por meio do portal para o blob para exibi-lo. Adicionais são necessárias permissões de AD do Azure para navegar por meio do portal e exibir os outros recursos que estão visíveis lá.

Se os usuários precisarem ser capaz de acessar os blobs no portal do Azure e, em seguida, atribuí-los uma função RBAC adicional, o [leitor](../../role-based-access-control/built-in-roles.md#reader) função aos usuários, no nível da conta de armazenamento ou superior. O **leitor** função é uma função do Azure Resource Manager que permite aos usuários exibir recursos da conta de armazenamento, mas não modificá-los. Ele não fornece permissões de leitura aos dados no armazenamento do Azure, mas somente para os recursos de gerenciamento de conta.

Siga estas etapas para atribuir a **leitor** função para que um usuário possa acessar os blobs do portal do Azure. Neste exemplo, a atribuição é abrangida à conta de armazenamento:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **controle de acesso (IAM)** para exibir as configurações de controle de acesso da conta de armazenamento. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. No **Adicionar atribuição de função** janela, selecione a **leitor** função. 
1. Dos **atribuir acesso a** lista suspensa, selecione **usuário, grupo ou entidade de serviço do Azure AD**.
1. Pesquisar para localizar a entidade de segurança ao qual você deseja atribuir a função.
1. Salve a atribuição de função.

> [!NOTE]
> Atribuir a função de leitor é necessário apenas para usuários que precisam acessar os blobs ou filas usando o portal do Azure. 

## <a name="use-azure-ad-credentials-with-the-portal"></a>Usar credenciais do Azure AD com o portal

Para acessar blobs ou filas no portal do Azure usando suas credenciais do Azure AD, use os links de visualização, mostrados na imagem a seguir:

![Acessar blobs ou filas com credenciais do Azure AD no portal](media/storage-auth-aad-rbac/access-data-azure-ad.png)

Se você acessar dados blob ou fila usando os links de produção em vez dos links de visualização, o portal do Azure usa sua chave de conta para autorizar o acesso, em vez de usar o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o RBAC, consulte [O que é o Controle de Acesso Baseado em Função (RBAC)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
- Para obter informações adicionais sobre a integração do Azure AD para contêineres e filas do Azure, consulte a postagem no blog da equipe do Armazenamento do Microsoft Azure, [Anunciando a versão prévia de autenticação do Azure AD para Armazenamento do Microsoft Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
