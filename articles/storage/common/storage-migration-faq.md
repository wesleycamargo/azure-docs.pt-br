---
title: "Perguntas frequentes sobre a migração do Armazenamento do Microsoft Azure | Microsoft Docs"
description: "Respostas para perguntas comuns sobre a migração do Armazenamento do Azure"
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas frequentes sobre a migração do Armazenamento do Azure

Este artigo responde perguntas frequentes sobre a migração do Armazenamento do Azure. 

## <a name="faq"></a>Perguntas frequentes

**Como criar um script para copiar arquivos de um contêiner para outro?**

Para copiar arquivos entre contêineres, use o AzCopy. Veja os exemplos a seguir:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

O AzCopy usa a [API do Blob de Cópia](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para realizar a cópia de cada arquivo no contêiner.  
  
Use qualquer máquina virtual ou máquina local com acesso à Internet para executar o AzCopy. Você também pode usar o Agendamento do Lote do Azure para fazer isso automaticamente, porém é mais complicado.  
  
O script de Automação destina-se à implantação do Azure Resource Manager, em vez da manipulação do conteúdo de armazenamento. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Há algum encargo para cópia de dados entre dois compartilhamentos de arquivo diferentes na mesma conta de armazenamento dentro da mesma região?**

Não. Não há encargos para esse processo.

**Como fazer backup de toda minha conta de armazenamento para outra conta de armazenamento?**

Não há opção para fazer backup direto de uma conta de armazenamento inteira. Mas você pode mover manualmente o contêiner nessa conta de armazenamento para outra conta usando o AzCopy ou o Gerenciador de Armazenamento. As etapas a seguir mostram a você como usar o AzCopy para mover o contêiner:  
 

1.  Instale a ferramenta de linha de comando [AzCopy](storage-use-azcopy.md). Essa ferramenta ajuda você a mover o arquivo VHD entre as contas de armazenamento.

2.  Após a instalação do AzCopy no Windows usando o instalador, abra uma janela do Prompt de Comando e navegue até a pasta de instalação do AzCopy em seu computador. Por padrão, o AzCopy está instalado em **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   Execute o comando a seguir para mover o contêiner. Substitua o texto pelo valor real.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - /source: forneça o URI da conta de armazenamento de origem (até o contêiner)  
        - /dest: forneça o URI da conta de armazenamento de destino (até o contêiner)  
        - /sourcekey: forneça a chave primária da conta de armazenamento de origem. Copie essa chave no portal selecionando a conta de armazenamento.  
        - /destkey: forneça a chave primária da conta de armazenamento de destino. Copie essa chave no portal selecionando a conta de armazenamento.

Após a execução desse comando, os arquivos do contêiner vão para a conta de armazenamento de destino.

**A CLI do AzCopy não funciona junto com a opção "Pattern" quando você copia de um blob do Azure para outro.**

Você pode copiar e editar o cmd do AzCopy diretamente, e verificar se o Pattern corresponde ao Source. Verifique também se os curingas **/S** estão em vigor. Para saber mais, veja [Parâmetros do AzCopy](storage-use-azcopy.md).

**Como mover dados de um contêiner de armazenamento para outro?**

Para fazer isso, siga estas etapas:

1.  Crie o contêiner (pasta) no blob de destino.

2.  Use o [Azcopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) para copiar o conteúdo do contêiner do blob original para o contêiner de um Blob diferente.

**Como criar um script do PowerShell para mover dados de um compartilhamento de arquivos do Azure para outro armazenamento do Azure?**

Use o AzCopy para mover os dados de um compartilhamento de arquivo do Azure para outro armazenamento do azure. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como carregar arquivos .csv grandes no armazenamento do Azure?**

Use o AzCopy para carregar arquivos .csv grandes no armazenamento do Azure. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Preciso mover os logs da "unidade D" para a minha conta de armazenamento do Azure todos os dias. Como faço para automatizar isso?**

Você pode usar o AzCopy e criar uma tarefa no Agendador de Tarefas. Carregue arquivos em uma conta de armazenamento do Azure usando um script em lotes do AzCopy. Para obter mais informações, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](../../cloud-services/cloud-services-startup-tasks.md).

**Como faço para mover minha conta de armazenamento entre as assinaturas?**

Use o AzCopy para mover suas conta de armazenamento entre as assinaturas. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover 10 TB de dados para um armazenamento em outra região?**

