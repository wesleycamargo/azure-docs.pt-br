---
title: Implantar o StorSimple Virtual Array 1 - Preparação do portal
description: O primeiro tutorial para implantar o StorSimple Virtual Array envolve a preparação do portal
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2016
ms.author: alkohli

---
# Implantar o StorSimple Virtual Array – Preparar o portal
![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Visão geral
Este artigo se aplica ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual local StorSimple ou dispositivo virtual StorSimple) que executa a versão GA (disponibilidade geral) de março de 2016. É o primeiro artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI. Este artigo descreve a preparação necessária para criar e configurar o serviço StorSimple Manager antes do provisionamento de uma matriz virtual. Também tem links para uma lista de verificação da configuração da implantação, assim como para pré-requisitos de configuração.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você examine a lista de verificação de configuração da implantação antes de começar. A preparação do portal levará menos de 10 minutos.

As informações publicadas neste artigo se aplicam à implantação de Matrizes virtuais do StorSimple no portal clássico do Azure, bem como na Nuvem de governo do Microsoft Azure.

### Introdução
O fluxo de trabalho de implantação consiste em preparação do portal, provisionamento de uma matriz virtual no seu ambiente virtualizado e conclusão da instalação. Para começar com a implantação do StorSimple Virtual Array como um servidor de arquivos ou um servidor iSCSI, você precisará consultar os seguintes recursos tabulados (artigos e vídeos).

#### Artigos sobre implantação
Consulte os artigos a seguir na sequência prescrita para implantar seu StorSimple Virtual Array.

| **#** | **Nesta etapa** | **O que você fará…** | **Use estes documentos.** |
| --- | --- | --- | --- |
| 1\. |**Configurar o portal clássico do Azure** |Crie e configure o serviço StorSimple Manager antes do provisionamento de um dispositivo virtual StorSimple. |[Preparar o portal](storsimple-ova-deploy1-portal-prep.md) |
| 2\. |**Provisionar o Virtual Array** |Para o Hyper-V, provisione e conecte-se a um dispositivo virtual StorSimple em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para o VMware, provisione e se conecte ao dispositivo virtual local StorSimple em um sistema de host que executa o VMware ESXi 5.5 e acima.<br></br> |[Provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Provisionar uma matriz virtual no VMware](storsimple-ova-deploy2-provision-vmware.md) |
| 3\. |**Configurar o Virtual Array** |Para seu servidor de arquivos, execute a configuração inicial, registre seu servidor de arquivos do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar compartilhamentos SMB. <br></br> <br></br> Para o servidor iSCSI, realize a configuração inicial, registre seu servidor iSCSI do StorSimple e conclua a configuração do dispositivo. Em seguida, é possível provisionar volumes iSCSI. |[Configurar a matriz virtual como um servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md) |

