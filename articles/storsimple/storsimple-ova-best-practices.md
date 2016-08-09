<properties 
   pageTitle="Práticas recomendadas para o StorSimple Virtual Array | Microsoft Azure"
   description="Descreve as práticas recomendadas para implantar e gerenciar o StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="alkohli" />

# Práticas recomendadas do StorSimple Virtual Array

## Visão geral

A Matriz Virtual Microsoft Azure StorSimple é uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre um dispositivo virtual local executando em um hipervisor e o armazenamento em nuvem do Microsoft Azure. O StorSimple Virtual Array é uma alternativa eficiente e econômica para a matriz física da série 8000. A matriz virtual pode ser executada em sua infraestrutura de hipervisor existente, oferece suporte a iSCSI e aos protocolos SMB e é especialmente adequada a cenários de filiais/escritórios remotos. Para saber mais sobre as soluções do StorSimple, vá para [Visão geral do Microsoft Azure StorSimple](https://www.microsoft.com/pt-BR/server-cloud/products/storsimple/overview.aspx).

Este artigo aborda as práticas recomendadas implementadas durante a configuração inicial, a implantação e o gerenciamento do StorSimple Virtual Array. Essas práticas recomendadas oferecem diretrizes validadas para a instalação e o gerenciamento da sua matriz virtual. Este artigo é destinado a administradores de TI responsáveis pela implantação e gerenciamento de matrizes em seus data centers virtuais.

É recomendável uma revisão periódica das práticas recomendadas para ajudar a garantir que seu dispositivo ainda esteja em conformidade quando forem feitas alterações no fluxo de instalação ou de operação. Se você encontrar problemas ao implementar essas práticas recomendadas em sua matriz virtual, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

## Práticas recomendadas de configuração 

Essas práticas recomendadas abrangem as diretrizes que precisam ser seguidas durante a instalação inicial e a implantação das matrizes virtuais. Isso inclui as práticas recomendadas relacionadas ao provisionamento da máquina virtual, às configurações de política de grupo, ao dimensionamento, à configuração da rede, à configuração de contas de armazenamento e à criação de compartilhamentos e de volumes para a matriz virtual.

### Provisionamento 

O StorSimple Virtual Array é uma máquina virtual (VM) provisionada no hipervisor (Hyper-V ou VMware) do seu servidor host. Ao provisionar a máquina virtual, verifique se seu host é capaz de dedicar recursos suficientes. Para saber mais, vá para [requisitos mínimos de recurso](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) para provisionar uma matriz.

Implemente as seguintes práticas recomendadas ao provisionar a matriz virtual:


| | Hyper-V | VMware |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Tipo de máquina virtual** | A VM de **geração 2** a ser usada com o Windows Server 2012 ou posterior e uma imagem *.vhdx*. <br></br> A VM de **geração 1** a ser usada com um Windows Server 2008 ou posterior e uma imagem *.vhd*. | Use a máquina virtual versão 8 a 11 ao usar a imagem *.vmdk*. |
| **Tipo de memória** | Configurar como **memória estática**. <br></br> Não use a opção **memória dinâmica**. | |
| **Tipo de disco de dados** | Provisione como **expandindo dinamicamente**.<br></br> **Tamanho fixo** levará um longo tempo. <br></br> Não use a opção de **diferenciação**. | Use a opção **provisionamento dinâmico**. |
| **Modificação de disco de dados** | A expansão ou a redução não é permitida. Uma tentativa de fazer isso resultará na perda de todos os dados locais no dispositivo. | A expansão ou a redução não é permitida. Uma tentativa de fazer isso resultará na perda de todos os dados locais no dispositivo. |

### Dimensionamento

Ao dimensionar seu StorSimple Virtual Array, você precisará considerar os seguintes fatores:

- Reserva de local de volumes ou de compartilhamentos. Cerca de 12% do espaço é reservado na camada local para cada volume em camadas provisionado. Aproximadamente 10% do espaço também é reservado para um volume localmente fixo para o sistema de arquivos.
- Sobrecarga de instantâneo. Aproximadamente 15% do espaço na camada local é reservado para instantâneos.
- Necessidade de restaurações. O dimensionamento deve levar em consideração o espaço necessário para a restauração caso fazer uma restauração seja como uma nova operação. Observe que a restauração é concluída para um compartilhamento ou um volume do mesmo tamanho ou maior.
- Parte do buffer deve ser alocada para qualquer crescimento inesperado.

Com base nos fatores acima, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Os exemplos a seguir ilustram como você pode dimensionar uma matriz virtual com base em seus requisitos.

#### Exemplo 1:
Em sua matriz virtual, você deseja ser capaz de

- provisionar um volume ou um compartilhamento em camadas de 2 TB.
- provisionar um volume ou um compartilhamento em camadas de 1 TB.
- provisionar um volume ou um compartilhamento fixado localmente de 300 GB.


Para os volumes ou os compartilhamentos acima, vamos calcular os requisitos de espaço na camada local.

Primeiro, para cada volume/compartilhamento em camadas, a reserva local seria igual a 12% do tamanho do volume/compartilhamento. Primeiro, para cada volume/compartilhamento em camadas, a reserva local seria igual a 10% do tamanho do volume/compartilhamento. Neste exemplo, você precisará de

- reserva local de 240 GB (para um volume/compartilhamento em camadas de 2 TB)
- reserva local de 120 GB (para um volume/compartilhamento em camadas de 1 TB)
- 330 GB para volume ou compartilhamento afixado localmente

O espaço total necessário na camada local até o momento será de: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, será necessário pelo menos um espaço na camada local do tamanho da maior reserva única. Esse valor extra será usado caso você precise restaurar de um instantâneo de nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo a reserva para o sistema de arquivos) e, portanto, você adicionaria isso aos 660 GB: 660 GB + 330 GB = 990 GB. Se executamos as restaurações adicionais subsequentes, sempre será possível liberar o espaço da operação de restauração anterior.

