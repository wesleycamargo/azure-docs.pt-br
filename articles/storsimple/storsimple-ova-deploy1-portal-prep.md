<properties
   pageTitle="Implantar o StorSimple Virtual Array 1 - Preparação do portal"
   description="O primeiro tutorial para implantar o StorSimple Virtual Array envolve a preparação do portal"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="alkohli"/>

# Implantar o StorSimple Virtual Array - Preparar o portal (visualização)

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Introdução 

Este artigo se aplica à Matriz Virtual Microsoft Azure StorSimple (também conhecido como o dispositivo virtual local StorSimple ou o dispositivo virtual StorSimple) executando somente a v.1.1.1.0 (Visualização Pública). É o primeiro artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI. Este artigo descreve a preparação necessária para criar e configurar o serviço StorSimple Manager antes do provisionamento de uma matriz virtual. Também tem links para uma lista de verificação da configuração da implantação, assim como para pré-requisitos de configuração.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você examine a lista de verificação de configuração da implantação antes de começar. A preparação do portal levará menos de 10 minutos.

As informações de implantação do StorSimple publicadas neste artigo se aplicam somente ao StorSimple Virtual Array.

> [AZURE.IMPORTANT]
> 
> Esta visualização pública é destinada apenas para fins de planejamento da implantação e avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção.

### Introdução

O fluxo de trabalho de implantação consiste em preparação do portal, provisionamento de uma matriz virtual no seu ambiente virtualizado e conclusão da instalação. Para começar com a implantação do StorSimple Virtual Array como um servidor de arquivos ou um servidor iSCSI, você precisará consultar os seguintes recursos tabulados (artigos e vídeos).

#### Artigos sobre implantação

Consulte os artigos a seguir na sequência prescrita para implantar seu StorSimple Virtual Array.

| **#** | **Nesta etapa** | **O que você fará…** | **Use estes documentos...**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Configurar o portal clássico do Azure** | Crie e configure o serviço StorSimple Manager antes do provisionamento de um dispositivo virtual StorSimple. |[Preparar o portal](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **Provisionar o Virtual Array** | Para Hyper-V, provisione e se conecte a um dispositivo virtual StorSimple em um sistema de host que executa Hyper-V 2008 R2, Hyper-V 2012 ou Hyper-V 2012 R2. <br></br> <br></br> Para VMware, provisione e se conecte ao dispositivo virtual local StorSimple em um sistema de host que executa o VMware ESXi 5.5 e acima.| [Provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Provisionar uma matriz virtual no VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **Configurar o Virtual Array** | Para seu servidor de arquivos, execute a configuração inicial, registre seu servidor de arquivos do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar compartilhamentos SMB. <br></br> <br></br> Para seu servidor iSCSI, execute a configuração inicial, registre seu servidor iSCSI do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar volumes iSCSI.| [Configurar a matriz virtual como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### Vídeos de implantação

| **Para realizar esta etapa...** | **Assista a este vídeo**|
|----------------|-------------|
| Instruções passo a passo para começar a usar o StorSimple Virtual Array. | [Introdução ao StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array)|
| Instruções passo a passo para provisionar um StorSimple Virtual Array no Hyper-V.|[Criar um StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array) |
|Instruções passo a passo para configurar e registrar um StorSimple Virtual Array|[Configurar um StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array)|
|Instruções passo a passo para criar compartilhamentos, fazer backup de compartilhamentos e restaurar dados em um StorSimple Virtual Array configurado como um servidor de arquivos|[Usar o StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array)|
|Instruções passo a passo para failover e recuperação de desastre de um StorSimple Virtual Array|[Recuperação de desastre do StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery)

Agora você pode começar a configurar o portal clássico do Azure.

## Lista de verificação da configuração

A lista de verificação da configuração a seguir descreve as informações que você precisa coletar antes de configurar o software em seu dispositivo StorSimple. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente. Dependendo se o dispositivo virtual StorSimple será implantado como um servidor de arquivos ou um servidor iSCSI, você precisará de uma das listas de verificação a seguir.