#### Vídeos de implantação
| **Para realizar esta etapa...** | **Assista a este vídeo.** |
| --- | --- |
| Instruções passo a passo para começar a usar o StorSimple Virtual Array. |[Introdução ao StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/) |
| Instruções passo a passo para provisionar um StorSimple Virtual Array no Hyper-V. |[Criar um StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
| Instruções passo a passo para configurar e registrar um StorSimple Virtual Array |[Configurar um StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/) |
| Instruções passo a passo para criar compartilhamentos, fazer backup de compartilhamentos e restaurar dados em um StorSimple Virtual Array configurado como um servidor de arquivos |[Usar o StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/) |
| Instruções passo a passo para failover e recuperação de desastre de um StorSimple Virtual Array |[Recuperação de desastre do StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/) |

Agora você pode começar a configurar o portal clássico do Azure.

## Lista de verificação da configuração
A lista de verificação da configuração a seguir descreve as informações que você precisa coletar antes de configurar o software em seu dispositivo StorSimple. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente. Dependendo se o dispositivo virtual StorSimple será implantado como um servidor de arquivos ou um servidor iSCSI, você precisará de uma das listas de verificação a seguir.

* Baixe a [Lista de verificação da configuração do servidor de arquivos do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Baixe a [Lista de verificação da configuração do servidor iSCSI do StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Pré-requisitos
Aqui você encontrará os pré-requisitos de configuração para seu serviço StorSimple Manager, seu dispositivo virtual StorSimple e a rede de datacenter.

### Para o serviço StorSimple Manager
Antes de começar, verifique se:

* Você tem sua conta da Microsoft com credenciais de acesso.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.
* Sua assinatura do Microsoft Azure deve estar habilitada para o serviço StorSimple Manager.

### Para o dispositivo virtual StorSimple
Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5.5 ou posterior) que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:
  
  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### Para a rede de datacenter
Antes de começar, verifique se:

* A rede em seu datacenter está configurada de acordo com os requisitos de rede para seu dispositivo StorSimple. Para obter mais informações, veja os [Requisitos do sistema do StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Seu dispositivo virtual StorSimple tem uma largura de banda de Internet dedicada de 5 Mbps (ou mais) disponível sempre. Essa largura de banda não deve ser compartilhada com outros aplicativos.

## Preparação passo a passo
Use as seguintes instruções passo a passo para preparar seu portal para o serviço StorSimple Manager.

## Etapa 1: Criar um novo serviço
Uma única instância do serviço StorSimple Manager pode gerenciar vários dispositivos StorSimple 1200. Execute as seguintes etapas para criar uma nova instância do serviço StorSimple Manager. Se tiver um serviço StorSimple Manager existente para gerenciar os dispositivos 1200, ignore esta etapa e vá para a [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [!IMPORTANT]
> Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço.
> 
> * Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key).
> 
> 

## Etapa 2: Obter a chave de registro do serviço
Depois que o serviço StorSimple Manager estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple com o serviço.

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/).

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [!NOTE]
> A chave de registro de serviço é usada para registrar todos os dispositivos do StorSimple Manager que precisam se registrar no serviço StorSimple Manager.
> 
> 

## Etapa 3: Baixar a imagem de dispositivo virtual
Depois que tiver a chave de registro de serviço, você precisará baixar a imagem do dispositivo virtual apropriada para provisionar um dispositivo virtual no sistema de host. As imagens de dispositivo virtual são específicas do sistema operacional e podem ser baixadas na página de Início Rápido no portal clássico do Azure.

> [!IMPORTANT]
> O software em execução no StorSimple Virtual Array só pode ser usado em conjunto com o serviço do StorSimple Manager.
> 
> 

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/).

#### Para obter a imagem de dispositivo virtual
1. Na página **Serviço StorSimple Manager**, clique no serviço que você criou. Isso o levará para a página de **Início Rápido**. (Você pode clicar no ícone de início rápido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) para acessar a página **Início Rápido** a qualquer momento.)
2. Clique no link correspondente à imagem que você deseja baixar do Centro de Download da Microsoft. Os arquivos de imagem têm aproximadamente 4,8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e posterior
   * VHD para Hyper-V no Windows Server 2008 R2 e posterior
   * VMDK para VMWare ESXi 5.5 e posterior
3. Baixe e descompacte o arquivo em uma unidade local, observando onde o arquivo descompactado está localizado.

![ícone de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Vídeo disponível**

Assista ao vídeo com instruções passo a passo para começar a usar o StorSimple Virtual Array.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Get-Started-with-the-StorSimple-Virtual-Array/player]
> 
> 

## Etapa opcional: Configurar uma nova conta de armazenamento para o serviço
Esta é uma etapa opcional que precisa ser executada somente se você não tiver habilitado a criação automática de uma conta de armazenamento com seu serviço.

Se precisar criar uma conta de armazenamento do Azure em uma região diferente, veja [Como criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute as etapas a seguir no [Portal Clássico do Azure](https://manage.windowsazure.com/) na página do serviço do StorSimple Manager para adicionar uma conta de armazenamento do Microsoft Azure existente.

#### Para adicionar uma conta de armazenamento
1. Na página inicial do serviço StorSimple Manager, selecione seu serviço e clique duas vezes nele. Isso o levará para a página de **Início Rápido**. Selecione a página **Configurar**.
2. Clique em **Adicionar/editar conta de armazenamento**. Na caixa de dialogo **Adicionar/Editar conta de armazenamento**, faça o seguinte:
   
   1. Clique em **Adicionar nova**.
   2. Forneça um nome para sua conta de armazenamento.
   3. Forneça a **Chave de Acesso** primária para sua conta de armazenamento do Microsoft Azure.
   4. Selecione **Habilitar modo SSL** para criar um canal seguro para a comunicação de rede entre o dispositivo e a nuvem. Desmarcar a caixa de seleção **Habilitar modo SSL** somente se você estiver operando em uma nuvem privada.
   5. Clique no ícone de verificação ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Você será notificado depois que a conta de armazenamento tiver sido criada com êxito.
      
      ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)
3. A conta de armazenamento recém-criada será exibida na página **Configurar** em **Contas de armazenamento**. Clique em **Salvar** para salvar a conta de armazenamento recém-criada. Clique em **OK** quando solicitado para confirmar.

## Próxima etapa
A próxima etapa é provisionar uma máquina virtual para o dispositivo virtual StorSimple. Dependendo do sistema operacional host, consulte as instruções detalhadas em:

* [Provisionar um StorSimple Virtual Array no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)
* [Provisionar um StorSimple Virtual Array no VMware](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0601_2016-->