Use o AzCopy para mover os dados. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como copiar dados do local para o armazenamento do Azure?**

Use o AzCopy para copiar os dados. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover dados do local para o Serviço de Arquivos do Azure?**

Use o AzCopy para mover os dados. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mapear uma pasta de contêiner em uma máquina virtual?**

Use o Compartilhamento de arquivos do Azure.

**Como fazer backup do meu armazenamento de arquivos do Azure?**

Não há solução de backup. No entanto, os Arquivos do Azure também oferecem suporte à cópia assíncrona. Portanto, você pode copiar arquivos de um compartilhamento para outro compartilhamento (dentro da mesma conta de armazenamento, ou em outra) ou para um contêiner de blob (dentro da mesma conta de armazenamento, ou em outra).
Para saber mais, confira [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md).

**Como mover discos gerenciados para outra conta de armazenamento?**

Para fazer isso, siga estas etapas:

1.  Pare a máquina virtual à qual o disco gerenciado está conectado.

2.  Copie o VHD do disco gerenciado de uma área para outra executando o seguinte script do Azure PowerShell:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerenciado usando o arquivo VHD em outra região na qual você copiou o VHD. Para fazer isso, execute este script do Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Para saber mais sobre como implantar uma máquina virtual de um disco gerenciado, veja [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Como baixar cerca de 1 a 2 TB de dados do Portal do Azure?**

Use o AzCopy para baixar os dados. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como alterar o local secundário de uma conta de armazenamento para a região da Europa?**

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A seleção da região secundária tem base na região primária, e não pode ser alterada. Confira [Replicação do Armazenamento do Azure](storage-redundancy.md).

**Onde posso saber mais sobre o SSE (Criptografia do Serviço de Armazenamento) do Azure?**  
  
Confira os seguintes artigos:

-  [Guia de segurança do Armazenamento do Azure](storage-security-guide.md)

-   [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](storage-service-encryption.md)

**Como mover ou baixar dados de uma conta de armazenamento?**

Use o AzCopy para baixar os dados. Para saber mais, veja [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).


**Como posso criptografar dados em uma conta de armazenamento?**

Depois que você habilita a criptografia na conta de armazenamento, os dados existentes não são criptografados. Para criptografar os dados existentes, faça o upload novamente na conta de armazenamento.  Para fazer isso, siga estas etapas:

Use o AZcopy para copiar os dados em uma conta de armazenamento diferente e depois voltar para uma conta de armazenamento. Também é possível usar a [Criptografia em Repouso](storage-service-encryption.md).

**Como posso baixar um VHD em uma máquina local, além de usar a opção de download no portal?**

Use o [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) para baixar um VHD.

**Há pré-requisitos para alterar a replicação de uma conta de armazenamento do GRS para LRS?**

Não. 

**Como acessar o armazenamento com redundância dos Arquivos do Azure?**

RA-GRS (Armazenamento com redundância geográfica com acesso de leitura) é necessário para acessar o armazenamento redundante. No entanto, os Arquivos do Azure são suporte apenas a LRS e GRS padrão que não permite acesso somente leitura. 

**Como mover do Armazenamento Premium para o Armazenamento Standard?**

Para fazer isso, siga estas etapas:

1.  Crie uma nova Conta de Armazenamento (ou use uma conta de Armazenamento Standard em sua assinatura).

2.  Faça o download do AzCopy. Execute um dos seguintes comandos do AzCopy.
      
    Para copiar os discos inteiros na conta de armazenamento:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Para copiar somente um disco, forneça o nome do disco no padrão

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Talvez demore várias horas para completar a operação.

Para certificar-se de que a transferência foi concluída com êxito, examine o contêiner da conta de armazenamento de destino no Portal do Azure. Após os discos serem copiados para a conta de armazenamento padrão, você poderá anexá-los à máquina virtual como um disco existente. Para saber mais, confira [Como anexar um disco de dados gerenciados a uma VM do Windows no Portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Como converter um compartilhamento de arquivos em Armazenamento Premium?**

O Armazenamento Premium não é permitido no Compartilhamento de Arquivos do Azure.

**Como atualizar de uma conta de Armazenamento Standard para Armazenamento Premium? Como fazer downgrade de uma conta de Armazenamento Premium para Armazenamento Standard?**

- Você deve criar a conta de armazenamento de destino, copiar os dados da conta de origem para a conta de destino e, depois, excluir a conta de origem.

- Você pode usar uma ferramenta como o AzCopy para executar a cópia dos dados.

- Se, além disso, você tiver máquinas virtuais, será necessário executar várias etapas adicionais antes de migrar os dados da conta de armazenamento. Para saber mais, confira [Migrar para o Armazenamento Premium do Azure (Discos não gerenciados)](storage-migration-to-premium-storage.md).

**Como mover de uma conta de armazenamento clássico para uma conta de Armazenamento do Azure Resource Manager?**

1.  Use o cmdlet Move-AzureStorageAccount.

2.  Esse cmdlet tem várias etapas (Validar, Preparar, Confirmar) e você pode validar a movimentação antes de fazê-la.

3.  Se, além disso, você tiver máquinas virtuais, será necessário executar várias etapas adicionais antes de migrar os dados da conta de armazenamento. Para saber mais, confira [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como fazer download dos dados para um computador baseado em Linux, de uma conta de armazenamento do Azure, ou carregar dados de um computador com Linux?**

Use a CLI do Azure.

-   Baixar um blob individual

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Carregar um blob individual: 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Como posso dar a outras pessoas acesso aos meus recursos de armazenamento?**

Para dar a outras pessoas acesso aos meus recursos de armazenamento:

-   Use um token SAS (Assinatura de acesso compartilhado) para fornecer acesso a um recurso. 

-   Forneça a um usuário a chave primária ou secundária da conta de armazenamento. Para saber mais, confira [Gerenciar sua conta de armazenamento](storage-create-storage-account.md#manage-your-storage-account).

-   Altere a política de acesso para permitir o acesso anônimo. Para saber mais, confira [Conceder permissões de usuário anônimo a contêineres e blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde o AzCopy é instalado?**

-   Se você acessar o AzCopy da "linha de comando do Armazenamento do Microsoft Azure", digite 'AzCopy'. A linha de comando é instalada junto com o AzCopy.

-   Se você tiver instalado a versão de 32 bits, ela estará aqui: **%ProgramFiles(x86)%\\SDKs do Microsoft\\Azure\\AzCopy.**

-   Se você tiver instalado a versão de 64 bits, ela estará aqui: **%ProgramFiles%\\SDKs do Microsoft\\Azure\\AzCopy**.

**Para uma conta de armazenamento replicada (por exemplo, ZRS, GRS ou RA-GRS), como acessar dados armazenados na região secundária?**

-   Se você estiver usando ZRS (Armazenamento com Redundância de Zona) ou GRS (Armazenamento com Redundância Geográfica), não poderá acessar dados da região secundária, a menos que ocorra um failover. Para saber mais sobre o processo de failover, veja [O que esperar se ocorrer um failover de armazenamento](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Se você estiver usando **RA-GRS** (Armazenamento com redundância geográfica com acesso de leitura), poderá acessar dados da região secundária a qualquer momento. Para tal, use um dos seguintes métodos:  
      
    AzCopy: acrescente '-secondary' ao nome da Conta de Armazenamento na URL para acessar o ponto de extremidade secundário. Por exemplo:  
     
    https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    Token SAS: use um Token SAS para acessar dados do ponto de extremidade. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-dotnet-shared-access-signature-part-1.md) para saber mais.

**Como usar um domínio personalizado de HTTPS com minha Conta de Armazenamento? Por exemplo, como fazer "https://mystorageaccountname.blob.core.windows.net/images/image.gif" aparecer como "https://www.contoso.com/images/image.gif"?**

No momento, o SSL não é compatível com Contas de Armazenamento com domínios personalizados.
Mas você pode usar domínios personalizados não HTTPS. Para saber mais, confira [Configurar um nome de domínio personalizado para seu ponto de extremidade do Armazenamento de Blobs](../blobs/storage-custom-domain-name.md).

**Como usar o FTP para acessar dados que estão em uma Conta de Armazenamento?**

Não é possível acessar uma conta de armazenamento diretamente por meio de FTP. No entanto, você pode configurar uma máquina virtual do Azure e, depois, instalar um servidor FTP na máquina virtual. Você pode fazer com que o servidor FTP armazene arquivos em um compartilhamento de Arquivos do Azure ou em um disco de dados que está disponível para a máquina virtual.
Se você quiser só baixar os dados sem a necessidade de usar o Gerenciador de Armazenamento ou um aplicativo semelhante, talvez seja possível usar um token SAS. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-dotnet-shared-access-signature-part-1.md) para saber mais.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.