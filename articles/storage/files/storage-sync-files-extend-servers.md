---
title: 'Tutorial: Estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure | Microsoft Docs'
description: Saiba como estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure, do início ao fim.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc34411cc27870dbd9c707a34ebf34b96c7253dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986110"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Tutorial: Estender servidores de arquivos do Windows com a Sincronização de Arquivos do Azure
Neste tutorial, vamos mostrar as etapas básicas para estender a capacidade de armazenamento de um Windows Server usando a Sincronização de Arquivos do Azure. Embora estejamos usando uma VM do Azure do Windows Server para este tutorial, você normalmente faria este processo para seus servidores locais. Se você estiver pronto para implantar a Sincronização de Arquivos do Azure em seu próprio ambiente, use o artigo [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) em vez deste.

> [!div class="checklist"]
> * Implantar o Serviço de Sincronização de Armazenamento
> * Preparar Servidores Windows para uso com Sincronização de arquivos do Azure
> * Instalar o agente de Sincronização de Arquivo do Azure
> * Registrar o Windows Server com o Serviço de Sincronização de Armazenamento
> * Criar um grupo de sincronização e um ponto de extremidade de nuvem
> * Criar um ponto de extremidade do servidor

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em https://portal.azure.com.

## <a name="prepare-your-environment"></a>Prepare o seu ambiente
Há algumas coisas que você precisa configurar para este tutorial antes de implantar a Sincronização de Arquivos do Azure. Juntamente com a criação de uma conta do Armazenamento do Azure e o compartilhamento de arquivos, você criará uma VM do Windows Server 2016 Datacenter e preparará o servidor para a Sincronização de Arquivos do Azure.

### <a name="create-a-folder-and-txt-file"></a>Criar uma pasta e um arquivo .txt

No computador local, crie uma nova pasta chamada *FilesToSync* e adicione um arquivo de texto chamado *mytestdoc.txt*. Você vai carregar esse arquivo no compartilhamento de arquivos mais tarde neste tutorial.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos
Em seguida, crie um compartilhamento de arquivos.

1. Quando a implantação da conta de armazenamento do Azure for concluída, clique em **Ir para o recurso**.
1. Clique em **Arquivos** no painel da conta de armazenamento.

    ![Clicar em Arquivos](./media/storage-sync-files-extend-servers/click-files.png)

