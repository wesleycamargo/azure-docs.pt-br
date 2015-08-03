<properties 
   pageTitle="Implantação do seu dispositivo StorSimple local"
   description="Etapas e práticas recomendadas para implantar o dispositivo e serviço StorSimple Atualização 1."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/08/2015"
   ms.author="alkohli" />

# Implantação do seu dispositivo StorSimple local

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## Visão geral

Bem-vindo à implantação do dispositivo Microsoft Azure StorSimple.

Esses tutoriais de implantação se aplicam ao StorSimple série 8000 Atualização 1.0.

Essa série de tutoriais descreve como configurar seus dispositivos StorSimple e inclui uma lista de verificação de pré-instalação, pré-requisitos de configuração e etapas de configuração detalhadas.

> [AZURE.NOTE]As informações de implantação do StorSimple publicadas no site do Microsoft Azure e na Biblioteca MSDN se aplicam somente a dispositivos da série StorSimple 8000. Para obter informações completas sobre os dispositivos da série 7000, vá para: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações de implantação da série 7000, consulte o [Guia de Início Rápido do Sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

As informações nesses tutoriais pressupõem que você revisou as precauções de segurança e a lista de verificação de configuração e desembalou, instalou os cabos e colocou seu dispositivo StorSimple em um rack. Se você ainda precisa executar essas tarefas, vá para [Precauções de segurança](https://msdn.microsoft.com/library/azure/dn772366.aspx), [Lista de verificação de configuração](https://msdn.microsoft.com/library/azure/dn757787.aspx) e [Instalação de Hardware do dispositivo](https://msdn.microsoft.com/library/azure/dn772375.aspx), conforme apropriado.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. Recomenda-se que você revise a lista de verificação de pré-instalação antes de começar. O processo de implantação e configuração pode levar algum tempo para ser concluído.

## Lista de verificação de pré-instalação

A lista de verificação de pré-instalação a seguir descreve as informações que você precisa coletar antes de configurar o software em seu dispositivo StorSimple. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo StorSimple em seu ambiente.

| | Requisitos | Detalhes | Valores |
|---| --------------------- | ---------------------- | ------------- |
| 1 | Configurações de rede <ol><li>Interfaces de rede, 4 x 1 GbE, 2 x 10 GbE</li><li>Controlador IP fixo</li><li>Máscaras de sub-rede</li><li>Gateway</li></ol> | Total de IPs necessários: 8 <ol><li>Um por interface de rede habilitada, total 6</li><li>Um por controlador, total 2, exigido para conexão com a Internet para atualizações de serviço</li><li>Um para cada endereço IP</li><li>Um por dispositivo</li></ol> | |
| 2 | Acesso serial | Configuração inicial do dispositivo | Sim/Não |
| 3 | Endereços IP do servidor DNS | Necessário para conectar-se ao Microsoft Azure: total de 2 necessários para alta disponibilidade | |
| 4 | Endereços IP do servidor NTP | Necessário para sincronizar a hora com o Azure: 1 obrigatório, 1 opcional | |
| 5 | Servidor proxy (opcional) | Endereço IP/nome de domínio totalmente qualificado do servidor proxy, porta a ser usada | |
| 6 | Conta de armazenamento do Azure | Acessar credenciais como nome da conta e chave de acesso, por conta de armazenamento | |
| 7 | Chave de criptografia de armazenamento em nuvem (recomendado) | Por contêiner de volume | |
| 8 | IQN do host | Por host | |

## Pré-requisitos de implantação

As seções a seguir explicam os pré-requisitos de configuração para o seu serviço StorSimple Manager e o seu dispositivo StorSimple.

### Para o serviço StorSimple Manager

Antes de começar, verifique se:

- Você tem sua conta da Microsoft com credenciais de acesso.

- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

- Sua assinatura do Microsoft Azure está habilitada para o serviço StorSimple Manager. Sua assinatura deve ser comprada por meio do [Contrato Enterprise](http://azure.microsoft.com/pricing/enterprise-agreement/).

- Você tem acesso ao software de emulação de terminal como o PuTTY.

### Para o dispositivo no datacenter

Antes de configurar o dispositivo, verifique se:

- Seu dispositivo está totalmente desembalado conforme descrito em [Desembalando o seu dispositivo 8100](https://msdn.microsoft.com/library/azure/dn772327.aspx) ou [Desembalando o seu dispositivo 8600](https://msdn.microsoft.com/library/azure/dn772418.aspx).

- Seu dispositivo está montado em um rack conforme descrito em [Montagem em rack do seu dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757749.aspx) ou [Montagem em rack do seu dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757745.aspx).

- Seu dispositivo está totalmente cabeado para energia, rede e acesso serial conforme descrito em [Cabeamento do seu dispositivo 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) ou [Cabeamento do seu dispositivo 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

- As portas no firewall do seu datacenter são abertas para permitir tráfego de nuvem e iSCSI, conforme descrito em [Requisitos de rede para o dispositivo StorSimple](https://msdn.microsoft.com/library/dn772371.aspx).

## Etapas de implantação.

Siga estas etapas necessárias para configurar seu dispositivo StorSimple e conectá-lo ao serviço StorSimple Manager:

- Etapa 1: Criar um novo serviço 
- Etapa 2: Obter a chave de registro do serviço
- Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple 
- Etapa 4: Concluir a instalação mínima do dispositivo
- Etapa 5: Criar um contêiner de volume 
- Etapa 6: Criar um volume
- Etapa 7: Montar, inicializar e formatar um volume 
- Etapa 8: Fazer um backup

Além das etapas necessárias, há algumas etapas opcionais que você talvez precise concluir para implantar a sua solução. Estas etapas opcionais explicam como:

- Configurar uma nova conta de armazenamento para o serviço
- Usar o PuTTY para conectar-se ao console serial do dispositivo
- Obter o IQN de um host do Windows Server
- Criar um backup manual
- Configurar o MPIO

As instruções passo a passo de implantação indicam quando você deve executar cada uma destas etapas opcionais.

## Etapa 1: Criar um novo serviço

Um serviço StorSimple Manager pode gerenciar vários dispositivos StorSimple. Execute as seguintes etapas para criar uma nova instância do serviço StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT]Se você não ativou a criação automática de uma conta de armazenamento com seu serviço, você precisará criar pelo menos uma conta de armazenamento depois que você criou com êxito um serviço. Esta conta de armazenamento será usada quando você criar um contêiner de volume.
>
> * Se você não tiver criado uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas. 
> * Se você habilitou a criação automática de uma conta de armazenamento, vá para [Etapa 2: Obter a chave de registro do serviço](#step-2:-get-the-service-registration-key).

## Etapa 2: Obter a chave de registro do serviço

Depois que o serviço StorSimple Manager estiver em execução, será necessário obter a chave de registro do serviço. Essa chave é usada para registrar e conectar o seu dispositivo StorSimple com o serviço.

Execute as seguintes etapas no Portal de Gerenciamento.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple

Use o Windows PowerShell para StorSimple para concluir a configuração inicial do seu dispositivo StorSimple, conforme explicado no procedimento a seguir. Você precisará usar o software de emulação de terminal para concluir esta etapa. Para obter mais informações, consulte [Use o PuTTY para conectar-se ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Etapa 4: Concluir a instalação mínima do dispositivo

Para a configuração mínima de dispositivo do seu dispositivo StorSimple, é necessário:

- Configurar o servidor DNS secundário.
- Habilitar o iSCSI em pelo menos uma interface de rede.
- Atribuir endereços IP fixos para ambos os controladores.

Execute as seguintes etapas no Portal de Gerenciamento para concluir a configuração mínima do dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Etapa 5: Criar um contêiner de volume

Um contêiner de volume tem a conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidos nele. Você precisará criar um contêiner de volume antes de começar a provisionar volumes em seu dispositivo StorSimple.

Execute as seguintes etapas no Portal de Gerenciamento para criar um contêiner de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Etapa 6: Criar um volume

Depois de criar um contêiner de volume, você pode provisionar um volume de armazenamento no dispositivo StorSimple para seus servidores. Execute as seguintes etapas no Portal de Gerenciamento para criar um volume.

> [AZURE.IMPORTANT]O StorSimple do Azure pode criar somente volumes pequenos de provisionamento. Não é possível criar volumes parcialmente ou totalmente provisionados em um sistema StorSimple do Azure.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Etapa 7: Montar, inicializar e formatar um volume

Execute estas etapas no host do Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Etapa 8: Fazer um backup

Backups oferecem proteção pontual de volumes e melhoram a capacidade de recuperação, minimizando os tempos de restauração. Você pode executar dois tipos de backup em seu dispositivo StorSimple: instantâneos locais e instantâneos em nuvem. Cada um desses tipos de backup pode ser **Agendado** ou **Manual**.

Execute as seguintes etapas no Portal de Gerenciamento para criar um backup agendado.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Você pode fazer um backup manual a qualquer momento. Para saber os procedimentos, vá para [Criar um backup manual](#create-a-manual-backup).

## Configurar uma nova conta de armazenamento para o serviço

Esta é uma etapa opcional que você precisa executar somente se não tiver ativado a criação automática de uma conta de armazenamento com o seu serviço. É necessária uma conta de armazenamento do Microsoft Azure para criar um contêiner de volume StorSimple.

Se você precisar criar uma conta de armazenamento do Azure em uma região diferente, consulte [Sobre Contas de Armazenamento do Azure](../storage/storage-create-storage-account.md) para obter instruções passo a passo.

Execute as seguintes etapas no Portal de Gerenciamento, na página **Serviço StorSimple Manager**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Usar o PuTTY para conectar-se ao console serial do dispositivo

Para se conectar ao Windows PowerShell para StorSimple, você precisa usar um software de emulação de terminal como o PuTTY. Você pode usar o PuTTY ao acessar o dispositivo diretamente através do console serial ou abrindo uma sessão de telnet a partir de um computador remoto.

[AZURE.INCLUDE [Use o PuTTY para conectar-se ao console serial do dispositivo](../../includes/storsimple-use-putty.md)]

## Obter o IQN de um host do Windows Server

Execute as seguintes etapas para obter o iSCSI IQN (Nome Qualificado) de um host do Windows que está executando o Windows Server ® 2012.

[AZURE.INCLUDE [Criar um backup manual](../../includes/storsimple-get-iqn.md)]

## Criar um backup manual

Execute as seguintes etapas no Portal de Gerenciamento para criar um backup manual sob demanda para um único volume em seu dispositivo StorSimple.

[AZURE.INCLUDE [Criar um backup manual](../../includes/storsimple-create-manual-backup.md)]

## Configurar o MPIO

Multipath I/O (MPIO) é um recurso opcional e não é instalado no Windows Server por padrão. Ele deve ser instalado como um recurso por meio do Gerenciador de Servidor.

> [AZURE.NOTE]Não há suporte para MPIO em um dispositivo virtual StorSimple.

Para obter instruções de instalação de MPIO, vá para [Configurar MPIO para o seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

## Próximas etapas

Configurar um [dispositivo virtual](storsimple-virtual-device.md).

Use o [Serviço StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) para gerenciar o seu dispositivo StorSimple.
 

<!---HONumber=July15_HO4-->