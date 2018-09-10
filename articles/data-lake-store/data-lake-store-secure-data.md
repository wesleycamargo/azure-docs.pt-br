---
title: Protegendo os dados armazenados no repositório Azure Data Lake Store | Microsoft Docs
description: Aprenda a proteger os dados no repositório Azure Data Lake usando grupos e listas de controle de acesso
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 0ac6b90f2efc525cfb9767843c741f1e3cfc6de7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448800"
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protegendo os dados armazenados no repositório Azure Data Lake
A proteção de dados no repositório Azure Data Lake é uma abordagem de três etapas.  Controle de acesso baseado em regra (RBAC) e listas de controle de acesso (ACLs) devem ser definidas para possibilitar o acesso completo a dados para usuários e grupos de segurança.

1. Comece criando grupos de segurança no Active Directory do Azure (AAD). Esses grupos de segurança são usados para implementar o RBAC (controle de acesso baseado em função) no portal do Azure. Para saber mais, confira [Controle de Acesso Baseado em Função no Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Atribua os grupos de segurança do AAD à conta do repositório Azure Data Lake. Isso controla o acesso à conta do repositório Data Lake a partir das operações de portal e de gerenciamento do portal ou das APIs.
3. Atribua os grupos de segurança do AAD como listas de controle de acesso (ACLs) no sistema de arquivos do repositório Data Lake.
4. Além disso, você também pode definir um intervalo de endereços IP para clientes que podem acessar os dados no armazenamento do Data Lake.

Este artigo fornece instruções sobre como usar o Portal do Azure para realizar as tarefas acima. Para obter informações detalhadas sobre como Data Lake Store implementa a segurança no nível de conta e dados, consulte [Segurança no Armazenamento do Azure Data Lake](data-lake-store-security-overview.md). Para obter informações detalhadas sobre como ACLs são implementadas no Azure Data Lake Store, consulte [Visão geral do controle de acesso no armazenamento do Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Active Directory do Azure
Para obter instruções sobre como criar grupos de segurança do AAD e como adicionar usuários ao grupo, consulte [Gerenciar grupos de segurança no Active Directory do Azure](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Adicione usuários e outros grupos a um grupo no Azure AD usando o portal do Azure. No entanto, para adicionar uma entidade de serviço a um grupo, use um [módulo do PowerShell do Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Atribua usuários ou grupos de segurança às contas do repositório Azure Data Lake.
Ao atribuir usuários ou grupos de segurança às contas do repositório Azure Data Lake, você controla o acesso às operações de gerenciamento na conta usando o Portal do Azure e as APIs do Gerenciador de Recursos do Azure. 

1. Abra uma conta do repositório Azure Data Lake. No painel esquerdo, clique em **Todos os recursos**, e depois, na folha Todos os recursos, clique no nome da conta sob a qual você deseja criar um usuário ou grupo de segurança.

2. Na folha de configurações da conta Data Lake Store, clique em **Controle de Acesso (IAM)**. Por padrão, a folha lista os proprietários de assinatura como o proprietário.
   
    ![Atribuir um grupo de segurança para a conta de armazenamento do Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "atribuir o grupo de segurança para a conta de armazenamento do Azure Data Lake Store")

3. Na folha do **Controle de Acesso (IAM)**, clique em **Adicionar** para abrir a folha **Adicionar permissões**. Na folha **Adicionar permissões**, selecione um **Função** para o usuário/grupo. Procure o grupo de segurança criado anteriormente no Microsoft Azure Active Directory e selecione-o. Se houver muitos usuários e grupos a sua pesquisa, use a caixa de texto **Selecionar** na parte superior para filtrar pelo nome do grupo. 
   
    ![Adicionar uma função para o usuário](./media/data-lake-store-secure-data/adl.add.user.1.png "adicionar uma função do usuário")
   
    As funções **Proprietário** e **Colaborador** fornecem acesso a várias funções de administração da conta do Data Lake. Para usuários que irão interagir com dados no lago de dados, mas ainda precisa exibir informações de gerenciamento de conta, você pode adicioná-los à função do **Leitor**. O escopo dessas funções é limitado às operações de gerenciamento relacionadas à conta do repositório Azure Data Lake.
   
    Para as operações de dados, as permissões do sistema de arquivos individual definem o que os usuários podem fazer. Portanto, um usuário com uma função de Leitor pode exibir somente as configurações administrativas associadas à conta, mas possivelmente pode ler e gravar dados com base nas permissões de sistema de arquivo atribuídas a ele. As permissões do sistema de arquivos do repositório Data Lake são descritas em [Atribuir grupo de segurança como ACLs ao sistema de arquivos do repositório Azure Data Lake](#filepermissions).

    > [!IMPORTANT]
    > Somente a função do **Proprietário** automaticamente habilita o acesso de sistema de arquivos. O **Colaborador**, **Leitor**, e todas as outras funções exigem ACLs para permitir qualquer nível de acesso a arquivos e pastas.  A função do **Proprietário** fornece arquivo de superusuário e permissões que não podem ser substituídas via ACLs. Para obter mais informações sobre como as políticas RBAC mapeiam o acesso a dados, consulte [RBAC para gerenciamento de conta](data-lake-store-security-overview.md#rbac-for-account-management).

4. Se você deseja adicionar um grupo/usuário não listado na folha **Adicionar permissões**, você pode convidá-los digitando seu endereço de email na caixa de texto **Selecionar**, e em seguida, selecione-os na lista.
   
    ![Adicionar um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "adicionar um grupo de segurança")
   
5. Clique em **Salvar**. O grupo de segurança deve ter sido adicionado, como exibido abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "o grupo de segurança adicionado")

6. Agora, o usuário/grupo de segurança tem acesso à conta do repositório Azure Data Lake. Se você quiser fornecer acesso a usuários específicos, adicione-os ao grupo de segurança. Da mesma forma, se você quiser revogar o acesso de um usuário, remova-o do grupo de segurança. Também é possível atribuir vários grupos de segurança a uma conta. 

## <a name="filepermissions"></a>Atribuir usuários ou grupo de segurança como ACLs ao sistema de arquivos do Azure Data Lake Store
Ao atribuir usuários/grupos de segurança ao sistema de arquivos do Azure Data Lake, defina o controle de acesso nos dados armazenados no repositório Azure Data Lake.

1. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Exibir dados por meio do Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Exibir dados por meio do Data Explorer")
2. Na folha **Data Explorer**, clique na pasta para o qual você deseja configurar a ACL e, em seguida, clique em **Acessar**. Para atribuir as ACLs em um arquivo, primeiro você clique no arquivo para visualizá-lo e, em seguida, clique em **Acesso** da  **folha Exibição de arquivo**.
   
    ![Configurar ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de arquivos do Data Lake")
3. A folha de **Acesso** lista os proprietários e permissões já atribuído para a raiz. Clique no ícone **Adicionar** para adicionar ACLs de Acesso.
    > [!IMPORTANT]
    > Configuração das permissões de acesso para um único arquivo necessariamente não concede um acesso de usuário/grupo a esse arquivo. O caminho para o arquivo deve estar acessível para o usuário/grupo atribuído. Para obter mais informações e exemplos, consulte [Cenários comuns relacionados às permissões](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "lista de acesso padrão e personalizado")
   
   * Os **Proprietários** e **Ouras pessoas** fornecem acesso ao estilo UNIX, no qual você especifica leitura, gravação e execução (rwx) a três classes de usuário distintas: proprietário, grupo e outros.
   * **Permissões atribuídas** correspondem às ACLs POSIX e permite a definição de permissões para usuários ou grupos nomeados específicos, além do proprietário do arquivo ou grupo. 
     
     Para saber mais, consulte [ACLs HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para saber mais sobre como as ACLs são implementadas no Data Lake Store, veja [Controle de acesso no Data Lake Store](data-lake-store-access-control.md).
4. Clique no ícone **Adicionar** para abrir a folha **Atribuir permissões**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e na folha **Selecionar Usuário ou Grupo**, procure o grupo de segurança que você criou anteriormente no Azure Active Directory. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")
5. Clique em **Selecionar Permissões**, selecione as permissões, todas as permissões devem ser aplicadas recursivamente, e sempre que você quiser atribuir as permissões como um ACL de acesso, ACL padrão ou ambos. Clique em **OK**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões no Data Lake Store e ACLs de acesso/padrão, consulte [Controle de acesso no Data Lake Store](data-lake-store-access-control.md).
6. Depois de clicar em **Ok** na folha **Selecionar permissões**, o grupo recém-adicionado e permissões associadas agora estarão listadas na folha **Acesso**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões ao grupo")
   
   > [!IMPORTANT]
   > Na versão atual, você pode ter até 28 entradas em **Permissões atribuídas**. Se você quiser adicionar mais de 28 usuários, crie grupos de segurança, adicione os usuários aos grupos, forneça acesso a esses grupos de segurança para a conta do repositório Data Lake.
   > 
   > 
7. Se for necessário, também é possível modificar as permissões de acesso depois de adicionar o grupo. Marque ou desmarque a caixa de seleção de cada tipo de permissão (Leitura, Gravação, Execução) com base em seu desejo de remover ou atribuir essa permissão ao grupo de segurança. Clique em **Salvar** para salvar as alterações ou em **Descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso a dados
O Repositório Azure Data Lake permite bloquear ainda mais o acesso ao seu repositório de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Uma vez habilitado, somente os clientes com os endereços IP no intervalo definido poderão conectar o repositório.

![Configurações de firewall e acesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Endereço IP e configurações de firewall")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Remova grupos de segurança de uma conta do repositório Azure Data Lake.
Ao remover grupos de segurança das contas do Repositório do Azure Data Lake, você está apenas alterando o acesso às operações de gerenciamento na conta usando o Portal do Azure e as APIs do Gerenciador de Recursos do Azure.  

O acesso a dados é alterado e ainda é gerenciado pelo acesso de ACLs.  A exceção a isso são os usuários/grupos na função Proprietários.  Usuários/grupos não são mais superusuários e seu acesso voltará para acessar configurações de ACL. 

1. Na folha de configurações da conta Data Lake Store, clique em **Controle de Acesso (IAM)**. 
   
    ![Atribuir um grupo de segurança para a conta do Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "atribuir o grupo de segurança para a conta do Azure Data Lake")
2. Na folha **Controle de Acesso (IAM)**, clique no grupo de segurança que você deseja remover. Clique em **Remover**.
   
    ![Removido do grupo de segurança](./media/data-lake-store-secure-data/adl.remove.group.png "removido do grupo de segurança")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Remover ACLs do grupo de segurança do sistema de arquivos do repositório Azure Data Lake
Quando você remove as ACLs de grupos de segurança do sistema de arquivos do Azure Data Lake Store, você altera o acesso aos dados no Data Lake Store.

1. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Criar diretórios na conta do Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta do Data Lake")
2. Na folha **Data Explorer**, clique na pasta para o qual você deseja remover a ACL e, em seguida, clique em **Acessar**. Para remover as ACLs em um arquivo, primeiro você clique no arquivo para visualizá-lo e, em seguida, clique em **Acesso** da  **folha Exibição de arquivo**. 
   
    ![Configurar ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de arquivos do Data Lake")
3. Na folha **Acesso**, clique no grupo de segurança que você deseja remover. Na folha **Acessar detalhes**, clique em **Remover**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões ao grupo")

## <a name="see-also"></a>Consulte também
* [Visão geral do repositório Azure Data Lake](data-lake-store-overview.md)
* [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Repositório Data Lake usando o PowerShell](data-lake-store-get-started-powershell.md)
* [Introdução ao Repositório Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Acessar os logs de diagnóstico do Azure Data Lake Store](data-lake-store-diagnostic-logs.md)