-   Baixe a [lista de verificação da configuração do servidor de arquivos do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Baixe a [lista de verificação da configuração do servidor iSCSI do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Pré-requisitos

Aqui você encontrará os pré-requisitos de configuração para seu serviço StorSimple Manager, seu dispositivo virtual StorSimple e a rede de datacenter.

### Para o serviço StorSimple Manager

Antes de começar, verifique se:

-   Você tem sua conta da Microsoft com credenciais de acesso.

-   Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

-   Sua assinatura do Microsoft Azure deve estar habilitada para o serviço StorSimple Manager.

### Para o dispositivo virtual StorSimple 

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host que executa o Hyper-V (2008 R2 ou superior) ou VMware (ESXi 5.5 ou posterior) que pode ser usado para provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:
	
	-   Um mínimo de quatro núcleos.
	
	-   Pelo menos 8 GB de RAM.
	
	-   Uma interface de rede.
	
	-   Um disco virtual de 500 GB para dados do sistema.

### Para a rede de datacenter 

Antes de começar, verifique se:

-   A rede em seu datacenter está configurada de acordo com os requisitos de rede para seu dispositivo StorSimple. Para obter mais informações, consulte os [Requisitos do sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md).

-   Seu dispositivo virtual StorSimple tem uma largura de banda de Internet dedicada de 5 Mbps (ou mais) disponível sempre. Essa largura de banda não deve ser compartilhada com outros aplicativos.

## Preparação passo a passo

Use as seguintes instruções passo a passo para preparar seu portal para o serviço StorSimple Manager.

## Etapa 1: Criar um novo serviço

Uma única instância do serviço StorSimple Manager pode gerenciar vários dispositivos StorSimple 1200. Execute as seguintes etapas para criar uma nova instância do serviço StorSimple Manager. Se você tiver um serviço StorSimple Manager existente para gerenciar os dispositivos 1200, ignore esta etapa e vá para a [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).

#### Para criar um novo serviço

1.  Usando suas credenciais de conta da Microsoft, faça logon no portal clássico do Azure na seguinte URL: <https://manage.windowsazure.com/>

2.  No portal, clique em **Novo > Serviços de Dados > StorSimple Manager > Criação rápida**.

3.  No formulário exibido, faça o seguinte:

	1.  Fornecer um **Nome** exclusivo para o serviço. Este é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.

	2.  Para um serviço gerenciar um dispositivo virtual StorSimple, na lista suspensa de **Tipo de dispositivos gerenciados**, escolha **Série do dispositivo virtual**.

	3.  Fornecer um **Local** para o serviço. Local refere-se à região geográfica em que você deseja implantar seu dispositivo.

	 -   Se você tiver outras cargas de trabalho no Azure que planeja implantar com seu dispositivo StorSimple, recomendamos o uso daquele datacenter.

   	 -   O StorSimple Manager e o armazenamento do Azure podem estar em dois locais separados. Nesse caso, é necessário criar a conta de armazenamento do Azure e do StorSimple Manager separadamente. Para criar uma conta de armazenamento do Azure, acesse o serviço Armazenamento do Azure no portal e siga as etapas em [Criar uma conta de Armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account). Após criar essa conta, adicione-a ao serviço StorSimple Manager seguindo as etapas em [Configurar uma nova conta de armazenamento para o serviço](#appendix-a-configure-a-new-storage-account-for-the-service).
   	 
   	 -   Observe que, na visualização, você pode criar o serviço StorSimple Manager somente no Oeste dos EUA e Leste do Japão.
	
	1.  Escolha uma **Assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo não estará presente quando você tem apenas uma assinatura.

	1.  Selecione **Criar uma nova conta de armazenamento do Azure** para criar automaticamente uma conta de armazenamento com o serviço. Essa conta de armazenamento terá um nome especial, como "storsimplebwv8c6dcnf". Se você precisar de seus dados em um local diferente, desmarque essa caixa de seleção.

	1.  Clique em **Criar StorSimple Manager** para criar o serviço.

		![](./media/storsimple-ova-deploy1-portal-prep/image1.png)

	Você será direcionado para a página inicial **Serviço**. A criação do serviço levará alguns minutos. Depois que o serviço for criado com êxito, você será notificado adequadamente.

	![](./media/storsimple-ova-deploy1-portal-prep/image2.png)

	O status do serviço será alterado para **Ativo**.

	![](./media/storsimple-ova-deploy1-portal-prep/image3.png)

> [AZURE.IMPORTANT]
> 
> Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço.
> 

> - Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#appendix-a-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> 

> - Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).


## Etapa 2: Obter a chave de registro do serviço


Depois que o serviço StorSimple Manager estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple com o serviço.

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/).

#### Para obter a chave de registro do serviço StorSimple

1.  Na página do **serviço StorSimple Manager**, clique na **Chave de registro** localizada na parte inferior.

2.  Você precisará aguardar alguns minutos enquanto a chave é recuperada. A caixa de diálogo **Chave de Registro do Serviço** é exibida.


	1.  Localize a **Chave de registro de serviço**.

	2.  Clique no ícone de copiar ![](./media/storsimple-ova-deploy1-portal-prep/image6.png) para copiar a chave e salvá-la para uso posterior.

> [AZURE.NOTE]
> 
> A chave de registro de serviço é usada para registrar todos os dispositivos do StorSimple Manager que precisam se registrar no serviço StorSimple Manager.

## Etapa 3: Baixar a imagem de dispositivo virtual

Depois que tiver a chave de registro de serviço, você precisará baixar a imagem do dispositivo virtual apropriada para provisionar um dispositivo virtual no sistema de host. As imagens de dispositivo virtual são específicas do sistema operacional e podem ser baixadas na página de Início Rápido no portal clássico do Azure.

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/).

