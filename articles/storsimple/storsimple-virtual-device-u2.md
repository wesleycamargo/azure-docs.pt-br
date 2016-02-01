<properties 
   pageTitle="Atualização 2 do dispositivo virtual do StorSimple | Microsoft Azure"
   description="Saiba como criar, implantar e gerenciar um dispositivo virtual StorSimple em uma rede virtual do Microsoft Azure. (Aplica-se ao StorSimple Atualização 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# Implantar e gerenciar um dispositivo virtual do StorSimple no Azure (Atualização 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-virtual-device-u2.md)
- [Update 1](../articles/storsimple/storsimple-virtual-device-u1.md)
- [GA Release](../articles/storsimple/storsimple-virtual-device.md)

##Visão geral
O dispositivo virtual StorSimple é um recurso adicional que acompanha a sua solução Microsoft Azure StorSimple. O dispositivo virtual StorSimple é executado em uma máquina virtual em uma rede virtual do Microsoft Azure e você pode usá-lo para fazer backup e clonar dados de seus hosts.


#### Comparação do modelo de dispositivo virtual

O dispositivo virtual do StorSimple está disponível em dois modelos, um 8010 padrão e um 8020 premium (introduzido na Atualização 2). Uma comparação dos dois modelos é mostrada na tabela abaixo.


| Modelo do dispositivo | 8010<sup>1</sup> | 8020 |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacidade máxima** | 30 TB | 64 TB |
| **VM do Azure** | Standard\_A3 (4 núcleos, 7 GB de memória) | Standard\_DS3 (4 núcleos, 14 GB de memória) |
| **Compatibilidade de versão** | Versões com pré-Atualização 2 ou posterior | Versões com Atualização 2 ou posterior |
| **Disponibilidade de região** | Todas as regiões do Azure | Regiões do Azure que dão suporte ao Armazenamento Premium<br></br>Para obter uma lista de regiões que, atualmente, dão suporte ao Armazenamento Premium, veja [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) |
| **Tipo de armazenamento** | Usa o Armazenamento do Azure Standard<br></br> Saiba como [criar uma conta de Armazenamento Standard]() | Usa o Armazenamento do Azure Premium<br></br>Saiba como [criar uma conta de Armazenamento Premium](storage-premium-storage-preview-portal.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk) |
| **Diretrizes sobre carga de trabalho** | Recuperação no nível de item de arquivos de backups | Cenários de desenvolvimento e teste na nuvem, baixa latência, cargas de trabalho de desempenho mais altas <br></br>Dispositivo secundário para recuperação de desastre |
 
<sup>1</sup> *Conhecido anteriormente como 1100*.


Este artigo descreve o processo passo a passo de implantação de um dispositivo virtual do StorSimple no Azure. Depois de ler este artigo, você irá:

- Entenda as diferenças entre o dispositivo virtual e o dispositivo físico.

- Esteja apto a criar e configurar o dispositivo virtual.

- Conecte-se ao dispositivo virtual.

- Saiba como trabalhar com o dispositivo virtual.

Este tutorial se aplica a todos os dispositivos virtuais do StorSimple que executam a Atualização 2 e superior.

## Como o dispositivo virtual difere do dispositivo físico

O dispositivo virtual StorSimple é uma versão somente de software do StorSimple, que é executado em um único nó em uma Máquina Virtual do Microsoft Azure. O dispositivo virtual dá suporte a cenários de recuperação de desastre em que o seu dispositivo físico não está disponível e é adequado para uso em recuperação no nível do item de cenários de back-ups, recuperação de desastre local e desenvolvimento e teste na nuvem.

#### Diferenças do dispositivo físico

A tabela abaixo mostra algumas das principais diferenças entre o dispositivo virtual do StorSimple e o dispositivo físico do StorSimple.

| | Dispositivo físico | Dispositivo virtual |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Localidade** | Reside no datacenter. | É executado no Azure. |
| **Interfaces de rede** | Tem seis interfaces de rede: DATA 0 a DATA 5. | Tem apenas uma interface de rede: DATA 0. |
| **Registro** | Registrado durante a etapa de configuração. | O registro é uma tarefa separada. |
| **Chave de criptografia de dados do serviço** | Gere novamente no dispositivo físico e, em seguida, atualize o dispositivo virtual com a nova chave. | Não pode gerar novamente do dispositivo virtual. |