Em terceiro lugar, precisaremos de 15% do espaço total local até o momento para armazenar os instantâneos locais, para que apenas 85% disso esteja disponível. Neste exemplo, isso seria em torno de 990 GB = 0,85*TB de disco de dados provisionado. Portanto, o disco de dados provisionado seria (990*(1/0,85))= 1164 GB = 1,16 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Incluindo o crescimento inesperado e novas restaurações, você deverá provisionar um disco local de aproximadamente 1,25 a 1,5 TB.

> [AZURE.NOTE] Também é recomendável que o disco local seja dinamicamente provisionado. Isso ocorre porque o espaço de restauração só será necessário quando você quiser restaurar dados anteriores a 5 dias. A recuperação no nível do item permitirá que você restaure os dados dos últimos 5 dias sem exigir espaço extra para a restauração.

#### Exemplo 2: 
Em sua matriz virtual, você deseja ser capaz de

- provisionar um volume em camadas de 2 TB
- provisionar um volume fixado localmente de 300 GB

Com base em 12% de reserva de espaço local para volumes/compartilhamentos em camadas e 10% para volumes/compartilhamentos fixados localmente, será necessária

- uma reserva local de 240 GB (para um volume/compartilhamento em camadas de 2 TB)
- 330 GB para volume ou compartilhamento afixado localmente

O espaço total necessário na camada local será de: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restauração seria de 330 GB.

15% do disco total seriam usados para armazenar instantâneos, de forma que somente 0,85 estariam disponíveis. Portanto, o tamanho do disco seria (900*(1/0,85)) = TB 1,06 ~ 1,25 TB (arredondamento para o quartil mais próximo)

Incluindo qualquer crescimento inesperado, você poderá provisionar um disco local de 1,25-1,5 TB.


### Política de grupo