#### Para obter a imagem de dispositivo virtual

1.  Na página **Serviço StorSimple Manager**, clique no serviço que você criou. Isso o levará para a página de **Início Rápido**. (Você pode clicar no ícone de início rápido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) para acessar a página **Início Rápido** a qualquer momento.)


1.  Baixe o VHD apropriado em um compartilhamento de rede no seu datacenter. VHDs separados estão disponíveis para:

	-   Hyper-V 2008 R2 e posterior

	-   VMWare ESXi 5.5 e posterior

1.  Clique na imagem para o sistema operacional do host que você usará para provisionar o dispositivo virtual. Isso levará você ao Centro de Download da Microsoft.

1.  Se usar o Hyper-V, baixe o VHD para Hyper-V 2008 R2 e acima. Se usar o VMware, baixe o VMDK. O VHD é um arquivo compactado de 4,77 GB e o VMDK é um arquivo de 4,75 GB. O tempo para baixar o arquivo depende da sua conexão de Internet.

2.  Descompacte o arquivo e anote o local descompactado em sua unidade local.

![ícone de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Vídeo disponível**

Assista ao vídeo com instruções passo a passo para começar a usar o StorSimple Virtual Array.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]

## Próxima etapa

A próxima etapa é provisionar uma máquina virtual para o dispositivo virtual StorSimple. Dependendo do sistema operacional host, consulte as instruções detalhadas em:

-   [Provisionamento do StorSimple Virtual Array no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Provisionamento do StorSimple Virtual Array no VMware](storsimple-ova-deploy2-provision-vmware.md)


## Apêndice A: Configurar uma nova conta de armazenamento para o serviço

Esta é uma etapa opcional que precisa ser executada somente se você não tiver habilitado a criação automática de uma conta de armazenamento com seu serviço.

Se você precisar criar uma conta de armazenamento do Azure em uma região diferente, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/) na página do serviço StorSimple Manager para adicionar uma conta de armazenamento do Microsoft Azure existente.

#### Para adicionar uma conta de armazenamento

1.  Na página inicial do serviço StorSimple Manager, selecione seu serviço e clique duas vezes nele. Isso o levará para a página de **Início Rápido**. Selecione a página **Configurar**.

2.  Clique em **Adicionar/editar conta de armazenamento**. Na caixa de dialogo **Adicionar/Editar conta de armazenamento**, faça o seguinte:

	1.  Clique em **Adicionar nova**.

	1.  Forneça um nome para sua conta de armazenamento.

	1.  Forneça a **Chave de Acesso** primária para sua conta de armazenamento do Microsoft Azure.

	1.  Selecione **Habilitar modo SSL** para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem. Desmarcar a caixa de seleção **Habilitar modo SSL** somente se você estiver operando em uma nuvem privada.

	1.  Clique no ícone de verificação ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Você será notificado depois que a conta de armazenamento tiver sido criada com êxito.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  A conta de armazenamento recém-criada será exibida na página **Configurar** em **Contas de armazenamento**. Clique em **Salvar** para salvar a conta de armazenamento recém-criada. Clique em **OK** quando solicitado para confirmar.

<!---HONumber=AcomDC_0121_2016-->