## Pré-requisitos para o dispositivo virtual

As seções a seguir explicam os pré-requisitos de configuração para o dispositivo virtual do StorSimple. Antes de implantar um dispositivo virtual, examine as [considerações sobre segurança para uso de um dispositivo virtual](storsimple-security.md#storsimple-virtual-device-security).

#### Requisitos do Azure

Antes de provisionar o dispositivo virtual, você precisará fazer as seguintes preparações no seu ambiente do Azure:

- Para o dispositivo virtual, [configure uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Se usar o Armazenamento Premium, você deve criar uma rede virtual em uma região do Azure que dá suporte ao Armazenamento Premium. Mais informações sobre [regiões que, atualmente, dão suporte ao Armazenamento Premium](https://azure.microsoft.com/regions/#services).
- É aconselhável utilizar o servidor DNS padrão fornecido pelo Azure em vez de especificar o nome do seu próprio servidor DNS. Se o nome do servidor DNS não for válido ou se o servidor DNS não conseguir resolver endereços IP corretamente, a criação do dispositivo virtual falhará.
- Ponto a site e site a site são opcionais, mas não obrigatórios. Se desejar, você pode configurar essas opções para cenários mais avançados. 
- É possível criar [Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-about.md) (servidores de host) na rede virtual que podem usar os volumes expostos pelo dispositivo virtual. Esses servidores devem atender aos seguintes requisitos: 							
	- Ser VMs do Windows ou do Linux com software Iniciador iSCSI instalado.
	- Estar em execução na mesma rede virtual que o dispositivo virtual.
	- Ser capaz de se conectar ao destino iSCSI do dispositivo virtual por meio do endereço IP interno do dispositivo virtual.

- Verifique se você configurou o suporte para tráfego iSCSI e de nuvem na mesma rede virtual.


#### Requisitos do StorSimple

Faça as seguintes atualizações para o seu serviço do Azure StorSimple antes de criar um dispositivo virtual:


- Adicione [registros de controle de acesso](storsimple-manage-acrs.md) às máquinas virtuais que serão os servidores de host para seu dispositivo virtual.

- Use uma [conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account) na mesma região do dispositivo virtual. Contas de armazenamento em regiões diferentes podem resultar em baixo desempenho. Você pode usar uma conta de Armazenamento Standard ou Premium com o dispositivo virtual. Mais informações sobre como criar uma [conta de Armazenamento Standard]() ou uma [conta de Armazenamento Premium](storage-premium-storage-preview-portal.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Use uma conta de armazenamento para a criação do dispositivo virtual diferente da usada para seus dados. O uso da mesma conta de armazenamento pode resultar em baixo desempenho.

Certifique-se de ter as seguintes informações antes de começar:

- Sua conta do portal clássico do Azure com credenciais de acesso.

- Uma cópia da chave de criptografia de dados de serviço de seu dispositivo físico.


## Criar e configurar o dispositivo virtual

Antes de executar esses procedimentos, certifique-se de que você atendeu aos [pré-requisitos para o dispositivo virtual](#prerequisites-for-the-virtual-device).

Depois de criar uma rede virtual, configurar um serviço do StorSimple Manager e registrar seu dispositivo físico do StorSimple no serviço, você poderá usar as etapas a seguir para criar e configurar um dispositivo virtual do StorSimple.

### Etapa 1: criar um dispositivo virtual

Execute as seguintes etapas para criar o dispositivo virtual StorSimple.

[AZURE.INCLUDE [Criar um dispositivo virtual](../../includes/storsimple-create-virtual-device-u2.md)]


### Etapa 2: configurar e registrar o dispositivo virtual

Antes de iniciar este procedimento, verifique se você tem uma cópia da chave de criptografia de dados de serviço. A chave de criptografia de dados do serviço foi criada quando você configurou o primeiro dispositivo StorSimple e foi orientado a salvá-la em um local seguro. Se você não tiver uma cópia da chave de criptografia dos dados de serviço, deverá contatar o Suporte da Microsoft para obter assistência.

Execute as seguintes etapas para configurar e registrar o dispositivo virtual StorSimple[AZURE.INCLUDE [Configurar e registrar um dispositivo virtual](../../includes/storsimple-configure-register-virtual-device.md)]

### Etapa 3: (opcional) Modificar as definições de configuração do dispositivo

A seção a seguir descreve as definições de configuração de dispositivo necessárias para o dispositivo virtual do StorSimple se você desejar usar o CHAP, o StorSimple Snapshot Manager ou alterar a senha do Administrador do Dispositivo.

#### Configurar o iniciador CHAP

Este parâmetro contém as credenciais que o dispositivo virtual (destino) espera dos iniciadores (servidores) que estão tentando acessar os volumes. Os iniciadores fornecerão um nome de usuário CHAP e uma senha CHAP para identificá-los para o dispositivo durante essa autenticação. Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### Configurar o destino CHAP

Esse parâmetro contém as credenciais usadas pelo dispositivo virtual quando um iniciador compatível com CHAP solicita autenticação mútua ou bidirecional. Seu dispositivo virtual usará um nome de usuário CHAP Reverso e uma senha CHAP Reversa para identificar a si mesmo para o iniciador durante esse processo de autenticação. Observe que as configurações de destino de CHAP são configurações globais. Quando elas forem aplicadas, todos os volumes conectados ao dispositivo virtual de armazenamento usarão a autenticação CHAP. Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### Configurar a senha do Gerenciador de Instantâneos StorSimple

O software Gerenciador de Instantâneos StorSimple software reside no host Windows e permite que os administradores gerenciem backups do seu dispositivo StorSimple na forma de instantâneos locais e de nuvem.

>[AZURE.NOTE]Para o dispositivo virtual, seu host do Windows é uma máquina virtual do Azure.

Ao configurar um dispositivo no Gerenciador de Instantâneos StorSimple, você deverá fornecer o endereço IP do dispositivo StorSimple e a senha para autenticar o dispositivo de armazenamento. Para obter etapas detalhadas, vá para [Configurar senha do StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### Alterar a senha de administrador do dispositivo 

Quando você usar a interface do Windows PowerShell para acessar o dispositivo virtual, será solicitada a inserção de uma senha de administrador do dispositivo. Para a segurança de seus dados, será necessário alterar essa senha para que o dispositivo virtual possa ser usado. Para obter etapas detalhadas, vá para [Configurar senha de administrador do dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).

## Conectar-se remotamente ao dispositivo virtual
O acesso remoto ao seu dispositivo virtual por meio da interface do Windows PowerShell não está habilitado por padrão. Você precisa habilitar o gerenciamento remoto primeiro no dispositivo virtual e então habilitá-lo no cliente que será usado para acessar seu dispositivo virtual.

O processo de duas etapas para se conectar remotamente é detalhado abaixo.

### Etapa 1: configurar o gerenciamento remoto

Execute as etapas a seguir para configurar o gerenciamento remoto para seu dispositivo virtual StorSimple.

[AZURE.INCLUDE [Configurar o gerenciamento remoto via HTTP para o dispositivo virtual](../../includes/storsimple-configure-remote-management-http-device.md)]

### Etapa 2: acessar remotamente o dispositivo virtual

Depois de habilitar o gerenciamento remoto na página de configuração do dispositivo do StorSimple, você poderá usar a comunicação remota do Windows PowerShell para se conectar ao dispositivo virtual de outra máquina virtual na mesma rede virtual; por exemplo, é possível se conectar da VM host configurada e usada para conectar o iSCSI. Na maioria das implantações, você irá já terá aberto um ponto de extremidade público para acessar sua VM host que poderá ser usada para acessar o dispositivo virtual.

>[AZURE.WARNING]**Para maior segurança, é altamente recomendável que você use HTTPS durante a conexão com os pontos de extremidade e, em seguida, exclua os pontos de extremidade depois de concluir sua sessão remota do PowerShell.**

Você deve seguir os procedimentos em [Conectar-se remotamente ao seu dispositivo do StorSimple](storsimple-remote-connect.md) para configurar a comunicação remota de seu dispositivo virtual.

## Conectar-se diretamente ao dispositivo virtual

Você também pode se conectar diretamente ao dispositivo virtual. Se você quiser se conectar diretamente ao dispositivo virtual de outro computador fora da rede virtual ou do ambiente do Microsoft Azure, precisará criar pontos de extremidade adicionais, como descrito no procedimento a seguir.

Execute as seguintes etapas para criar o dispositivo virtual StorSimple.

[AZURE.INCLUDE [Criar pontos de extremidade públicos em um dispositivo virtual](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

É recomendável que você se conecte de outra máquina virtual dentro da mesma rede virtual porque essa prática minimiza o número de pontos de extremidade públicos em sua rede virtual. Quando você usar esse método, simplesmente se conectará à máquina virtual por meio de uma sessão de Área de Trabalho Remota e configurará essa máquina virtual para uso como o faria com qualquer outro cliente do Windows em uma rede local. Você não precisa anexar o número da porta pública porque a porta já será conhecida.

## Trabalhar com o dispositivo virtual StorSimple

Agora que você criou e configurado o dispositivo virtual StorSimple, está pronto para começar a trabalhar com ele. Você pode trabalhar com contêineres de volume, volumes e políticas de backup em um dispositivo virtual assim como faria em um dispositivo StorSimple físico; a única diferença é que você precisará certificar-se de selecionar o dispositivo virtual na sua lista de dispositivos. Veja [usar o serviço do StorSimple Manager para gerenciar um dispositivo virtual](storsimple-manager-service-administration.md) para obter procedimentos passo a passo das várias tarefas de gerenciamento para o dispositivo virtual.

As seções a seguir discutem as diferenças que você encontrará ao trabalhar com o dispositivo virtual.

### Manter um dispositivo virtual StorSimple

Como é um dispositivo somente de software, a manutenção para o dispositivo virtual é mínima quando comparada à manutenção do dispositivo físico. Você tem as seguintes opções:

- **Atualizações de software** – você pode exibir a data em que o software foi atualizado pela última vez, junto com quaisquer mensagens de status de atualização. Você pode usar o botão **Verificar atualizações** na parte inferior da página para executar uma verificação manual em busca de novas atualizações. Se houver atualizações disponíveis, clique em **Instalar Atualizações** para instalá-las. Como há apenas uma única interface do dispositivo virtual, isso significa que haverá uma ligeira interrupção do serviço quando as atualizações forem aplicadas. O dispositivo virtual será automaticamente desligado e reiniciará (se for necessário) para aplicar todas as atualizações liberadas. Para obter um procedimento passo a passo, vá para [atualizar o dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Pacote de suporte** – você pode criar e carregar um pacote de suporte para ajudar o Suporte da Microsoft a solucionar problemas com seu dispositivo virtual. Para obter um procedimento passo a passo, vá para [criar e gerenciar um pacote de suporte](storsimple-create-manage-support-package.md).

### Contas de armazenamento para um dispositivo virtual

As contas de armazenamento são criadas para uso pelo serviço Gerenciador do StorSimple, pelo dispositivo virtual e pelo dispositivo físico. Quando você cria suas contas de armazenamento, é recomendável que você use um identificador de região no nome amigável para ajudar a garantir que a região seja consistente em todos os componentes do sistema. Para um dispositivo virtual, é importante que todos os componentes estejam na mesma região para evitar problemas de desempenho.

Para obter um procedimento passo a passo, vá para [adicionar uma conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account).

### Desativar um dispositivo virtual StorSimple

Desativar um dispositivo virtual exclui a VM e os recursos criados quando ela foi provisionada. Depois que o dispositivo virtual for desativado, ele não poderá ser restaurado ao estado anterior. Antes de desativar o dispositivo virtual, pare ou exclua clientes e hosts que dependem dele.

Desativar um dispositivo virtual resulta nas seguintes ações:

- O dispositivo virtual é removido.

- O disco do SO e os discos de dados criados para o dispositivo virtual são removidos.

- O serviço hospedado e a rede virtual criados durante o provisionamento são mantidos. Se você não os estiver usando, deverá excluí-los manualmente.

- Instantâneos de nuvem criados para o dispositivo virtual são mantidos.

Para obter um procedimento passo a passo, acesse [Desativar e excluir seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Assim que o dispositivo virtual for mostrado como desativado na página do serviço do StorSimple Manager, você poderá excluir o dispositivo virtual da lista de dispositivos na página **Dispositivos**.


### Iniciar, parar e reiniciar um dispositivo virtual
Ao contrário do dispositivo físico do StorSimple, não há um botão liga/desliga em um dispositivo virtual do StorSimple. No entanto, pode haver ocasiões em que você precisará parar e reiniciar o dispositivo virtual. Por exemplo, algumas atualizações podem exigir que a VM seja reiniciada para a conclusão do processo de atualização. A maneira mais fácil de iniciar, parar e reiniciar um dispositivo virtual é usar o Console de Gerenciamento de Máquinas Virtuais.

Quando você examinar o Console de Gerenciamento, o status do dispositivo virtual será **Em execução** porque ele é iniciado por padrão após sua criação. É possível iniciar, parar e reiniciar uma máquina virtual a qualquer momento.

[AZURE.INCLUDE [Parar e reiniciar o dispositivo virtual](../../includes/storsimple-stop-restart-virtual-device.md)]

### Redefinir para os padrões de fábrica

Se você decidir que deseja apenas recriar seu dispositivo virtual, simplesmente desative-o e exclua-o e então crie um novo. Assim como quando o dispositivo físico é redefinido, o novo dispositivo virtual não terá qualquer atualização instalada; portanto, verifique se há atualizações antes de usá-lo.


## Failover para o dispositivo virtual

A recuperação de desastres (RD) é um dos principais cenários para os quais o dispositivo virtual StorSimple foi projetado. Nesse cenário, o dispositivo físico StorSimple ou o datacenter inteiro podem não estar disponível. Felizmente, você pode usar um dispositivo virtual para restaurar operações em um local alternativo. Durante a RD, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino. Os pré-requisitos para a RD são que o dispositivo virtual tenha sido criado e configurado, todos os volumes no contêiner de volume tenham sido colocados offline e o contêiner de volume tenha um de instantâneo de nuvem associado.

>[AZURE.NOTE]
>
> - Ao usar um dispositivo virtual como o dispositivo secundário para recuperação de desastre, tenha em mente que o 8010 tem 30 TB de Armazenamento Standard e o 8020 tem 64 TB de Armazenamento Premium. O dispositivo virtual 8020 com uma maior capacidade pode ser mais adequado para um cenário de recuperação de desastre.
> - Não é possível executar failover ou clonar um dispositivo que executa a Atualização 2 para um dispositivo que executa um software anterior à Atualização 1. No entanto, é possível executar failover de um dispositivo com a Atualização 2 para um dispositivo com a Atualização 1 (1.1 ou 1.2)

Para obter um procedimento passo a passo, vá para [executar failover para um dispositivo virtual](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## Desligar ou excluir o dispositivo virtual

Se você configurou previamente e usou um dispositivo virtual StorSimple, mas agora deseja parar de acumular encargos de computação para seu uso, poderá desligar o dispositivo virtual. Desligar o dispositivo virtual não exclui seu sistema operacional ou os discos de dados do armazenamento. Isso para o acúmulo de encargos em sua assinatura, mas os encargos de armazenamento para os discos de sistema operacional e de dados continuarão.

Se você excluir ou desligar o dispositivo virtual, ele será exibido como **Offline** na página Dispositivos do serviço Gerenciador do StorSimple. Você pode optar por desativar ou excluir o dispositivo se também desejar excluir os backups criados pelo dispositivo virtual. Para obter mais informações, consulte [Desativar e excluir um dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Desligar um dispositivo virtual](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Excluir um dispositivo virtual](../../includes/storsimple-delete-virtual-device.md)]

## Próximas etapas

- Saiba como [Usar o serviço do StorSimple Manager para gerenciar um dispositivo virtual](storsimple-manager-service-administration.md).
 
- Entenda como [Restaurar um volume do StorSimple de um conjunto de backups](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0121_2016-->