A Política de Grupo é uma infraestrutura que permite a você implementar configurações específicas para usuários e computadores. As configurações de Política de Grupo estão contidas em GPOs (objetos de Política de Grupo), vinculados aos seguintes contêineres do AD DS (Serviços de Domínio do Active Directory): sites, domínios ou UOs (unidades organizacionais).

Se a matriz virtual estiver associada a um domínio, os GPOs poderão ser aplicados a ela. Esses GPOs podem instalar aplicativos como um software antivírus que possa afetar negativamente a operação do StorSimple Virtual Array.

Portanto, recomendamos que você:

-   Certifique-se de que a matriz virtual esteja em sua própria OU (unidade organizacional) do Active Directory.

-   Certifique-se de que nenhum GPO (objeto de política de grupo) seja aplicado à matriz virtual. Você pode bloquear a herança para garantir que a matriz virtual (nó filho) não herde automaticamente qualquer GPO do pai. Para saber mais, acesse [bloquear herança](https://technet.microsoft.com/library/cc731076.aspx).


### Rede

A configuração de rede para sua matriz virtual é feita por meio da interface do usuário da Web local. Uma interface de rede virtual é habilitada por meio do hipervisor em que a matriz virtual é provisionada. Use a página [Configurações de Rede](storsimple-ova-deploy3-fs-setup.md) para configurar o endereço IP da interface de rede virtual, a sub-rede e o gateway. Você também pode configurar o servidor DNS primário e secundário, as configurações de hora e as configurações de proxy opcionais para o seu dispositivo. A maior parte da configuração de rede é uma configuração única. Examine os [Requisitos de rede do StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implantar a matriz virtual.

Ao implantar sua matriz virtual, recomendamos que você siga estas práticas recomendadas:

-   Certifique-se de que a rede na qual a matriz virtual está implantada tenha a capacidade de dedicar uma largura de banda de Internet de 5 Mbps (ou mais) em todas as ocasiões.

    -   A largura de banda da Internet necessária variará de acordo com as características de sua carga de trabalho e com a taxa de alteração de dados.

    -   A alteração de dados que pode ser tratada é diretamente proporcional à sua largura de banda da Internet. Como um exemplo, ao fazer um backup, uma largura de banda de 5 Mbps pode acomodar uma alteração de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), você poderá lidar com uma alteração de dados quatro vezes maior (72 GB).

-   Verifique se a conectividade com a Internet está disponível em todas as ocasiões. As conexões da Internet esporádicas ou não confiáveis com os dispositivos podem resultar em perda de acesso aos dados na nuvem e podem resultar em uma configuração sem suporte.

-   Se você planeja implantar o dispositivo como um servidor iSCSI:
	-   Recomendamos que você desabilite a opção **Obter endereço IP automaticamente** (DHCP).
	-   Configure endereços IP estáticos. Você também deve configurar um servidor DNS principal e um secundário.

	-   Se você estiver definindo várias interfaces de rede em sua matriz virtual, observe que apenas a primeira interface de rede (por padrão, a **Ethernet**) poderá acessar a nuvem. Para controlar o tipo de tráfego, você poderá criar várias interfaces de rede virtual na matriz virtual (configurada como um servidor iSCSI) e conectá-las a sub-redes diferentes.

-   Para limitar somente a largura de banda da nuvem (usada pela matriz virtual), configure a limitação no roteador ou no firewall. Se você definir a limitação em seu hipervisor, ele limitará todos os protocolos, incluindo iSCSI e SMB, em vez de apenas a largura de banda de nuvem.

-   Verifique se a sincronização de hora para hipervisores está ativada. Se estiver usando o Hyper-V, selecione a matriz virtual no Gerenciador do Hyper-V, vá para **Configurações &gt; Serviços de Integração** e verifique se a **Sincronização de hora** está marcada.

### Contas de armazenamento

O StorSimple Virtual Array pode ser associado a uma única conta de armazenamento. Essa conta de armazenamento pode ser uma conta de armazenamento gerada automaticamente, uma conta na mesma assinatura que o serviço ou uma conta de armazenamento relacionada a outra assinatura. Para saber mais, veja [gerenciar contas de armazenamento para sua matriz virtual](storsimple-ova-manage-storage-accounts.md).

Use as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

-   Ao vincular várias matrizes virtuais a uma única conta de armazenamento, inclua a capacidade máxima (64 TB) em uma matriz virtual e o tamanho máximo (500 TB) de uma conta de armazenamento. Isso limita o número de matrizes virtuais de tamanho máximo que podem ser associadas à conta de armazenamento a cerca de 7.

-   Ao criar uma nova conta de armazenamento
	-   É recomendável que você a crie na região mais próxima ao escritório remoto/filial onde o StorSimple Virtual Array foi implantado para minimizar as latências.

	-   Tenha em mente que você não pode mover uma conta de armazenamento entre regiões diferentes. Também é possível mover um serviço entre assinaturas.

	-   Use uma conta de armazenamento que implemente a redundância entre os data centers. O GRS (Armazenamento com Redundância Geográfica), o ZRS (Armazenamento com Redundância Local) e o LRS (Armazenamento Localmente Redundante) têm suporte para serem usados com a matriz virtual. Para saber mais sobre os diferentes tipos de contas de armazenamento, vá para [Replicação de armazenamento do Azure](../storage/storage-redundancy.md).


### Compartilhamentos e volumes

Para seu StorSimple Virtual Array, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho, bem como ao tipo configurado.

#### Tamanho de volume/compartilhamento

Em sua matriz virtual, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes quando ele estiver configurado como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho, bem como ao tipo configurado.

Tenha em mente as seguintes práticas recomendadas ao provisionar compartilhamentos ou volumes em seu dispositivo virtual.

-   Os tamanhos de arquivo relativos ao tamanho provisionado de um compartilhamento em camadas pode afetar o desempenho em camadas. Trabalhar com arquivos grandes pode resultar em uma divisão lenta em camadas. Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja inferior a 3% do tamanho do compartilhamento.

-   Um máximo de 16 volumes/compartilhamentos pode ser criado na matriz virtual. Se fixados localmente, os volumes/compartilhamentos poderão ter um tamanho entre 50 GB a 2 TB. Se estiverem em camadas, os volumes/compartilhamentos deverão ter entre 500 GB e 20 TB.

-   Ao criar um volume, inclua o consumo de dados esperado, bem como o crescimento futuro. Observe que, embora o volume não possa ser expandido posteriormente, você sempre poderá restaurar para um volume maior.

-   Assim que o volume tiver sido criado, não será possível reduzir o tamanho do volume no StorSimple.
   
-   Ao gravar em um volume em camadas no StorSimple, observe que quando os dados do volume atingirem um certo limite (relativo ao espaço local reservado para o volume), a E/S será limitada. Continuar a gravar nesse volume reduzirá significativamente a velocidade de E/S. Embora seja possível gravar em um volume em camadas além da sua capacidade provisionada (nós não impedimos ativamente o usuário de gravar além da capacidade provisionada), você verá uma notificação de alerta informando que há um excesso de assinaturas. Quando você vir o alerta, será imperativo que você tome medidas corretivas, como excluir dados do volume ou restaurar o volume em um volume maior (atualmente, a expansão de volume não tem suporte).

-   Para casos de uso da recuperação de desastre, como o número de compartilhamentos/volumes permitidos é 16 e o número máximo de compartilhamentos/volumes que podem ser processados em paralelo também é 16, o número de compartilhamentos/volumes não influenciará o RPO e os RTOs.

#### Tipo de volume/compartilhamento

O StorSimple oferece suporte a dois tipos de volume/compartilhamento baseados no uso: fixados localmente e em camadas. Os volumes/compartilhamentos localmente fixados são fortemente provisionados, enquanto os volumes/compartilhamentos em camadas têm provisionamento dinâmico.

É recomendável que você implemente as seguintes práticas recomendadas ao configurar volumes/compartilhamentos do StorSimple:

-   Identifique o tipo de volume com base em cargas de trabalho que você pretende implantar antes de criar um volume. Use volumes localmente fixos para cargas de trabalho que exigem garantias locais de dados (mesmo durante uma interrupção de nuvem) e que exigem latências de nuvem baixas. Quando você cria um volume em sua matriz virtual, não pode alterar o tipo de volume de fixado localmente para em camadas ou *vice versa*. Por exemplo, crie volumes localmente fixos ao implantar cargas de trabalho do SQL ou cargas de trabalho que hospedam máquinas virtuais (VMs); use volumes em camadas para cargas de trabalho de compartilhamento de arquivo.


-   Marque a opção para dados de arquivamento usados com menos frequência ao lidar com arquivos de tamanho grande. Um tamanho maior de bloco de eliminação de duplicação de 512 K é usado quando essa opção é habilitada para acelerar a transferência de dados para a nuvem.

#### Formato de volume

Depois de criar volumes do StorSimple em seu servidor iSCSI, você precisará inicializar, montar e formatar os volumes. Essa operação é executada no host conectado ao seu dispositivo StorSimple. As práticas recomendadas a seguir são recomendadas ao montar e formatar volumes no host do StorSimple.

-   Execute uma formatação rápida em todos os volumes do StorSimple.

-   Ao formatar um volume do StorSimple, use um tamanho de unidade de alocação (AUS) de 64 KB (o padrão é 4 KB). O AUS de 64 KB baseia-se em testes realizados internamente para cargas de trabalho comuns do StorSimple, bem como outras cargas de trabalho.

-   Ao usar o StorSimple Virtual Array configurado como um servidor iSCSI, não use volumes estendidos ou discos dinâmicos porque eles não têm suporte do StorSimple.

#### Acesso ao compartilhamento

Ao criar compartilhamentos em seu servidor de arquivo de matriz virtual, siga estas diretrizes:

-   Ao criar um compartilhamento, atribua um grupo de usuários como um administrador de compartilhamento em vez de um único usuário.

-   Você pode gerenciar as permissões de NTFS depois que o compartilhamento for criado por meio da edição dos compartilhamentos no Windows Explorer.

#### Acesso ao volume

Ao configurar os volumes iSCSI no Array Virtual StorSimple, é importante controlar o acesso sempre que necessário. Crie e associe ACRs (registros de controle de acesso) a volumes do StorSimple para determinar quaisStorSimple servidores de host poderão acessar os volumes.

Use as seguintes práticas recomendadas ao configurar ACRs para volumes do StorSimple:

-   Sempre associe pelo menos um ACR a um volume.

-   Vários ACRs só devem ser definidos em um ambiente clusterizado.

-   Ao atribuir mais de um ACR a um volume, verifique se o volume não está exposto de uma maneira em que possa ser acessado simultaneamente por mais de um host não clusterizado. Se vários ACRs tiverem sido atribuídos a um volume, uma mensagem de aviso será exibida para você verificar sua configuração.

### Segurança e criptografia de dados

O StorSimple Virtual Array tem recursos de segurança e criptografia de dados que garantem a confidencialidade e a integridade dos dados. Ao usar esses recursos, é recomendável que você siga estas práticas recomendadas:

-   Defina uma chave de criptografia de armazenamento de nuvem para gerar a criptografia AES-256 antes que os dados sejam enviados da sua matriz virtual para a nuvem. Essa chave não será necessária se seus dados forem criptografados inicialmente. A chave pode ser gerada e mantida segura usando um sistema de gerenciamento de chaves como o [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md).

-   Ao configurar a conta de armazenamento por meio do serviço StorSimple Manager, habilite o modo SSL para criar um canal seguro para comunicação de rede entre seu dispositivo StorSimple e a nuvem.

-   Regenere as chaves para suas contas de armazenamento (acessando o serviço de armazenamento do Azure) periodicamente para ser responsável por todas as alterações no acesso com base na lista de administradores alterada.

-   Os dados em sua matriz virtual são compactados e a eliminação de duplicação acontece antes que os dados sejam enviados para o Azure. Não recomendamos usar o serviço de função Eliminação de Duplicação de Dados no host do Windows Server.


## Práticas recomendadas operacionais

As práticas recomendadas operacionais são diretrizes que devem ser seguidas durante a operação da matriz virtual ou no gerenciamento diário. Elas abordarão as tarefas de gerenciamento específicas, como fazer backups, restaurar de um conjunto de backup, realizar um failover, desativar e excluir a matriz, uso e integridade do sistema de monitoramento e a execução de verificaçõess de vírus na matriz virtual.

### Backups

É feito backup dos dados em sua matriz virtual para a nuvem de duas maneiras: um backup padrão diário automatizado do dispositivo inteiro começando às 22:30 ou por meio de um backup manual sob demanda. Por padrão, o dispositivo cria automaticamente instantâneos de nuvem diários de todos os dados que residem nela. Para saber mais, acesse [fazer backup do seu StorSimple Virtual Array](storsimple-ova-backup.md).

A frequência e a retenção associadas aos backups padrão não podem ser alteradas, mas você pode configurar a hora em que os backups diários são iniciados todos os dias. Ao configurar a hora de início para os backups automatizados, recomendamos que:

-   Agende os backups fora dos horários de pico. A hora de início de backup não deve coincidir com muitas E/S do host.

-   Inicie um backup manual sob demanda ao planejar a execução de um failover de dispositivo ou antes da janela de manutenção para proteger os dados em sua matriz virtual.

### Restaurar

Você pode restaurar de um conjunto de backup de duas maneiras: restaure para outro volume ou compartilhamento ou realize uma recuperação no nível de item (disponível apenas em uma matriz virtual configurada como um servidor de arquivos). A recuperação no nível do item permite fazer uma recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Para saber mais, vá para [restaurar de um backup](storsimple-ova-restore.md).

Ao realizar uma restauração, lembre-se das seguintes diretrizes:

-   Seu StorSimple Virtual Array não oferece suporte à restauração no local. Entretanto, isso pode ser facilmente obtido em um processo de duas etapas: abra espaço na matriz virtual e restaure em outro volume/compartilhamento.

-   Ao restaurar de um volume local, tenha em mente que a restauração será uma operação demorada. Embora o volume possa ficar online rapidamente, os dados continuarão a ser alimentados no plano de fundo.

-   O tipo de volume permanece o mesmo durante o processo de restauração. Um volume em camadas é restaurado para outro volume em camadas e um volume fixado localmente é restaurado para outro volume fixado localmente.

-   Ao tentar restaurar um volume ou um compartilhamento de um conjunto de backup, se o trabalho de restauração falhar, um volume de destino ou compartilhamento ainda poderá ser criado no portal. É importante excluir este volume de destino não utilizado ou compartilhá-lo no portal para minimizar quaisquer problemas futuros causado por esse elemento.

### Failover e recuperação de desastres

Um failover de dispositivo permitirá que você migre os dados de um dispositivo de *origem* no datacenter para outro dispositivo de *destino* localizado no mesmo local geográfico ou em outro diferente. O failover de dispositivo é para todo o dispositivo. Durante o failover, a propriedade dos dados de nuvem para o dispositivo de origem é alterada para aquela do dispositivo de destino.

Para seu StorSimple Virtual Array, só será possível fazer failover para outra matriz virtual gerenciada pelo mesmo serviço do StorSimple Manager. Não é permitido um failover para um dispositivo da série 8000 ou para uma matriz gerenciada por um serviço do StorSimple Manager diferente (daquele para o dispositivo de origem). Para saber mais sobre as considerações de failover, acesse os [pré-requisitos para o failover de dispositivo](storsimple-ova-failover-dr.md).

Ao fazer um failover para sua matriz virtual, tenha o seguinte em mente:

-   Para um failover planejado, é uma prática recomendada colocar todos os volumes/compartilhamentos offline antes de iniciar o failover. Siga as instruções específicas do sistema operacional para colocar primeiro os volumes/compartilhamentos offline no host e depois colocá-los offline em seu dispositivo virtual.

-   Para uma DR (recuperação de desastre) de um servidor de arquivos, recomendamos que você ingresse o dispositivo de destino ao mesmo domínio que o dispositivo de origem, para que as permissões de compartilhamento sejam resolvidas automaticamente. Nesta versão, há suporte apenas para o failover de um dispositivo de destino no mesmo domínio.

-   Depois que a recuperação de desastre for concluída com êxito, o dispositivo de origem será automaticamente excluído. Embora o dispositivo não esteja mais disponível, a máquina virtual que você provisionou no sistema host ainda está consumindo recursos. É recomendável que você exclua esta máquina virtual do sistema host para impedir o acúmulo de todos os encargos.

-   Observe que, mesmo se o failover não for bem-sucedido, **os dados estarão sempre seguros na nuvem**. Considere os três cenários a seguir em que o failover não foi concluído com êxito:

    -   Ocorreu uma falha nos estágios iniciais do failover, como quando as pré-verificações de DR estão sendo executadas. Nessa situação, o dispositivo de destino ainda é utilizável. Você pode repetir o failover no mesmo dispositivo de destino.

    -   Ocorreu uma falha durante o processo de failover real. Nesse caso, o dispositivo de destino é marcado como inutilizável. Você precisará provisionar e configurar a outra matriz virtual de destino e usá-la para failover.

    -   O failover foi concluído e depois dele o dispositivo de origem foi excluído, mas o dispositivo de destino tem problemas e o usuário não consegue acessar os dados. Os dados ainda estão seguros na nuvem e podem ser facilmente recuperados por meio da criação de outra matriz virtual ; depois eles serão usados como um dispositivo de destino para a recuperação de desastre.

### Desativar

Quando você desativar uma Matriz Virtual StorSimple, você dividirá a conexão entre o dispositivo e o serviço StorSimple Manager correspondente. A desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço StorSimple Manager novamente. Para saber mais, acesse [desativar e excluir seu StorSimple Virtual Array](storsimple-deactivate-and-delete-device.md).

Lembre-se das seguintes práticas recomendadas ao desativar sua matriz virtual:

-   Tire um instantâneo de nuvem de todos os dados antes de desativar um dispositivo virtual. Quando você desativa uma matriz virtual, todos os dados do dispositivo local são perdidos. Tirar um instantâneo de nuvem permitirá que você recupere os dados em um estágio posterior.

-   Antes de desativar um StorSimple Virtual Array, certifique-se de interromper ou excluir clientes e hosts que dependem desse dispositivo virtual.

-   Exclua um dispositivo desativado se ele não estiver sendo usado para que ele não acumule cobranças.

### Monitoramento

Para garantir que seu StorSimple Virtual Array esteja em um estado de integridade contínuo, você precisará monitorar a matriz e garantir que receberá informações do sistema, incluindo alertas. Para monitorar a integridade geral da matriz virtual, implemente as seguintes práticas recomendadas:

- Configure o monitoramento para acompanhar o uso do disco de dados da matriz virtual, bem como o disco do sistema operacional. Se você estiver executando o Hyper-V, poderá usar uma combinação do SCVMM (System Center Virtual Machine Manager) e do SCOM (System Center Operations Manager) para monitorar seus hosts de virtualização.

- Configure notificações por email em sua matriz virtual para enviar alertas em determinados níveis de uso.

### Aplicativos de pesquisa de índice e de verificação de vírus

Um StorSimple Virtual Array pode colocar automaticamente os dados em camadas da camada local para a nuvem do Microsoft Azure. Quando um aplicativo como a pesquisa de índice ou uma verificação de vírus é usado para varrer os dados armazenados no StorSimple, você precisará tomar cuidado para que os dados de nuvem não sejam acessados e obtidos na camada local.

É recomendável que você implemente as práticas recomendadas a seguir ao configurar a verificação de vírus ou a pesquisa de índice em sua matriz virtual:

-   Desabilite todas as operações de verificação completa configuradas automaticamente.

-   Para os volumes em camadas, configure a pesquisa de índice ou o aplicativo de verificação de vírus para executar uma verificação incremental. Isso examinaria apenas os novos dados, provavelmente residentes na camada local. Os dados que estão em camadas na nuvem não são acessados durante uma operação incremental.

-   Verifique se as configurações e os filtros de pesquisa corretos foram definidos de forma que somente os tipos de arquivo sejam verificados. Por exemplo, os arquivos de imagem (JPEG, GIF e TIFF) e os desenhos de engenharia não devem ser verificados durante a recriação de índice completo ou incremental.

Se você estiver usando o processo de indexação do Windows, siga estas diretrizes:

-   Não use o Indexador do Windows para volumes em camadas, já que isso cancelará grandes quantidades de dados (TBs) da nuvem caso o índice tenha de ser recriado com frequência. A recriação do índice poderia recuperar todos os tipos de arquivo para indexar seu conteúdo.

-   Use o processo de indexação do Windows para volumes fixados localmente, já que eles só acessariam dados nas camadas locais para criar os índices (os dados da nuvem não serão acessados).

### Bloqueio de intervalo de bytes

Os aplicativos podem bloquear um intervalo de bytes especificado nos arquivos. Se o bloqueio de intervalos de bytes for habilitado nos aplicativos que estejam gravando em seu StorSimple, as camadas não funcionarão em sua matriz virtual. Para que as camadas funcionem, todas as áreas dos arquivos acessados deverão estar desbloqueadas. Não há suporte para o bloqueio de intervalo de bytes com volumes em camadas em sua matriz virtual.

As medidas recomendadas para aliviar isso incluem:

-   Desligar o bloqueio de intervalo de bytes em sua lógica de aplicativo.

-   Usar volumes fixados localmente (em vez de em camadas) para os dados associados a este aplicativo. Os volumes fixados localmente não ficam em camadas na nuvem.

-   Quando usar volumes fixados localmente com o bloqueio de intervalo de bytes habilitado, observe que o volume poderá ficar online antes da conclusão da restauração. Nestas instâncias, você deverá aguardar a conclusão da restauração.

## Várias matrizes

Talvez seja necessário implantar várias matrizes virtuais para responder por um crescente conjunto de dados de trabalho que poderia derramar para a nuvem, afetando o desempenho do dispositivo. Nesses casos, é melhor dimensionar dispositivos à medida que o conjunto de trabalho aumentar. Isso exigirá que um ou mais dispositivos sejam adicionados ao data center local. Ao adicionar os dispositivos, você poderia:

-   Dividir o conjunto de dados atual.
-   Implantar novas cargas de trabalho para os novos dispositivos.
-   Se você estiver implantando várias matrizes, recomendamos que, da perspectiva do balanceamento de carga, distribua a matriz por vários hosts de hipervisor diferentes.

-  Várias matrizes virtuais (quando configuradas como um servidor de arquivos ou como um servidor iSCSI) podem ser implantadas em um Namespace de Sistema de Arquivos Distribuído . Para obter as etapas detalhadas, vá para [Solução de Namespace de Sistema de Arquivos Distribuído com implantação de armazenamento em nuvem híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Atualmente, a replicação DFS não é recomendada para uso com a matriz virtual.


## Consulte também
Saiba como [administrar seu StorSimple Virtual Array](storsimple-ova-manager-service-administration.md) por meio do serviço StorSimple Manager.

<!---HONumber=AcomDC_0727_2016-->