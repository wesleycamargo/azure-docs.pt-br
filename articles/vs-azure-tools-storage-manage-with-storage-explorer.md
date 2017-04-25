---
title: "Introdução ao Gerenciador de Armazenamento (Preview) | Microsoft Docs"
description: "Gerenciar os recursos de armazenamento do Azure com o Gerenciador de Armazenamento (Visualização)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>Introdução ao Gerenciador de Armazenamento (Preview)
## <a name="overview"></a>Visão geral
O Gerenciador de Armazenamento do Microsoft Azure (Preview) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende as várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador de Armazenamento do Microsoft Azure (Preview)][15]

## <a name="prerequisites"></a>Pré-requisitos
* [Baixe e instale o Gerenciador de Armazenamento (preview)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço
O Gerenciador de Armazenamento (Visualização) fornece várias maneiras de se conectar às contas de armazenamento. Isso inclui se conectar a contas de armazenamento associadas às suas assinaturas do Azure, conectar-se às contas de contas de armazenamento e serviços compartilhados por outras assinaturas do Azure e até mesmo se conectar e gerenciar o armazenamento local usando o Emulador de Armazenamento do Azure. Além disso, você pode trabalhar com contas nacionais e internacionais de armazenamento no Azure:

* [Conectar-se a uma assinatura do Azure](#connect-to-an-azure-subscription) - gerencie os recursos de armazenamento que pertencem à sua assinatura do Azure.
* [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) - gerencie o armazenamento local usando o Emulador de Armazenamento do Azure.
* [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) - Gerencie os recursos de armazenamento que pertencem a outra assinatura do Azure ou que estejam sob nuvens nacionais do Azure usando o nome de conta de armazenamento, a chave e os pontos de extremidade.
* [Anexar conta de armazenamento usando SAS](#attach-storage-account-using-sas) - gerencie os recursos que pertencem a outra assinatura do Azure usando um SAS.
* [Anexar usando SAS](#attach-service-using-sas) - gerencie um serviço de armazenamento específico (contêiner de blob, fila ou tabela) que pertencem a outra assinatura do Azure usando um SAS.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure
> [!NOTE]
> Se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Gerenciador de Armazenamento (Visualização), selecione **Configurações de conta do Azure**.

    ![Configurações de conta do Azure][0]
2. O painel esquerdo exibe todas as contas da Microsoft em que você fez logon. Para se conectar a outra conta, selecione **Adicionar uma conta**e siga os diálogos para entrar com uma conta da Microsoft associada a pelo menos uma assinatura ativa do Azure.
> [!NOTE]
>Atualmente, não há suporte para a conexão ao Azure nacional, como o Black Forest Azure, Fairfax Azure e Mooncake Azure, por meio de início de sessão. Consulte a seção **Anexar ou desanexar uma conta de armazenamento externo** para saber como se conectar a contas de armazenamento do Azure nacional.

3. Depois de entrar com êxito usando uma conta da Microsoft, o painel esquerdo é preenchido com as assinaturas do Azure associadas à conta. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**. (A escolha dos botões **Todas as assinaturas** seleciona todas ou nenhuma das assinaturas do Azure listadas).

    ![Selecionar assinaturas do Azure][3]
4. O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Conexão com uma assinatura do Azure Stack

1. Uma conexão VPN é necessária para que o Gerenciador de armazenamento acesse a assinatura do Azure Stack remotamente. Para saber mais sobre como configurar a conexão VPN para o Azure Stack, confira [Como conectar-se com o Azure com VPN](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn)

2. Para o POC do Azure Stack, você precisa exportar o certificado de raiz da autoridade do Azure Stack. Abra `mmc.exe` em CON01-MAS, Azure Stack host ou em uma máquina local com a conexão VPN para o Azure Stack. Em **Arquivo**, selecione **Adicionar/Remover Snap-in** e adicione **Certificados** para gerenciar a **conta de computador** do **computador local**.

   ![Como carregar o certificado de raiz do Azure Stack por meio do mmc.exe][25]   

   Localize o **AzureStackCertificationAuthority** que pode ser encontrado em **Raiz do console\Certificado (Computador local)\Autoridades de certificação de raiz confiável\Certificados**. Clique com o botão direito no item e selecione **Todas as tarefas -> Exportar**. Em seguida, siga as caixas de diálogo para exportar o certificado **X.509 codificado com Base 64 (.CER)**. O certificado exportado será usado na próxima etapa.   

   ![Como exportar o certificado raiz de autorização do Azure Stack raiz][26]   

3. No Gerenciador de armazenamento (visualização), selecione o menu **Editar**, em seguida, **Certificados SSL**, em seguida, **Importar certificados**. Use a caixa de diálogo do seletor de arquivos para localizar e abrir o certificado que você explorou na etapa anterior. Após a importação, você será solicitado para reiniciar o Gerenciador de armazenamento.

   ![Importe o certificado no Gerenciador de armazenamento (visualização)][27]

4. Depois que o Gerenciador de armazenamento (visualização) reiniciar, selecione o menu **Editar** e confira se o **Destino do Azure Stack** está marcado. Se não estiver, marque-o e reinicie o Gerenciador de armazenamento para que a alteração tenha efeito. Essa configuração é necessária para que haja compatibilidade com seu ambiente do Azure Stack.

   ![Confira se o Destino Azure Stack está selecionado][28]

5. Na barra do lado esquerdo, selecione **Gerenciar contas**. O painel esquerdo exibe todas as contas da Microsoft nas quais você se registrou. Para conectar-se com a conta do Azure Stack, selecione **Adicionar uma conta**.

   ![Adicionar uma conta do Azure Stack][29]

6. Escolha **Criar ambiente personalizado** que pode ser encontrado em **Ambiente Azure** na caixa de diálogo **Adicionar nova conta** e, em seguida, clique em **Próximo**.

7. Insira todas as informações necessárias do ambiente personalizado do Azure Stack e, em seguida, clique em **Entrar**.  Preencha a caixa de diálogo **Entrar em um ambiente de nuvem personalizada** para entrar com a conta do Azure Stack associada a pelo menos uma assinatura ativa do Azure Stack. Veja em seguida os detalhes para cada campo na caixa de diálogo:

    * **Nome do ambiente** – o campo pode ser personalizado pelo usuário.
    * **Autoridade** – o valor deve ser https://login.windows.net. Para o Azure na China (Mooncake), use https://login.chinacloudapi.cn.
    * **ID do recurso de conexão** – execute o PowerShell a seguir para obter o valor:

    Se você for um administrador de nuvem:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    Se você for um locatário:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **Ponto de extremidade de gráfico** – o valor deve ser https://graph.windows.net. Para o Azure na China (Mooncake), use https://graph.chinacloudapi.cn.
    * **Id de recurso do ARM** – use o mesmo valor como ID do recurso de conexão.
    * **O ponto de extremidade dos recursos do ARM** – as amostras de ponto de extremidade dos recursos do ARM:

    Para o administrador de nuvem: https://adminmanagement.local.azurestack.external   
    Para o locatário: https://management.local.azurestack.external
 
    * **Ids de locatário** – opcional. O valor é fornecido apenas quando o diretório deve ser especificado.

8. Depois de entrar com êxito usando uma conta do Azure Stack, o painel esquerdo é preenchido com as assinaturas do Azure Stack associadas à conta. Selecione as assinaturas do Azure Stack com as quais você deseja trabalhar e, então, selecione **Aplicar**. (A escolha dos botões **Todas as assinaturas** seleciona todas ou nenhuma das assinaturas do Azure Stack listadas.)

   ![Selecione as assinaturas do Azure Stack depois de preencher a caixa de diálogo do ambiente de nuvem personalizada][30]

9. O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure Stack selecionadas.

   ![Lista de contas de armazenamento, incluindo contas de assinatura do Azure Stack][31]

## <a name="work-with-local-development-storage"></a>Trabalhar com o armazenamento de desenvolvimento local
O Gerenciador de Armazenamento (Preview) permite que você trabalhe no armazenamento local usando o Emulador de Armazenamento do Azure. Isso permite que você escreva códigos e teste o armazenamento sem necessariamente ter uma conta de armazenamento implantada no Azure (uma vez que a conta de armazenamento está sendo emulada pelo Emulador de Armazenamento do Azure).

> [!NOTE]
> No momento, o Emulador de Armazenamento do Azure tem suporte somente para Windows.
>
>

1. No painel esquerdo do Gerenciador de Armazenamento (Visualização), expanda o nó **(Local e Anexados** > **Contas de Armazenamento** > **(Desenvolvimento)**.

    ![Nó de desenvolvimento local][21]
2. Se você ainda não tiver instalado o Emulador de Armazenamento do Azure, receberá uma solicitação para fazer isso por meio de uma barra de informações. Se a barra de informações for exibida, escolha **Baixar a versão mais recente**e instale o emulador.

    ![Baixar o prompt do Emulador de Armazenamento do Azure][22]
3. Após a instalação do emulador, você poderá criar e trabalhar com blobs, filas e tabelas locais. Para saber como trabalhar com cada tipo de conta de armazenamento, selecione um dos links a seguir:

   * [Gerenciar recursos de Armazenamento de Blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
   * Gerenciar recursos de armazenamento de compartilhamento de arquivos do Azure - *em breve*
   * Gerenciar recursos de Armazenamento de filas do Azure - *em breve*
   * Gerenciar recursos de Armazenamento de tabelas do Azure - *em breve*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexão ou desconexão de uma conta de armazenamento externo
O Gerenciador de Armazenamento (Preview) permite anexar contas de armazenamento externo para facilitar o compartilhamento das contas de armazenamento. Esta seção explica como anexar e desanexar contas do armazenamento externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta de armazenamento
Para compartilhar uma conta de armazenamento externo, o proprietário dessa conta precisa primeiro obter as credenciais da conta, nome e chave, para depois compartilhar essas informações com a pessoa que deseja ser anexada a essa conta (externa). As credenciais da conta de armazenamento podem ser obtidas no Portal do Azure, executando estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Procurar**.
3. Selecione **Contas de Armazenamento**.
4. Na folha **Contas de Armazenamento** , escolha a conta de armazenamento desejada.
5. Na folha **Configurações** da conta de armazenamento selecionada, escolha **Chaves de acesso**.

   ![Opção Chaves de Acesso][5]
6. Na folha **Chaves de acesso**, copie os valores de **NOME DA CONTA DE ARMAZENAMENTO** e **CHAVE 1** que serão usados para anexação à conta de armazenamento.

   ![Chaves de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Anexação a uma conta de armazenamento externo
Para anexar a uma conta de armazenamento externo, você precisará do nome da conta e da chave. A seção *Obter as credenciais de conta de armazenamento* explica como obter esses valores no portal do Azure. No entanto, observe que, no portal, a chave da conta se chama "chave 1" e, portanto, quando o Gerenciador de Armazenamento (Visualização) solicitar uma chave de conta, você inserirá (ou colará) o valor "chave 1".

1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

   ![Opção Conectar ao Armazenamento do Azure][23]
2. Na caixa de diálogo **Conectar ao armazenamento do Azure**, especifique a chave de conta (o valor "chave 1" no portal do Azure) e selecione **Avançar**.
> [!NOTE]
> Você pode inserir a cadeia de Conexão de Armazenamento de uma conta de armazenamento no Azure nacional. Por exemplo, insira cadeias de conexão semelhantes à seguinte para conectar-se a contas de armazenamento do Azure Black Forest: DefaultEndpointsProtocol=https;AccountName=cawatest03;AccountKey=<storage_account_key>;EndpointSuffix=core.cloudapi.de; Você pode obter a cadeia de conexão do portal do Azure da mesma maneira como descrito na seção **Obter as credenciais da conta de armazenamento**

   ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]

3. Na caixa de diálogo **Anexar Armazenamento Externo**, insira o nome da conta de armazenamento na caixa **Nome da conta**, especifique outras configurações desejadas e selecione **Avançar** para concluir.

   ![Caixa de diálogo Anexar um armazenamento externo][8]
4. Na caixa de diálogo **Resumo da Conexão** , verifique as informações. Se você quiser alterar alguma coisa, selecione **Voltar** e insira novamente as configurações desejadas. Quando tiver concluído, selecione **Conectar**.
5. Depois de conectada, a conta de armazenamento externo será exibida com o texto **(Externo)** anexado ao nome da conta de armazenamento.

   ![Resultado da conexão a uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desconexão de uma conta de armazenamento externo
1. Clique com o botão direito na conta de armazenamento externo que você deseja desconectar e, no menu de contexto, escolha **Desanexar**.

   ![Opção Desanexar do armazenamento][10]
2. Quando a caixa de mensagem de confirmação aparecer, escolha **Sim** para confirmar a desconexão da conta de armazenamento externo.

## <a name="attach-storage-account-using-sas"></a>Anexar conta de armazenamento usando SAS
Uma [SAS (Assinatura de Acesso Compartilhado)](storage/storage-dotnet-shared-access-signature-part-1.md) proporciona ao administrador de uma assinatura do Azure a capacidade de conceder acesso temporário a uma conta de armazenamento sem precisar fornecer as credenciais de assinatura do Azure.

Para ilustrar isso, vamos supor que o UsuárioA é um administrador de uma assinatura do Azure, e o UsuárioA deseja permitir que o UsuárioB acesse uma conta de armazenamento por um período limitado com determinadas permissões:

1. O UsuárioA gera uma SAS (formada pela cadeia de conexão da conta de armazenamento) para um período específico e com as permissões desejadas.
2. O UsuárioA compartilha a SAS com a pessoa que deseja acessar a conta de armazenamento - em nosso exemplo, o UsuárioB.  
3. O UsuárioB usa o Gerenciador de Armazenamento (Preview) para se anexar à conta pertencente ao UsuárioA usando a SAS fornecida.

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obter uma SAS para a conta que você deseja compartilhar
1. No Gerenciador de Armazenamento (Visualização), clique com botão direito do mouse na conta de armazenamento que você deseja compartilhar e, no menu de contexto, escolha **Obter Assinatura de Acesso Compartilhado**.

   ![Opção do menu de contexto Obter SAS][13]
2. Na caixa de diálogo **Assinatura de Acesso Compartilhado**, especifique o intervalo e as permissões que você deseja para a conta e escolha **Criar**.

    ![Caixa de diálogo Obter SAS][14]
3. Um segundo diálogo **Assinatura de Acesso Compartilhado** aparecerá exibindo a SAS. Escolha **Copiar** ao lado de **Cadeia de Conexão** para copiá-la na área de transferência. Escolha **Fechar** para ignorar a caixa de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Anexar à conta compartilhada usando a SAS
1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

   ![Opção Conectar ao Armazenamento do Azure][23]
2. Na caixa de diálogo **Conectar ao Armazenamento do Azure**, especifique a cadeia de conexão e selecione **Avançar**.

   ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]
3. Na caixa de diálogo **Resumo da Conexão** , verifique as informações. Se você quiser alterar alguma coisa, selecione **Voltar** e insira novamente as configurações desejadas. Quando tiver concluído, selecione **Conectar**.
4. Depois de anexada, a conta de armazenamento será exibida com o texto (SAS) anexado ao nome da conta fornecido.

   ![O resultado da anexação a uma conta usando a SAS][17]

## <a name="attach-service-using-sas"></a>Anexar usando SAS
A seção [Anexar a conta de armazenamento usando SAS](#attach-storage-account-using-sas) ilustra como um administrador de assinatura do Azure pode conceder acesso temporário a uma conta de armazenamento gerando (e compartilhando) uma SAS para a conta de armazenamento. Da mesma forma, uma SAS pode ser gerada para um serviço específico (contêiner de blob, fila ou tabela) em uma conta de armazenamento.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Gerar uma SAS para o serviço que você deseja compartilhar
Nesse contexto, um serviço pode ser um contêiner de blob, fila ou tabela. As seções a seguir explicam como gerar a SAS para o serviço listado:

* [Obter a SAS para um contêiner de blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obter a SAS para um compartilhamento de arquivos - *em breve*
* Obter a SAS para uma fila - *em breve*
* Obter a SAS para uma tabela - *em breve*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Conectar ao serviço de conta compartilhada usando a SAS
1. No Gerenciador de Armazenamento (Visualização), selecione **Conectar ao armazenamento do Azure**.

   ![Opção Conectar ao Armazenamento do Azure][23]
2. Na caixa de diálogo **Conectar ao Armazenamento do Azure**, especifique o URI de SAS e selecione **Avançar**.

   ![Caixa de diálogo Conectar ao Armazenamento do Azure][24]
3. Na caixa de diálogo **Resumo da Conexão** , verifique as informações. Se você quiser alterar alguma coisa, selecione **Voltar** e insira novamente as configurações desejadas. Quando tiver concluído, selecione **Conectar**.
4. Depois de anexado o serviço recém-anexado será exibido abaixo do nó **(SAS do Serviço)**.

   ![Resultado da anexação a um serviço compartilhado usando SAS][20]

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento
Se você tiver uma lista longa de contas de armazenamento, uma maneira rápida de localizar uma determinada conta de armazenamento é usar a caixa de pesquisa na parte superior do painel esquerdo.

À medida que você digita na caixa de pesquisa, o painel esquerdo exibe somente as contas de armazenamento que correspondem ao valor de pesquisa inserido até esse ponto. A captura de tela a seguir ilustra um exemplo no qual eu pesquisei por todas as contas de armazenamento cujo nome contém o texto "tarcher".

![Pesquisa na conta de armazenamento][11]

Para limpar a pesquisa, escolha o botão **x** na caixa de pesquisa.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar os recursos de armazenamento de blobs do Azure com o Gerenciador de Armazenamento (Preview)](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png

