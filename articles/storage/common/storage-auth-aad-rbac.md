---
title: Use o RBAC para gerenciar os direitos de acesso aos contêineres de Armazenamento do Microsoft Azure e filas (Versão prévia) | Microsoft Docs
description: Use o controle de acesso baseado em função (RBA) para atribuir funções para acesso aos dados do Armazenamento do Microsoft Azure para usuários, grupos, entidades de serviço de aplicativo ou identidades de serviço gerenciado. O Armazenamento do Microsoft Azure oferece suporte a funções internas e personalizadas para direitos de acesso a filas e contêineres.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 9efd9470982f0afaa357114828d51df37a7c2890
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520921"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Gerenciar os direitos de acesso aos dados do Armazenamento do Microsoft Azure com o RBAC (Versão prévia)

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. Quando uma função do RBAC é atribuída a uma identidade do Azure AD, essa identidade recebe acesso a esses recursos, de acordo com o escopo especificado. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Você pode atribuir direitos de acesso para recursos de Armazenamento do Microsoft Azure usando o Portal do Azure, as ferramentas de linha de comando do Azure e as APIs de Gerenciamento do Azure. 

Uma identidade do Azure AD pode ser um usuário, grupo ou entidade de serviço de aplicativo ou pode ser uma *identidade de serviço gerenciada*. Uma entidade de segurança pode ser um usuário, grupo ou entidade de serviço de aplicativo. Uma [identidade de serviço gerenciada](../../active-directory/managed-service-identity/overview.md) é uma identidade automaticamente gerenciada usada para autenticar a partir de aplicativos executados em máquinas virtuais do Azure, aplicativos de função, conjuntos de dimensionamento de máquinas virtuais e outros. Para obter uma visão geral de identidade no Azure AD, consulte [Compreender as soluções de identidade do Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Funções do RBAC para Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure oferece suporte a funções do RBAC internas e personalizadas. O Armazenamento do Microsoft Azure oferece essas funções internas do RBAC para uso com o Azure AD:

- [Colaborador de Dados do Blob de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Leitor de Dados do Blob de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Colaborador de Dados da Fila de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Leitor de Dados da Fila de Armazenamento (Versão prévia)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Você também pode definir funções personalizadas para uso com contêineres e filas. Para obter mais informações, consulte [Criar funções personalizadas para controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Essa visualização destina-se somente a uso de não produção. Os SLAs (contratos de nível de serviço) de produção não estarão disponíveis até que a integração do Microsoft Azure Active Directory para Armazenamento do Microsoft Azure seja declarada geralmente disponível. Se a integração do Microsoft Azure Active Directory ainda não tiver suporte no seu cenário, continue usando os tokens de autorização ou a chave compartilhada nos aplicativos. Para obter informações adicionais sobre a versão prévia, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md).
>
> Durante a visualização, as atribuições de função do RBAC podem levar até cinco minutos para ser propagadas.

## <a name="assign-a-role-to-a-security-principal"></a>Atribuir uma função a uma entidade de segurança

Atribua uma função do RBAC para uma identidade do Azure para conceder permissões para contêineres ou filas na conta de armazenamento. Você pode definir o escopo de atribuição de função para a conta de armazenamento ou para uma fila ou um contêiner específico. A tabela a seguir resume os direitos de acesso concedidos por funções internas, dependendo do escopo: 

|                                 |     Colaborador de dados de blob                                                 |     Leitor de dados de blob                                                |     Colaborador de dados de fila                                  |     Leitor de dados de fila                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Escopo definido para assinatura       |    Acesso de leitura/gravação a todos os contêineres e blobs na assinatura       |    Acesso de leitura a todos os contêineres e blobs na assinatura       |    Acesso de leitura/gravação a todas as filas na assinatura       |    Acesso de leitura a todas as filas na assinatura         |
|    Escopo definido para o grupo de recursos     |    Acesso de leitura/gravação a todos os contêineres e blobs no grupo de recursos     |    Acesso de leitura para todos os contêineres e blobs no grupo de recursos     |    Acesso de leitura/gravação a todas as filas no grupo de recursos     |    Acesso de leitura a todas as filas no grupo de recursos     |
|    Escopo definido para a conta de armazenamento    |    Acesso de leitura/gravação a todos os contêineres e blobs na conta de armazenamento    |    Acesso de leitura a todos os contêineres e blobs na conta de armazenamento    |    Acesso de leitura/gravação a todas as filas na conta de armazenamento    |    Acesso de leitura a todas as filas na conta de armazenamento    |
|    Escopo definido para contêiner/fila    |    Acesso de leitura/gravação ao contêiner especificado e seus blobs              |    Acesso de leitura ao contêiner especificado e seus blobs              |    Acesso de leitura/gravação à fila especificada                  |    Acesso de leitura à fila especificada                    |

> [!NOTE]
> Como um proprietário de sua conta de Armazenamento do Microsoft Azure, permissões não são automaticamente atribuídas a você para acessar dados. Você deve atribuir explicitamente por conta própria uma função do RBAC para Armazenamento do Microsoft Azure. Você pode atribuí-la no nível da sua assinatura, grupo de recursos, conta de armazenamento ou um contêiner ou fila.

Para obter detalhes sobre as permissões necessárias para chamar operações de Armazenamento do Microsoft Azure, consulte [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

As seções a seguir mostram como atribuir uma função com escopo à conta de armazenamento ou com escopo a um contêiner individual.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Atribuir uma função com escopo até a conta de armazenamento no Portal do Azure

Para atribuir uma função interna concedendo acesso a todos os contêineres ou filas na conta de armazenamento no Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
2. Selecione sua conta de armazenamento, em seguida, selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso para a conta. Clique no botão **Adicionar** para adicionar uma nova função.

    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](media/storage-auth-aad-rbac/portal-access-control.png)

3. Na janela **Adicionar permissões**, selecione a função para atribuir a uma identidade do Azure AD. Em seguida, pesquise para localizar a identidade à qual você deseja atribuir essa função. Por exemplo, a imagem a seguir mostra a função **Leitor de Dados do Blob de Armazenamento (Versão prévia)** atribuída a um usuário.

    ![Captura de tela que mostra como atribuir uma função do RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários adicionados agora têm permissões de leitura para todos os dados de blob na conta de armazenamento.

    ![Captura de tela que mostra a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Atribuir uma função com escopo a um contêiner ou fila no Portal do Azure

As etapas para atribuir uma função interna com escopo a um contêiner ou a uma fila são semelhantes. O procedimento mostrado aqui atribui uma função com escopo a um contêiner, mas você pode seguir as mesmas etapas para atribuir uma função com escopo a uma fila: 

1. No [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento e exiba a **Visão geral** para a conta.
2. Em Serviço Blob, selecione **Procurar blobs**. 
3. Localize o contêiner para o qual você deseja atribuir uma função e exiba as configurações do contêiner. 
4. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso do contêiner.
5. Na janela **Adicionar permissões**, selecione a função que deseja atribuir a uma identidade do Azure AD. Em seguida, pesquise para localizar a identidade à qual você deseja atribuir essa função.
6. Clique em **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que o usuário adicionado agora tem permissões de leitura para os dados no contêiner nomeado *sample-container*.

    ![Captura de tela que mostra a lista de usuários atribuídos a uma função](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o RBAC, consulte [Introdução ao Controle de Acesso Baseado em Função](../../role-based-access-control/overview.md).
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../../role-based-access-control/role-assignments-rest.md)
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Usar o Azure AD com aplicativos do Armazenamento do Microsoft Azure](storage-auth-aad-app.md).
- Para obter informações adicionais sobre a integração do Azure AD para contêineres e filas do Azure, consulte a postagem no blog da equipe do Armazenamento do Microsoft Azure, [Anunciando a versão prévia de autenticação do Azure AD para Armazenamento do Microsoft Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 