1. Clique em **+ Compartilhamento de Arquivos**.

    ![Clicar no botão Adicionar compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nomeie o novo compartilhamento de arquivos *afsfileshare* e insira "1" como a **Cota**; em seguida, clique em **Criar**. A cota pode ter um máximo de 5 TiB, mas você só precisa de 1 GB para este tutorial.

    ![Fornecer um nome e uma cota para o novo compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecione o novo compartilhamento de arquivos e, no local do compartilhamento de arquivos, clique em **Carregar**.

    ![Carregar um arquivo](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Navegue até a pasta *FilesToSync* em que você criou seu arquivo. txt, selecione *mytestdoc.txt* e clique em **Carregar**.

    ![Procurar o compartilhamento de arquivos](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Nesta altura, você já criou uma conta de Armazenamento do Azure e um compartilhamento de arquivos contendo um arquivo no Azure. Agora, você criará a VM do Azure com o Windows Server 2016 Datacenter para representar o servidor local neste tutorial.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Implantar uma VM e anexar um disco de dados

1. Em seguida, expanda o menu no lado esquerdo do portal e escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do **Azure Marketplace**, procure e selecione **Windows Server 2016 Datacenter**, em seguida, escolha **Criar**.
1. Na guia **Básico**, em **Detalhes do projeto**, selecione o grupo de recursos que você criou para este tutorial.

   ![Insira as informações básicas sobre sua VM na folha do portal](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Em **Detalhes da instância**, forneça um nome de VM, como *myVM*.
1. Deixe as configurações padrão para **Região**, **Opções de disponibilidade**, **Imagem** e **Tamanho**.
1. Em **Conta de administrador**, forneça um **Nome de usuário** e uma **Senha** para a VM.
1. Em **Regras de porta de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** e **HTTP** na lista suspensa.

   Antes de criar a VM, você precisará criar um disco de dados.

1. Clique em **Avançar:Discos**

   ![Adicionar discos de dados](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Na guia **Discos**, em **Opções de disco**, mantenha os padrões.
1. Em **DISCOS DE DADOS**, clique em **Criar e anexar um novo disco**.

1. Mantenha os padrões, exceto alterar o **Tamanho (GiB)** para **1GB** neste tutorial.

   ![Detalhes do disco de dados](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Clique em **OK**.
1. Clique em **Revisar + Criar**.
1. Clique em **Criar**.

   Você pode clicar no ícone **Notificações** para assistir ao **Progresso da implantação**. A criação de uma nova VM levará alguns minutos para ser concluída.

1. Depois que a implantação da VM for concluída, clique em **Ir para o recurso**.

   ![Ir para o recurso](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Nesta altura, você já criou uma nova máquina virtual e anexou um disco de dados. Agora, você precisa se conectar à VM.

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

1. No portal do Azure, clique em **Conectar** na página de propriedades da máquina virtual.

   ![Conecte-se a uma VM do Azure no portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para se conectar por **endereço IP** pela porta 3389 e clique em **Baixar arquivo RDP**.

   ![Baixe o arquivo RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Abra o arquivo RDP baixado e clique em **Conectar** quando solicitado.
1. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome do usuário como *localhost/nome de usuário*, insira a senha que você criou para a máquina virtual e clique em **OK**.

   ![Mais opções](./media/storage-sync-files-extend-servers/local-host2.png)

1. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para criar a conexão.

### <a name="prepare-the-windows-server"></a>Preparar o Windows Server
Para o servidor **Windows Server 2016 Datacenter**, desabilite **Configuração de Segurança Reforçada do Internet Explorer**. Essa etapa é necessária apenas para o registro inicial do servidor. Você pode habilitá-la novamente depois que o servidor foi registrado.

Na VM **Windows Server 2016 Datacenter**, o **Gerenciador do Servidor** será aberto automaticamente.  Se o **Gerenciador do Servidor** não abrir por padrão, pesquise por ele no Explorer.

1. Em **Gerenciador do Servidor**, clique em **Servidor Local**.

   !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. No subpainel **Propriedades**, selecione o link de **Configuração de Segurança Reforçada do IE**.  

    ![O painel "Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Na caixa de diálogo **Configuração de Segurança Reforçada do Internet Explorer**, selecione **Desativado** para **Administradores** e **Usuários**.

    ![A janela pop-up da Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Agora, você pode adicionar o disco de dados à VM.

### <a name="add-the-data-disk"></a>Adicionar o disco de dados

1. Enquanto estiver na VM do **Windows Server 2016 Datacenter**, clique em **Arquivos e serviços de armazenamento** > **Volumes** > **Discos** .

    ![Disco de dados](media/storage-sync-files-extend-servers/your-disk.png)

1. Clique com o botão direito do mouse no disco de 1 GB chamado **Disco Virtual Msft** e clique em **Novo volume**.
1. Conclua o Assistente mantendo os padrões, anotando a letra da unidade atribuída e clicando em **Criar**.
1. Clique em **fechar**

   Nesta altura, você já colocou o disco online e criou um volume. Você pode confirmar que a adição do disco de dados teve êxito abrindo o Explorer na VM e confirmando que a nova unidade está presente.

1. No Explorer da VM, expanda **Este PC** e clique duas vezes na nova unidade. É a unidade F: neste exemplo.
1. Clique com botão direito do mouse e selecione **Nova** > **Pasta**. Nomeie a pasta *FilesToSync*.
1. Clique duas vezes na pasta **FilesToSync**.
1. Clique com botão direito do mouse e selecione **Novo** > **Documento de Texto**. Nomeie o arquivo de texto *MyTestFile*.

    ![Adicionar um novo arquivo de texto](media/storage-sync-files-extend-servers/new-file.png)

1. Feche o **Explorer** e o **Gerenciador do Servidor**.

### <a name="download-the-azurerm-powershell-module"></a>Baixar o módulo AzureRM do PowerShell
Em seguida, na VM do **Windows Server 2016 Datacenter**, instale o **módulo AzureRM do PowerShell** no servidor.

1. Na VM, abra uma janela do PowerShell com privilégios elevados
1. Execute o comando a seguir:

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > Caso sua versão seja mais antiga que a 2.8.5.201 do NuGet, será necessário baixar e instalar a versão mais recente do NuGet.

   Por padrão, a galeria do PowerShell não está configurada como um repositório confiável para o PowerShellGet. Na primeira vez em que a PSGallery for utilizada, o prompt a seguir será exibido:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Responda `Yes` ou `Yes to All` para continuar a instalação.

O módulo `AzureRM` é um módulo de rollup para os cmdlets do Azure PowerShell. A instalação dele baixa todos os módulos disponíveis do Azure Resource Manager e disponibiliza seus cmdlets para uso.

Nesta altura, você já terminou de configurar seu ambiente para o tutorial e está pronto para começar a implantar o **Serviço de Sincronização de Armazenamento**.

## <a name="deploy-the-service"></a>Implantar o serviço 
A implantação da Sincronização de Arquivos do Azure começa com a colocação de um recurso do **Serviço de Sincronização de Armazenamento** em um grupo de recursos da assinatura selecionada. O Serviço de Sincronização de Armazenamento herdou as permissões de acesso da assinatura e do grupo de recursos em que foi implantado.

1. No portal do Azure, clique em **Criar um recurso** e procure **Sincronização de Arquivos do Azure**.
1. Nos resultados da pesquisa, selecione **Sincronização de Arquivos do Azure**.
1. Selecione **Criar** para abrir a guia **Implantar Sincronização de Armazenamento**.

   ![Implantar Sincronização de Armazenamento](media/storage-sync-files-extend-servers/afs-info.png)

   No novo painel que será aberta, insira as seguintes informações:

   | Valor | DESCRIÇÃO |
   | ----- | ----- |
   | **Nome** | Um nome exclusivo (por assinatura) para o Serviço de Sincronização de Armazenamento.<br><br>Neste tutorial, estamos usando *afssyncservice02*. |
   | **Assinatura** | A assinatura que você está usando para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que você está usando para este tutorial.<br><br>Neste tutorial, estamos usando *afsresgroup101918*. |
   | **Localidade** | Leste dos EUA |

1. Quando terminar, selecione **Criar** para implantar o **Serviço de Sincronização de Armazenamento**.
1. Clique na guia **Notificações** > **Ir para o recurso**.

## <a name="install-the-agent"></a>Instalar o agente
O agente de Sincronização de arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure.

1. Volte para a VM do **Windows Server 2016 Datacenter** e abra o **Internet Explorer**.
1. Vá para o [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Role para baixo até a seção **Agente de Sincronização de Arquivos do Azure** e clique em **Baixar**.

   ![Download do agente de sincronização](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Marque a caixa do **StorageSyncAgent_V3_WS2016. EXE** e clique em **Avançar**.

   ![Selecionar agente](media/storage-sync-files-extend-servers/select-agent.png)

1. **Permitir uma vez** > **Executar** > **Abrir** o arquivo.
1. Se você ainda não fez isso, feche a janela do PowerShell.
1. Aceite os padrões do **Assistente de Instalação do Agente de Sincronização de Armazenamento**.
1. Clique em **Instalar**.
1. Clique em **Concluir**.

Você implantou o Serviço de Sincronização do Azure e instalou o agente na VM do **Windows Server 2016 Datacenter**. Agora você precisa registrar a VM no **Serviço de Sincronização de Armazenamento**.

## <a name="register-windows-server"></a>Registrar o Windows Server
Registrar o Windows Server com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor somente pode ser registrado em um Serviço de Sincronização de Armazenamento e sincronizar com outros servidores e compartilhamentos de arquivos do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

A interface do usuário do Registro do Servidor deve abrir automaticamente após a instalação do **agente de Sincronização de Arquivos do Azure**. Se não estiver, você pode abri-lo manualmente de seu local de arquivo: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Quando a interface do usuário do registro de servidor é aberta na VM, clique em **OK**.
1. Clique em **Entrar** para começar.
1. Insira as credenciais de conta do Azure e clique em **Entrar**.
1. Forneça as seguintes informações:

   ![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valor | DESCRIÇÃO |
   | **Assinatura do Azure** | A assinatura que contém o Serviço de Sincronização de Armazenamento para este tutorial. |
   | **Grupo de recursos** | O grupo de recursos que contém o Serviço de Sincronização de Armazenamento para este tutorial. Usamos *afsresgroup101918* ao longo deste tutorial. |
   | **Serviço de Sincronização de Armazenamento** | O nome do Serviço de Sincronização de Armazenamento usado para este tutorial. Usamos *afssyncservice02* ao longo deste tutorial. |

1. Clique em **Registrar** para concluir o registro do servidor.
1. Como parte do processo de registro, você será solicitado a realizar uma entrada adicional. Entre e clique em **Avançar**.
1. Clique em **OK**.

## <a name="create-a-sync-group"></a>Criar um Grupo de Sincronização
Um grupo de sincronização define a topologia de sincronização para um conjunto de arquivos. Um grupo de sincronização precisa conter um ponto de extremidade de nuvem que represente um compartilhamento de arquivos do Azure e um ou mais pontos de extremidade do servidor. Um ponto de extremidade do servidor representa um caminho em um servidor registrado.

1. Para criar um grupo de sincronização, no [portal do Azure](https://portal.azure.com/), selecione **+ Grupo de sincronização** no Serviço de Sincronização de Armazenamento que você criou para este tutorial. Usamos *afssyncservice02* como o exemplo neste tutorial.

   ![Criar um novo Grupo de Sincronização no Portal do Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. No painel que abrir, insira as informações a seguir para criar um Grupo de Sincronização com um ponto de extremidade de nuvem:

   | Valor | DESCRIÇÃO |
   | ----- | ----- |
   | **Nome do grupo de sincronização** | Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você. Neste tutorial, estamos usando *afssyncgroup*.|
   | **Assinatura** | A assinatura onde você implantou o Serviço de Sincronização de Armazenamento para este tutorial. |
   | **Conta de armazenamento** |Clique em **Selecionar conta de armazenamento**. No painel que aparece, selecione a conta de armazenamento que tem o compartilhamento de arquivos do Azure criado para este tutorial. Usamos *afsstoracct101918*. |
   | **Compartilhamento de arquivos do Azure** | O nome do compartilhamento de arquivos do Azure que você criou para este tutorial. Usamos *afsfileshare*. |

1. Clique em **Criar**.

Se você selecionar o grupo de sincronização, verá que possui agora um **ponto de extremidade de nuvem**.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto de extremidade do servidor
Um ponto de extremidade do servidor representa um local específico em um servidor registrado, como uma pasta em um volume do servidor.

1. Para adicionar um ponto de extremidade do servidor, selecione o grupo de sincronização recém-criado e selecione **Adicionar ponto de extremidade do servidor**.

   ![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. No painel **Adicionar ponto de extremidade do servidor**, insira as seguintes informações para criar um ponto de extremidade do servidor:

   | | |
   | ----- | ----- |
   | Valor | DESCRIÇÃO |
   | **Servidor registrado** | O nome do servidor que você criou para este tutorial. Usamos *afsvm101918* neste tutorial |
   | **Caminho** | O caminho do Windows Server para a unidade que você criou para este tutorial. Em nosso exemplo, é *F:\filestosync*. |
   | **Disposição em camadas de nuvem** | Deixe desabilitada para este tutorial. |
   | **Espaço Livre do Volume** | Deixe em branco para este tutorial. |

1. Clique em **Criar**.

Os arquivos agora estão sincronizados entre o compartilhamento de arquivos do Azure e o Windows Server.

![Armazenamento do Azure sincronizado com êxito](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu as etapas básicas para ampliar a capacidade de armazenamento de um Windows Server usando a Sincronização de Arquivos do Azure. Siga este link para obter uma visão mais completa do planejamento de uma implantação da Sincronização de Arquivos do Azure.

> [!div class="nextstepaction"]
> [Planejar a implantação da Sincronização de Arquivos do Azure](./storage-sync-files-planning.md)