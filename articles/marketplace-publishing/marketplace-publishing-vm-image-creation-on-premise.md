<properties
   pageTitle="Criar uma imagem de máquina virtual local para o Azure Marketplace | Microsoft Azure"
   description="Entenda e execute as etapas para criar uma imagem de VM local e implantar no Azure Marketplace para outras pessoas comprarem."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace-publishing"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/08/2015"
  ms.author="hascipio; v-divte"/>

# Desenvolver uma imagem de máquina virtual local para o Azure Marketplace
É altamente recomendável que você desenvolva VHDs do Azure diretamente na nuvem usando o RDP. No entanto, se for necessário, é possível baixar um VHD e desenvolver usando a infraestrutura local.

Para o desenvolvimento local, você deve baixar o VHD do sistema operacional (SO) da VM criada. Essas etapas podem ocorrer como parte da etapa 3.3 acima.

## Baixar uma imagem VHD
### Localizar um URL de Blob
Para baixar o VHD, primeiro localize a URL de blob do disco do SO.

Localize a URL de Blob do novo [Portal do Microsoft Azure](https://ms.portal.azure.com):

1.	Vá para **Procurar > VMs**, e selecione a VM implantada.
2.	Em **Configurar**, selecione o bloco **Discos** abrindo a folha Discos.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)

3.	Selecione o **Disco do SO**. Isso abrirá outra folha exibindo as propriedades do disco, incluindo o local do VHD.
4.	Copie a URL do blob.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)

5.	Agora, exclua a VM implantada sem excluir os discos de backup. Também é possível parar a VM em vez de excluí-la. Não baixe o VHD do SO quando a VM estiver em execução.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### Baixar o VHD
Quando você souber a URL de blob, você pode baixar o VHD usando o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) ou o PowerShell.
> [AZURE.NOTE]No momento da criação deste guia, a funcionalidade para baixar um VHD ainda não está presente no novo Portal do Microsoft Azure.

**Baixar o VHD do SO a partir do antigo [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/)**

1.	Se você ainda não fez isso, entre no Portal de Gerenciamento do Azure.
2.	Clique na guia **Armazenamento**.
3.	Selecione a **Conta de Armazenamento** na qual o VHD está armazenado.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)

4.	Isso abrirá as propriedades da Conta de Armazenamento. Selecione a guia Contêineres.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)

5.	Selecione o contêiner no qual o VHD está armazenado. Por padrão, quando criado a partir do Portal, o VHD será armazenado em um contêiner de vhds.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)

6.	Selecione o VHD do SO correto comparando com a URL daquele que você salvou.
7.	Clique em **Download**.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### Baixar o VHD usando o PowerShell
Além do Portal de Gerenciamento do Azure, você pode usar o cmdlet [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) para baixar o VHD do SO:

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
e.g., Save-AzureVhd -Source "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" -LocalFilePath "C:\\Users\\Administrator\\Desktop\\baseimagevm.vhd" -StorageKey <String>

> [AZURE.NOTE]*Save-AzureVhd* também tem uma opção de NumberOfThreads que pode ser usada para aumentar o paralelismo para fazer o melhor uso da largura de banda disponível para download.

## Carregar VHDs para uma conta de armazenamento do Azure
Se você preparou seus VHDs no local, precisará carregá-los em uma conta de armazenamento no Azure. Esta etapa deve ocorrer depois de criar o VHD local, mas antes de obter a certificação para a imagem VM.

### Criar uma conta e um contêiner de armazenamento
Conforme mencionado anteriormente, é recomendável carregar os VHDs em uma conta de armazenamento em uma região dos Estados Unidos. Todos os VHDs para uma única SKU devem ser colocados em um único contêiner dentro de uma única conta de armazenamento.

Para criar uma conta de armazenamento, você pode usar o [Portal do Microsoft Azure](https://portal.azure.com/), o PowerShell ou a ferramenta de linha de comando do Linux.

**Criar uma conta de armazenamento a partir do Portal do Microsoft Azure**

1.	Clique em **Novo**.
2.	Selecione **Armazenamento**.
3.	Preencha o nome da conta de armazenamento e selecione o local.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)

4.	Clique em **Criar**.
5.	A folha da conta de armazenamento criada deve estar aberta. Se não, selecione **Procurar > Contas de Armazenamento**. Na folha **Conta de Armazenamento**, selecione a conta de armazenamento criada.
6.	Selecione **Contêineres**.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img09.png)

7.	Na folha **Contêineres**, selecione **Adicionar** e insira um nome de contêiner e as permissões do contêiner. Selecione **Particular** para permissões do contêiner.

> [AZURE.TIP]É recomendável criar um contêiner por SKU que você planeja publicar.

  ![desenho](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### Criar uma conta de armazenamento usando o PowerShell
Usando o PowerShell, crie uma conta de armazenamento usando o cmdlet [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx):

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Em seguida, você pode criar um contêiner nessa conta de armazenamento usando o cmdlet [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx):

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [AZURE.NOTE]Esses comandos pressupõem que o contexto de conta de armazenamento atual já foi definido no PowerShell. Consulte [Configurar o Azure PowerShell](marketplace-publishing-powershell-setup.md) para obter mais detalhes sobre a instalação do PowerShell.
### Criar uma conta de armazenamento usando a ferramenta de linha de comando para Mac e Linux
A partir da [ferramenta de linha de comando do Linux](../virtual-machines/command-line-tools/), crie uma conta de armazenamento da seguinte maneira:

        azure storage account create mystorageaccount --location "West US"

Crie um contêiner conforme o seguinte:

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## Carregar VHDs
Depois de criar a conta de armazenamento e o contêiner, você pode carregar seus VHDs preparados. É possível usar o PowerShell, a ferramenta de linha de comando do Linux ou outras ferramentas de gerenciamento de armazenamento do Azure.

### Carregar um VHD por meio do PowerShell
Use o cmdlet [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx):

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### Carregar um VHD usando a ferramenta de linha de comando para Mac e Linux
Com a [ferramenta de linha de comando do Linux](../virtual-machines/command-line-tools/), use o seguinte: azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## Consulte também
- [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)
- [Configurando o Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=Oct15_HO3-->