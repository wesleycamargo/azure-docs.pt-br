---
title: Introdução ao Gerenciador de Armazenamento | Microsoft Docs
description: Gerenciar recursos do armazenamento do Azure com o Gerenciador de Armazenamento
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 38a857b1d309b92c48137a46655155e0e131908c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002673"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Visão geral

O Gerenciador de Armazenamento do Azure é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende diversas maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador do Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes versões do Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, .NET Framework 4.6.2 ou posterior é necessário.

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes versões do macOS:

* macOS 10.12 “Sierra” e versões posteriores

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes distribuições do Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

O Gerenciador de Armazenamento do Azure pode funcionar em outras distribuições, mas somente aquelas listadas acima são suportadas oficialmente.

Para obter mais ajuda para instalar o Gerenciador de armazenamento no Linux, consulte o [guia de solução de problemas](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

As [Notas de Versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) do Gerenciador de Armazenamento do Azure contém etapas específicas para algumas distribuições.

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço

O Gerenciador de Armazenamento fornece várias maneiras de se conectar às contas de armazenamento. Por exemplo, você pode:

* Conecte-se às contas de armazenamento associadas às suas assinaturas do Azure.
* Conecte-se às contas de armazenamento e serviços compartilhados a partir de outras assinaturas do Azure.
* Conecte-se e gerencie o armazenamento local usando o Emulador de armazenamento do Azure.

Além disso, você pode trabalhar com contas nacionais e internacionais de armazenamento no Azure:

* [Conectar-se a uma assinatura do Azure](#connect-to-an-azure-subscription): gerencie os recursos de armazenamento que pertencem à sua assinatura do Azure.
* [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage): gerencie o armazenamento local usando o Emulador do Armazenamento do Azure.
* [Anexar a um armazenamento externo](#attach-or-detach-an-external-storage-account): gerencie os recursos de armazenamento que pertencem a outra assinatura do Azure ou que estão sob nuvens nacionais do Azure usando o nome de conta de armazenamento, a chave e os pontos de extremidade.
* [Anexar uma conta de armazenamento usando uma SAS](#attach-a-storage-account-by-using-a-shared-access-signature-sas): gerencie recursos de armazenamento que pertencem a outra assinatura do Azure usando uma assinatura de acesso compartilhado (SAS).
* [Anexar um serviço usando uma SAS](#attach-a-service-by-using-a-shared-access-signature-sas): gerencie um serviço de armazenamento específico (contêiner de blobs, fila ou tabela) que pertence a outra assinatura do Azure usando uma SAS.
* [Conectar-se a uma conta do Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): gerencie a conta do Cosmos DB usando uma cadeia de conexão.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

> [!NOTE]
> Se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Gerenciador de Armazenamento, selecione **Gerenciar Contas** para acessar o **Painel de Gerenciamento de Conta**.

    ![Gerenciar Contas][1]

2. Agora o painel à esquerda exibe todas as contas do Azure em que você se conectou. Para conectar-se a outra conta, selecione **Adicionar uma conta**

3. Se você quiser entrar em uma nuvem nacional ou no Azure Stack, clique na lista suspensa **Ambiente do Azure** para selecionar qual nuvem do Azure você deseja usar. Depois de escolher o ambiente, clique no botão **Entrar...**. Se você estiver entrando no Azure Stack, consulte [Conectar o Gerenciador de Armazenamento a uma assinatura do Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) para obter mais informações.

    ![Opção de conexão][2]

4. Depois de entrar com êxito usando uma conta do Azure, a conta e as assinaturas do Azure associadas com essa conta serão adicionadas ao painel à esquerda. Selecione as assinaturas do Azure com as quais você deseja trabalhar e, em seguida, selecione **Aplicar** (selecionar **Todas as assinaturas:** alterna entre a seleção de todas ou de nenhuma das assinaturas do Azure listadas).

    ![Selecionar assinaturas do Azure][3]

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

## <a name="work-with-local-development-storage"></a>Trabalhar com o armazenamento de desenvolvimento local

O Gerenciador de Armazenamento permite que você trabalhe no armazenamento local usando um emulador. Essa abordagem permite que você simule o trabalho com o Armazenamento do Azure sem, necessariamente, ter uma conta de armazenamento implantada no Azure.

Começando com a versão 1.1.0, emulador de armazenamento local é compatível com todas as plataformas. O Gerenciador de armazenamento pode se conectar a qualquer serviço emulado ouvindo seus pontos de extremidade de armazenamento local padrão.

> [!NOTE]
> Suporte para recursos e serviços de armazenamento pode variar amplamente, dependendo de sua escolha de emulador. Verifique se o emulador suporta os serviços e recursos que você pretende trabalhar com.

1. Configure os serviços do seu emulador de escolha para escutar uma porta não utilizada.

   Serviço Emulado | Ponto de extremidade padrão
   -----------------|-------------------------
   Blobs            | `http://127.0.0.1:10000`
   Filas           | `http://127.0.0.1:10001`
   Tabelas           | `http://127.0.0.1:10002`

2. Inicie o emulador.
   > [!IMPORTANT]
   > O Gerenciador de Armazenamento do Microsoft Azure não inicia automaticamente o emulador. Você deve iniciá-lo.

3. No Gerenciador de Armazenamento do Microsoft Azure, clique no botão **Adicionar Conta**. Selecione **Anexar a um emulador local** e clique em **Próximo**.

4. Insira os números de porta para os serviços configurados acima (deixe em branco se você não pretende usar esse serviço). Clique em **Próximo**, em seguida, **Conectar** para criar a conexão.

5. Expanda o **Contas de Armazenamento** > **Locais e Anexadas** > nós, em seguida, expanda os nós de serviço no nó correspondente à sua conexão do emulador.

   Você pode usar esse nó para criar e trabalhar com tabelas, filas e blobs de locais. Para saber como trabalhar com cada tipo de conta de armazenamento, consulte os guias a seguir:

   * [Gerenciar recursos de Armazenamento de Blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
   * [Gerenciar recursos de Armazenamento de Arquivo do Azure](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Conexão ou desconexão de uma conta de armazenamento externo

O Gerenciador de Armazenamento permite anexar contas de armazenamento externas para facilitar o compartilhamento das contas de armazenamento. Esta seção explica como anexar e desanexar contas do armazenamento externo.

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

Para anexar a uma conta de armazenamento externo, você precisará do nome da conta e da chave. A seção "Como obter as credenciais de conta de armazenamento" explica como obter esses valores no portal do Azure. No entanto, no portal, a chave de conta é chamada **key1**. Então, quando o Gerenciador de Armazenamento solicitar uma chave de conta, digite o valor de **key1**.

1. No Gerenciador de Armazenamento, abra a **Caixa de Diálogo Conectar**.

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

3. O UsuárioB usa o Gerenciador de Armazenamento para se anexar à conta pertencente ao UsuárioA usando a SAS fornecida.

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>Gerar uma cadeia de caracteres de consulta de SAS para a conta que você deseja compartilhar

1. No Gerenciador de Armazenamento, clique com o botão direito do mouse na conta de armazenamento que você deseja compartilhar e, em seguida, selecione **Obter Assinatura de Acesso Compartilhado...**.

    ![Opção do menu de contexto Obter SAS][14]

2. Na caixa de diálogo **Gerar Assinatura de Acesso Compartilhado**, especifique o período e as permissões que você deseja para a conta e, em seguida, clique no botão **Criar**.

    ![Caixa de diálogo Obter SAS][15]

3. Ao lado da caixa de texto **Cadeia de caracteres de consulta**, escolha **Copiar** para copiá-la na área de transferência e, em seguida, clique em **Fechar**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Anexar a uma conta de armazenamento usando uma Cadeia de Conexão de SAS

1. No Gerenciador de Armazenamento, abra a **Caixa de Diálogo Conectar**.

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

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Anexar ao serviço de conta compartilhada usando um URI de SAS

1. No Gerenciador de Armazenamento, abra a **Caixa de Diálogo Conectar**.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Na **Caixa de Diálogo Conectar**, escolha **Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado** e, em seguida, clique em **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][16]

3. Escolha **Usar um URI de SAS** e cole o URI no campo **URI:**. Clique no botão **Avançar**.

    ![Caixa de diálogo Conectar ao Armazenamento do Azure][19]

4. Na caixa de diálogo **Resumo da Conexão**, verifique as informações. Para fazer alterações, selecione **Retornar** e, em seguida, insira as configurações desejadas.

5. Selecione **Conectar**.

6. Depois que o serviço é anexado com êxito, ele será exibido sob o nó **(Serviços Anexados por SAS)**.

    ![Resultado da anexação a um serviço compartilhado usando SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Além de gerenciar contas do Azure Cosmos DB por meio de uma assinatura do Azure, um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Azure Cosmos DB** e escolha **Conectar-se ao Azure Cosmos DB...**

    ![conectar-se ao Azure Cosmos DB por uma cadeia de conexão][21]

2. Escolha a API do Azure Cosmos DB, cole a **Cadeia de Conexão** e, em seguida, clique em **OK** para se conectar à conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se ao Azure Data Lake Store por URI

Se você deseja obter acesso aos recursos que não existem na sua assinatura. Mas outras pessoas concedem a você para obter o URI para os recursos. Nesse caso, você pode se conectar ao Data Lake Store usando o URI depois de se conectar. Consulte as etapas a seguir.

1. Abra o Explorer do Armazenamento.
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

* [Gerenciar recursos de Armazenamento de Blobs com o Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure (Versão Prévia)](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
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
