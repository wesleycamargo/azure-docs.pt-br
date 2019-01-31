---
title: Use o RBAC para gerenciar direitos de acesso a contêineres e filas (visualização) - Armazenamento do Azure | Microsoft Docs
description: Use o controle de acesso baseado em função (RBAC) para atribuir funções de acesso a dados de blob e fila a usuários, grupos, entidades de serviços de aplicativos ou identidades de serviços gerenciados. O Armazenamento do Microsoft Azure oferece suporte a funções internas e personalizadas para direitos de acesso a filas e contêineres.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6cecb2d5fde4fc651cf0ba971189ef62fa330c13
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241695"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>Gerenciamento de direitos de acesso para BLOBs do Azure e os dados da fila com o RBAC (versão prévia)

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. Quando uma função do RBAC é atribuída a uma identidade do Azure AD, essa identidade recebe acesso a esses recursos, de acordo com o escopo especificado. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Você pode atribuir direitos de acesso para recursos de Armazenamento do Microsoft Azure usando o Portal do Azure, as ferramentas de linha de comando do Azure e as APIs de Gerenciamento do Azure. 

Uma identidade do Azure AD pode ser um usuário, grupo ou entidade de serviço de aplicativo ou pode ser uma identidade gerenciada para recursos do Azure. Uma entidade de segurança pode ser um usuário, grupo ou entidade de serviço de aplicativo. Uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) é uma identidade gerenciada automaticamente usada para autenticar de aplicativos em execução em máquinas virtuais do Azure, aplicativos de função, conjuntos de escala de máquinas virtuais e outros. Para obter uma visão geral de identidade no Azure AD, consulte [Compreender as soluções de identidade do Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

O Armazenamento do Microsoft Azure oferece suporte a funções do RBAC internas e personalizadas. O Armazenamento do Microsoft Azure oferece essas funções internas do RBAC para uso com o Azure AD:

- [Proprietário de Dados do Blob de Armazenamento (Versão Prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview): Use para definir a propriedade e gerenciar o controle de acesso do POSIX para armazenamento do Azure Data Lake Storage Gen2 (visualização). Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de blob de armazenamento (pré-visualização)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de Dados do Blob de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Colaborador de Dados da Fila de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de Dados da Fila de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview): Use para conceder permissões somente leitura para as filas do Azure.

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Você também pode definir funções personalizadas para uso com contêineres e filas. Para obter mais informações, consulte [Criar funções personalizadas para controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Atribuir uma função a uma entidade de segurança

Atribua uma função do RBAC para uma identidade do Azure para conceder permissões para contêineres ou filas na conta de armazenamento. Você pode definir o escopo de atribuição de função para a conta de armazenamento ou para uma fila ou um contêiner específico. A tabela a seguir resume os direitos de acesso concedidos por funções internas, dependendo do escopo:

|Escopo|Proprietário de Dados do Blob de Armazenamento|Colaborador de dados de blob|Leitor de dados de blob|Colaborador de dados de fila|Leitor de dados de fila|
|---|---|---|---|---|---|
|Nível de assinatura|Gerenciamento de controle para todos os contêineres e blobs na assinatura de acesso de acesso de leitura/gravação e POSIX|Acesso de leitura/gravação a todos os contêineres e blobs na assinatura| Acesso de leitura a todos os contêineres e blobs na assinatura|Acesso de leitura/gravação a todas as filas na assinatura|Acesso de leitura a todas as filas na assinatura|
|Nível do grupo de recursos|Acesso de leitura/gravação e POSIX para todos os contêineres e blobs no grupo de recursos de gerenciamento do controle de acesso|Acesso de leitura/gravação a todos os contêineres e blobs no grupo de recursos|Acesso de leitura para todos os contêineres e blobs no grupo de recursos|Acesso de leitura/gravação a todas as filas no grupo de recursos|Acesso de leitura a todas as filas no grupo de recursos|
|Nível da conta de armazenamento|Gerenciamento de controle de acesso à leitura/gravação e POSIX para todos os contêineres e blobs na conta de armazenamento|Acesso de leitura/gravação a todos os contêineres e blobs na conta de armazenamento|Acesso de leitura a todos os contêineres e blobs na conta de armazenamento|Acesso de leitura/gravação a todas as filas na conta de armazenamento|Acesso de leitura a todas as filas na conta de armazenamento|
|Nível de contêiner/fila|Gerenciamento de controle de acesso à leitura/gravação e POSIX no contêiner especificado e seus blobs.|Acesso de leitura/gravação ao contêiner especificado e seus blobs|Acesso de leitura ao contêiner especificado e seus blobs|Acesso de leitura/gravação à fila especificada|Acesso de leitura à fila especificada|

> [!NOTE]
> Como um proprietário de sua conta de Armazenamento do Microsoft Azure, permissões não são automaticamente atribuídas a você para acessar dados. Você deve atribuir explicitamente por conta própria uma função do RBAC para Armazenamento do Microsoft Azure. Você pode atribuí-la no nível da sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.

Para obter detalhes sobre as permissões necessárias para chamar operações de Armazenamento do Microsoft Azure, consulte [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

As seções a seguir mostram como atribuir uma função com escopo à conta de armazenamento ou com escopo a um contêiner individual.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Atribuir uma função com escopo até a conta de armazenamento no Portal do Azure

Para atribuir uma função interna concedendo acesso a todos os contêineres ou filas na conta de armazenamento no Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione sua conta de armazenamento, em seguida, selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso para a conta. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](media/storage-auth-aad-rbac/portal-access-control.png)

1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função**, selecione a função para atribuir a uma identidade do Azure AD. Em seguida, pesquise para localizar a identidade à qual você deseja atribuir essa função. Por exemplo, a imagem a seguir mostra a função **Leitor de Dados do Blob de Armazenamento (Versão prévia)** atribuída a um usuário.

    ![Captura de tela que mostra como atribuir uma função do RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários adicionados agora têm permissões de leitura para todos os dados de blob na conta de armazenamento.

    ![Captura de tela que mostra a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Atribuir uma função com escopo a um contêiner ou fila no Portal do Azure

> [!IMPORTANT]
> Você não pode fazer isso se estiver usando uma conta com o namespace hierárquico, ainda habilitado.

As etapas para atribuir uma função interna com escopo a um contêiner ou a uma fila são semelhantes. O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila: 

1. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento e exiba a **Visão geral** para a conta.
1. Em Serviços, selecione **Blobs**. 
1. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner. 
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso do contêiner](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. Clique no botão **Adicionar atribuição de função** para adicionar uma nova função.
1. Na janela **Adicionar atribuição de função**, selecione a função que você deseja atribuir a uma identidade do Azure AD. Em seguida, pesquise para localizar a identidade à qual você deseja atribuir essa função.
1. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    ![Captura de tela que mostra a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o RBAC, consulte [O que é o Controle de Acesso Baseado em Função (RBAC)?](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
- Para obter informações adicionais sobre a integração do Azure AD para contêineres e filas do Azure, consulte a postagem no blog da equipe do Armazenamento do Microsoft Azure, [Anunciando a versão prévia de autenticação do Azure AD para Armazenamento do Microsoft Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
