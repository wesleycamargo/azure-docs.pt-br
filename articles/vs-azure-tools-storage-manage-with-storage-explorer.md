---
title: "Introdução ao Gerenciador de armazenamento (visualização) | Microsoft Docs"
description: "Gerenciar os recursos de armazenamento do Azure com o Gerenciador de Armazenamento (Visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 27b3775d81ec6dc093dae4ee46167c5d5a9c9e19
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Introdução ao Gerenciador de armazenamento (visualização)
## <a name="overview"></a>Visão geral
O Gerenciador de armazenamento do Azure (visualização) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende diversas maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador de Armazenamento do Microsoft Azure (Preview)][0]

## <a name="prerequisites"></a>pré-requisitos
* [Baixe e instale o Gerenciador de armazenamento (visualização)](http://www.storageexplorer.com)

> [!NOTE]
> Para distribuições Linux diferentes do Ubuntu 16.04, talvez seja necessário instalar algumas dependências manualmente. Em geral, são necessários os seguintes pacotes:
> * libgconf-2-4
> * libsecret
> * GCC atualizado
>
> Dependendo da sua distribuição, talvez seja necessário instalar outros pacotes. O Gerenciador de Armazenamento[Notas de Versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) contém etapas específicas para algumas distribuições.
>
>

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço
O Gerenciador de armazenamento (visualização) fornece várias maneiras de se conectar às contas de armazenamento. Por exemplo, você pode:
* Conecte-se às contas de armazenamento associadas às suas assinaturas do Azure.
* Conecte-se às contas de armazenamento e serviços compartilhados a partir de outras assinaturas do Azure.
* Conecte-se e gerencie o armazenamento local usando o Emulador de armazenamento do Azure. 

Além disso, você pode trabalhar com contas nacionais e internacionais de armazenamento no Azure:

* [Como conectar-se a uma assinatura do Azure](#connect-to-an-azure-subscription) - gerencie os recursos de armazenamento que pertencem à sua assinatura do Azure.
* [Como trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) - gerencie o armazenamento local usando o Emulador de armazenamento do Azure.
* [Como anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) - Gerencie os recursos de armazenamento que pertencem a outra assinatura do Azure ou que estão sob nuvens nacionais do Azure usando o nome de conta de armazenamento, a chave e os pontos de extremidade.
* [Como anexar uma conta de armazenamento usando uma SAS](#attach-storage-account-using-sas): gerencie recursos de armazenamento que pertencem a outra assinatura do Azure usando uma assinatura de acesso compartilhado (SAS).
* [Como anexar um serviço usando uma SAS](#attach-service-using-sas): gerenciar um serviço de armazenamento específico (contêiner de blobs, fila ou tabela) que pertence a outra assinatura do Azure usando uma SAS.
* [Conectar-se a uma conta do Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Gerencie a conta do Cosmos DB usando uma cadeia de conexão.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure
> [!NOTE]
> Se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Gerenciador de Armazenamento (versão prévia), selecione **Gerenciar Contas** para acessar o **Painel de Gerenciamento de Conta**.

    ![Gerenciar Contas][1]

2. Agora o painel à esquerda exibe todas as contas do Azure em que você se conectou. Para conectar-se a outra conta, selecione **Adicionar uma conta**

3. Se você quiser entrar em uma nuvem nacional ou no Azure Stack, clique na lista suspensa **Ambiente do Azure** para selecionar qual nuvem do Azure você deseja usar. Depois de escolher o ambiente, clique no botão **Entrar...**. Se você estiver entrando no Azure Stack, consulte [Conectar o Gerenciador de Armazenamento a uma assinatura do Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md) para obter mais informações.

    ![Opção de conexão][2]

3. Depois de entrar com êxito usando uma conta do Azure, a conta e as assinaturas do Azure associadas com essa conta serão adicionadas ao painel à esquerda. Selecione as assinaturas do Azure com as quais você deseja trabalhar e, em seguida, selecione **Aplicar** (selecionar **Todas as assinaturas:** alterna entre a seleção de todas ou de nenhuma das assinaturas do Azure listadas).

    ![Selecionar assinaturas do Azure][3]

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

## <a name="work-with-local-development-storage"></a>Trabalhar com o armazenamento de desenvolvimento local
O Gerenciador de armazenamento (visualização) permite que você trabalhe no armazenamento local usando o Emulador de Armazenamento do Azure. Essa abordagem permite que você simule o trabalho com o Armazenamento do Azure sem, necessariamente, ter uma conta de armazenamento implantada no Azure, uma vez que a conta de armazenamento está sendo emulada pelo Emulador de Armazenamento do Azure.

> [!NOTE]
> No momento, o Emulador de Armazenamento do Azure tem suporte somente para Windows.
>
>

> [!NOTE]
> O Emulador de Armazenamento do Azure não é compatível com Compartilhamentos de Arquivos.
>
>

1. No painel esquerdo do Gerenciador de Armazenamento (versão prévia), expanda o nó **(Local e Anexado)** > **Contas de Armazenamento** > **(Desenvolvimento)** > **Contêineres de Blob**.

    ![Nó de desenvolvimento local][5]

2. Se ainda não instalou o Emulador de Armazenamento do Azure, você receberá uma solicitação para fazer isso por meio de uma barra de informações. Se a barra de informações for exibida, escolha **Baixar a versão mais recente** e instale o emulador.

    ![Baixar o prompt do Emulador de Armazenamento do Azure][6]

3. Depois que o emulador está instalado, você pode criar e trabalhar com blobs, filas e tabelas locais. Para saber como trabalhar com cada tipo de conta de armazenamento, consulte os guias a seguir:

    * [Gerenciar recursos de Armazenamento de Blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
    * Gerenciar recursos de armazenamento de compartilhamento de arquivos do Azure: *em breve*
    * Gerenciar recursos de armazenamento de filas do Azure: *em breve*
    * Gerenciar recursos de armazenamento de tabelas do Azure: *em breve*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexão ou desconexão de uma conta de armazenamento externo
O Gerenciador de armazenamento (visualização) permite anexar contas de armazenamento externas para facilitar o compartilhamento das contas de armazenamento. Esta seção explica como anexar e desanexar contas do armazenamento externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta de armazenamento
Para compartilhar uma conta de armazenamento externo, o proprietário dessa conta precisa primeiro obter as credenciais (nome e chave da conta), para depois compartilhar essas informações com a pessoa que deseja ser anexada a essa conta. Você pode obter as credenciais da conta de armazenamento por meio do Portal do Azure fazendo as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Procurar**.

3. Selecione **Contas de Armazenamento**.

4. Na lista de **Contas de Armazenamento**, selecione a conta de armazenamento desejada.

5. Em **Configurações**, selecione **Chaves de acesso**.

    ![Opção Chaves de Acesso][7]

6. Copie o **Nome da conta de armazenamento** e **key1**.

    ![Chaves de acesso][8]

### <a name="attach-to-an-external-storage-account"></a>Anexação a uma conta de armazenamento externo
Para anexar a uma conta de armazenamento externo, você precisará do nome da conta e da chave. A seção "Como obter as credenciais de conta de armazenamento" explica como obter esses valores no portal do Azure. No entanto, no portal, a chave de conta é chamada **key1**. Então, quando o Gerenciador de Armazenamento (versão prévia) solicitar uma chave de conta, digite o valor de **key1**.

1. No Gerenciador de Armazenamento (versão prévia), abra a **Caixa de Diálogo Conectar**.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. NA **Caixa de Diálogo Conectar**, escolha **Usar um nome e uma chave da conta de armazenamento**

    ![Adicionar com o nome e a opção de chave][10]

3. Cole o nome da conta na caixa de texto **Nome da conta** e, em seguida, cole a chave de conta (o valor de **key1** do Portal do Azure) na caixa de texto **Chave de conta** e, depois, selecione **Avançar**.

    ![Página de nome e chave][11]

    > [!NOTE]
    > Para usar um nome e uma chave de uma nuvem nacional, use a lista suspensa **Domínio dos pontos de extremidade do armazenamento:** para selecionar o domínio adequado dos pontos de extremidade: 
    >
    >

4. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Se você quiser alterar alguma coisa, selecione **Voltar** e insira novamente as configurações desejadas. 

5. Selecione **Conectar**.

6. Depois que a conta de armazenamento tiver sido anexada com êxito, ela será exibida com o termo **(Externa)** anexado ao nome.

    ![Resultado da conexão a uma conta de armazenamento externo][12]

### <a name="detach-from-an-external-storage-account"></a>Desconexão de uma conta de armazenamento externo
1. Clique o botão direito do mouse na conta de armazenamento externo que você deseja desanexar e, em seguida, selecione **Desanexar**.

    ![Opção Desanexar do armazenamento][13]

2. Na mensagem de confirmação, escolha **Sim** para confirmar a desconexão da conta de armazenamento externo.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Anexar uma conta de armazenamento usando uma SAS (Assinatura de Acesso Compartilhado)
Uma Assinatura de Acesso Compartilhado ou [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), permite que o administrador de uma assinatura do Azure conceda acesso temporário para uma conta de armazenamento sem ter que fornecer credenciais de assinatura do Azure.

Para ilustrar isso, vamos supor que o UsuárioA é um administrador de uma assinatura do Azure, e o UsuárioA deseja permitir que o UsuárioB acesse uma conta de armazenamento por um período limitado com determinadas permissões:

1. O UsuárioA gera uma cadeia de conexão de SAS por um período de tempo específico, com as permissões desejadas.

2. O UsuárioA compartilha a SAS com a pessoa (neste exemplo, o UsuárioB) que deseja acessar a conta de armazenamento.  

3. O UsuárioB usa o Gerenciador de armazenamento (visualização) para se anexar à conta pertencente ao UsuárioA usando a SAS fornecida.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Gerar uma cadeia de conexão de SAS para a conta que você deseja compartilhar
1. No Gerenciador de Armazenamento (versão prévia), clique o botão direito do mouse na conta de armazenamento que você deseja compartilhar e, então, escolha **Obter Assinatura de Acesso Compartilhado...**.

    ![Opção do menu de contexto Obter SAS][14]

2. Na caixa de diálogo **Gerar Assinatura de Acesso Compartilhado**, especifique o período e as permissões que você deseja para a conta e, em seguida, clique no botão **Criar**.

    ![Caixa de diálogo Obter SAS][15]  

3. Ao lado da caixa de texto **Cadeia de Conexão**, selecione **Copiar** para copiá-la na área de transferência e, em seguida, clique em **Fechar**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Anexar a uma conta de armazenamento usando uma Cadeia de Conexão de SAS
1. No Gerenciador de Armazenamento (versão prévia), abra a **Caixa de Diálogo Conectar**.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Na **Caixa de Diálogo Conectar**, escolha **Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado** e, em seguida, clique em **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][16]

3. Escolha **Usar uma cadeia de conexão** e cole a cadeia de conexão no campo **Cadeia de conexão:**. Clique no botão **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][17]

4. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Para fazer alterações, selecione **Retornar** e, em seguida, insira as configurações desejadas. 

5. Selecione **Conectar**.

6. Depois que a conta de armazenamento tiver sido anexada com êxito, ela será exibida com o termo **(SAS)** anexado ao nome.

    ![O resultado da anexação a uma conta usando a SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Anexar um serviço usando uma SAS (Assinatura de Acesso Compartilhado)
A seção "Anexar uma conta de armazenamento usando uma SAS" ilustra como um administrador de assinatura do Azure pode conceder acesso temporário a uma conta de armazenamento gerando e compartilhando uma SAS para a conta de armazenamento. Da mesma forma, uma SAS pode ser gerada para um serviço específico (contêiner de blobs, fila, tabela ou compartilhamento de arquivo) em uma conta de armazenamento.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Gerar uma SAS para o serviço que você deseja compartilhar
Nesse contexto, um serviço pode ser um contêiner de blobs, uma fila, uma tabela ou um compartilhamento de arquivo. Para gerar o SAS para um serviço listado, confira:

* [Obter a SAS para um contêiner de blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obter a SAS para um compartilhamento de arquivos: *em breve*
* Obter a SAS para uma fila: *em breve*
* Obter a SAS para uma tabela: *em breve*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Anexar ao serviço de conta compartilhada usando um URI de SAS
1. No Gerenciador de Armazenamento (versão prévia), abra a **Caixa de Diálogo Conectar**.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Na **Caixa de Diálogo Conectar**, escolha **Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado** e, em seguida, clique em **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][16]

3. Escolha **Usar um URI de SAS** e cole o URI no campo **URI:**. Clique no botão **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][19]

3. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Para fazer alterações, selecione **Retornar** e, em seguida, insira as configurações desejadas. 

4. Selecione **Conectar**.

5. Depois que o serviço é anexado com êxito, ele será exibido sob o nó **(Serviços Anexados por SAS)**.

    ![Resultado da anexação a um serviço compartilhado usando SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão
Além de gerenciar contas do Azure Cosmos DB por meio de uma assinatura do Azure, um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Azure Cosmos DB** e escolha **Conectar-se ao Azure Cosmos DB...**

    ![conectar-se ao Azure Cosmos DB por uma cadeia de conexão][21]

2. Escolha a API do Azure Cosmos DB, cole a **Cadeia de Conexão** e, em seguida, clique em **OK** para se conectar à conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se ao Azure Data Lake Store por URI
Se você deseja obter acesso aos recursos que não existem na sua assinatura. Mas outras pessoas concedem a você para obter o URI para os recursos. Nesse caso, você pode se conectar ao Data Lake Store usando o URI depois de se conectar. Consulte as etapas a seguir.
1. Abra o Gerenciador de Armazenamento (Preview).
2. No painel esquerdo, expanda **Local e Conectado**.
3. Clique com o botão direito do mouse em **Data Lake Store**e, no menu de contexto, selecione **Conectar-se ao Data Lake Store...**.

    ![Menu de contexto Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Digite o URI e a ferramenta navegará até o local da URL inserida.

    ![Diálogo Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Resultado Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento
Se você precisa encontrar um recurso de armazenamento e não sabe onde ele está, use a caixa de pesquisa na parte superior do painel à esquerda para pesquisar o recurso.

À medida que você digita na caixa de pesquisa, o painel esquerdo exibe todos os recursos que correspondem ao valor de pesquisa inserido até esse ponto. Por exemplo, veja uma pesquisa por **pontos de extremidade** na captura de tela a seguir:

![Pesquisa na conta de armazenamento][23]

> [!NOTE]
> Use o **Painel de Gerenciamento de Conta** para cancelar a seleção das assinaturas que não contêm o item que você está procurando, a fim de melhorar o tempo de execução da pesquisa. Você também pode clicar com o botão direito do mouse em um nó e escolher **Pesquisar Aqui** para iniciar a pesquisa em um nó específico.
>
>

## <a name="next-steps"></a>Próximas etapas
* [Como gerenciar os recursos de armazenamento de Blobs do Azure com o Gerenciador de armazenamento (visualização)](vs-azure-tools-storage-explorer-blobs.md)
* [Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure (Versão Prévia)](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento (versão prévia)](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
