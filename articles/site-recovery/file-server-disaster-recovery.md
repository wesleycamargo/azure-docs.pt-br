---
title: Proteger um servidor de arquivos usando o Azure Site Recovery
description: Este artigo descreve como proteger um servidor de arquivos usando o Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: ac734ffc6cb57188b7b0959cbe7655949b2853de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Proteger um servidor de arquivos usando o Azure Site Recovery 

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por manter seus aplicativos de negócios em execução e disponíveis durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação de várias cargas de trabalho.

Este artigo descreve como proteger um servidor de arquivos usando o Azure Site Recovery e outras recomendações para atender a vários ambientes.     

- [Replicar computadores de servidor de arquivos IaaS do Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replicar um servidor de arquivos local usando o Azure Site Recovery](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Arquitetura do servidor de arquivos
O objetivo de um sistema de compartilhamento de arquivos distribuído é oferecer um ambiente em que um grupo de usuários distribuídos geograficamente possa colaborar para trabalhar eficientemente em arquivos e ter a certeza de que seus requisitos de integridade serão impostos. Um ecossistema de servidor de arquivos local típico que dá suporte a um grande número de usuários simultâneos e a um grande número de itens de conteúdo usa DFSR (Replicação de Sistema de Arquivos Distribuído) para programação de replicação e limitação de largura de banda. A DFSR usa um algoritmo de compactação conhecido como RDC (Compactação Diferencial Remota), que pode ser usado para atualizar com eficiência os arquivos em uma rede com largura de banda limitada. Ele detecta inserções, remoções e reorganizações de dados nos arquivos, permitindo que a DFSR replique somente os blocos de arquivo alterados quando os arquivos são atualizados. Também existem ambientes de servidor de arquivos em que os backups diários são feitos em intervalos fora de pico, que atendem às necessidades de desastre e não têm implementação de DFSR.

A topologia a seguir ilustra o ambiente de servidor de arquivos com DFSR implementada.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

Na referência acima, vários servidores de arquivos referidos como membros participam ativamente da replicação de arquivos em um grupo de replicação. O conteúdo na pasta replicada estará disponível para todos os clientes que enviarem solicitações para um dos membros, mesmo que um dos membros fique offline.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Recomendação de recuperação de desastre para servidores de arquivos:

1.  Replicar um servidor de arquivos usando o Azure Site Recovery: os servidores de arquivos podem ser replicados para o Azure usando o Azure Site Recovery. Quando um ou mais os servidores de arquivo no local estão inacessíveis, a recuperação de VMs pode ser processada no Azure, que pode, em seguida, atender a solicitações de clientes localmente, desde que exista conectividade VPN Site a Site e o Active Directory esteja configurado no Azure. Isso pode ser feito em um ambiente com DFSR configurada ou em um ambiente de servidor de arquivos simples sem DFSR. 

2.  Estender a DFSR a uma VM Iaas do Azure: em um ambiente de servidor de arquivos em cluster com DFSR implementada, uma abordagem recomendada é estender a DFSR local ao Azure. Uma máquina virtual do Azure é habilitada para executar a função de servidor de arquivos. 

    Depois que as dependências de conectividade VPN Site a Site e o Active Directory são tratadas e a DFSR está ativa, quando um ou mais os servidores de arquivo no local ficam inacessíveis, os clientes ainda podem se conectar à VM do Azure, que atenderá às solicitações.

    Essa abordagem é sugerida caso suas VMs tenham configurações sem suporte pelo Azure Site Recovery, por exemplo: disco compartilhado de cluster que, às vezes, é usado em ambientes de servidor de arquivos.  A DFSR também funciona bem em ambientes de baixa largura de banda com taxa de cancelamento média. O custo adicional de ter uma VM do Azure em execução o tempo todo também precisa ser levado em conta com isso.  

3.  Use o serviço Sincronização de Arquivos do Azure para replicar seus arquivos: se você está se preparando para sua jornada na nuvem ou já está usando uma VM do Azure, recomendamos o uso do serviço Sincronização de Arquivos do Azure, que oferece a sincronização de compartilhamentos de arquivo totalmente gerenciados na nuvem, que podem ser acessados pelo protocolo [SMB](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (Server Message Block) padrão do setor. Os compartilhamentos de Arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem do Windows, do Linux e do MacOS. 

O diagrama a seguir fornece uma representação visual destinada a facilitar a decisão de qual estratégia deve ser usada para o ambiente do servidor de arquivos.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Fatores a considerar ao tomar uma decisão de recuperação de desastre no Azure

|Ambiente  |Recomendações  |Considere o seguinte |
|---------|---------|---------|
|Ambiente de servidor de arquivos com/sem DFSR|   [Usar o Azure Site Recovery para replicação](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    O Site Recovery não dá suporte a cluster de disco compartilhado e NAS. Se seu ambiente usa uma dessas configurações, use um dos outros métodos conforme apropriado. <br> O Azure Site Recovery não dá suporte a SMB 3.0, o que significa que somente quando as alterações feitas nos arquivos forem atualizadas no local original do arquivo é que a VM replicada incorporará as alterações.
|Ambiente de servidor de arquivos com DFSR     |  [Estender a DFSR para uma máquina virtual IaaS do Azure:](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     A DFSR funciona bem em ambientes de largura de banda extremamente fragmentada, mas essa abordagem requer uma VM do Azure em execução o tempo todo. O custo da VM deve ser considerado no planejamento.         |
|VM Iaas do Azure     |     [Sincronização de Arquivos do Azure](#use-azure-file-sync-service-to-replicate-your-files)   |     Em um cenário de recuperação de desastre, se você estiver usando a Sincronização de Arquivos do Azure, algumas ações manuais precisam ser realizadas durante o failover para fazer com que os compartilhamentos de arquivos fiquem acessíveis de maneira transparente ao computador cliente. O AFS requer que a porta 445 esteja abertas no computador cliente.     |


### <a name="site-recovery-support"></a>Suporte do Site Recovery
Já que a replicação do Site Recovery é independente do aplicativo, as recomendações fornecidas aqui devem servir também para os seguintes cenários:
| Fonte    |Para um site secundário    |Para o Azure
|---------|---------|---------|
|As tabelas| -|Sim|
|Hyper-V|   Sim |Sim
|VMware |Sim|   Sim
|Servidor físico|   Sim |Sim
 

> [!IMPORTANT]
> Antes de prosseguir com uma das três abordagens abaixo, verifique se as seguintes dependências serão atendidas:

**Conectividade Site a Site**: a conexão direta entre o site local e a rede do Azure deve ser estabelecida para permitir a comunicação entre servidores.  Isso pode ser garantido por uma conexão VPN Site a Site segura para uma Rede Virtual do Microsoft Azure que será usada como o local de recuperação de desastre.  
Consulte [Estabelecer uma conexão VPN Site a Site entre o site local e a rede do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory**: a DFSR depende do Active Directory.  Isso significa que a floresta do Active Directory com controladores de domínio locaia é estendida para o local de recuperação de desastre no Azure. Mesmo se você não está usando DFSR, se os usuários precisarem receber acesso ou ter o acesso verificado como na maioria das organizações, estas etapas precisarão ser executadas.
Consulte [Estender o Active Directory no local para o Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recomendação de recuperação de desastre para máquinas virtuais IaaS do Azure

Se você estiver configurando e gerenciando a recuperação de desastre de servidores de arquivos hospedados em VMs IaaS do Azure, poderá escolher entre duas opções, dependendo da sua vontade de migrar para os [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Usar a Sincronização de Arquivos do Azure](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Usar o Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Usar o serviço Sincronização de Arquivos do Azure para replicar arquivos hospedados na máquina virtual IaaS

Os **Arquivos do Azure** podem ser usados para substituir completamente ou complementar os servidores de arquivos tradicionais no local ou dispositivos NAS. Os compartilhamentos dos Arquivos do Azure também podem ser replicados com Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados. As etapas a seguir detalham a recomendação de recuperação de desastre para VMs do Azure que executam a mesma funcionalidade que os servidores de arquivos tradicionais:
1.  Proteger máquinas usando o Azure Site Recovery com as etapas mencionadas [aqui](azure-to-azure-quickstart.md).
2.  Use a Sincronização de Arquivos do Azure para replicar arquivos da VM que atua como servidor de arquivos para a nuvem.
3.  Use o recurso de [plano de recuperação](site-recovery-create-recovery-plans.md) do Azure Site Recovery para adicionar scripts a fim de [montar o compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e acessar o compartilhamento em sua máquina virtual.

As etapas abaixo descrevem brevemente como usar o serviço Sincronização de Arquivos do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se você tiver escolhido RA-GRS (armazenamento com redundância geográfica com acesso de leitura) para suas contas de armazenamento, terá acesso de leitura aos dados da região secundária em caso de desastres. Consulte as [estratégias de recuperação de desastre do compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para saber mais.
2. [Crie um compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implantar a Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no servidor de arquivos do Azure.
4. Criar um Grupo de Sincronização: os pontos de extremidade em um Grupo de Sincronização serão mantidos em sincronia entre si. Um Grupo de Sincronização deve conter pelo menos um ponto de extremidade de Nuvem, que representa um compartilhamento de Arquivo do Azure e um ponto de extremidade do servidor, que representa um caminho em um Windows Server.
5.  Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e seu servidor local.
6.  Em caso de desastre no seu ambiente local, faça failover usando um [plano de recuperação](site-recovery-create-recovery-plans.md) e adicione o script para [montar o compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e acessar o compartilhamento em sua máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Replicar uma máquina virtual de servidor de arquivos IaaS usando o Azure Site Recovery

Se você tiver clientes locais acessando a máquina virtual de servidor de arquivos IaaS, execute as duas primeiras etapas; do contrário, prossiga para a etapa 3.

1. Estabelecer uma conexão VPN Site a Site entre o site local e a rede do Azure.
1. Estender o Active Directory local.
1. [Configurar a recuperação de desastre](azure-to-azure-tutorial-enable-replication.md) para o computador do servidor de arquivos IaaS em uma região secundária.


Para saber mais sobre recuperação de desastre em uma região secundária, consulte [este documento](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Replicar um servidor de arquivos local usando o Azure Site Recovery

As etapas abaixo detalham a replicação para uma VM VMware; para obter as etapas de replicação de uma VM Hyper-V, consulte [aqui](tutorial-hyper-v-to-azure.md).

1.  [Preparar recursos do Azure](tutorial-prepare-azure.md) para replicação de máquinas locais.
2.  Estabelecer uma conexão VPN Site a Site entre o site local e a rede do Azure.  
3. Estender o Active Directory local.
4.  [Preparar servidores VMware locais](tutorial-prepare-on-premises-vmware.md).
5.  [Configurar a recuperação de desastre](tutorial-vmware-to-azure.md) de VMs locais para o Azure.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Estender a DFSR para uma máquina virtual IaaS do Azure:

1.  Estabelecer uma conexão VPN Site a Site entre o site local e a rede do Azure. 
2.  Estender o Active Directory local.
3.  [Criar e provisionar uma VM do servidor de arquivos](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) na rede virtual do Microsoft Azure.
Adicione a máquina virtual à mesma rede virtual do Microsoft Azure que tem conexão com o ambiente local. 
4.  Instalar e [configurar a replicação do DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) no Windows Server.
5.  [Implementar um namespace do DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Com o Namespace do DFS implementado, o failover das pastas compartilhadas de produção para sites de recuperação de desastre pode ser feito atualizando os destinos de pasta do namespace do DFS.  Depois que essas alterações de namespace do DFS replicam por meio do Active Directory, os usuários estão conectados aos destinos de pasta apropriada de forma transparente.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Use o serviço Sincronização de Arquivos do Azure para replicar seus arquivos locais:
Usando o serviço Sincronização de Arquivos do Azure, você pode replicar os arquivos desejados para a nuvem, para que, em caso de desastre e indisponibilidade do servidor de arquivos local, você possa montar os locais de arquivo desejados da nuvem no e continuar a atender a solicitações dos computadores clientes.
A abordagem sugerida de integração da Sincronização de Arquivos do Azure com o Azure Site Recovery é
1.  Proteger computadores do servidor de arquivos usando o Azure Site Recovery com as etapas mencionadas [aqui](tutorial-vmware-to-azure.md).
2.  Use a Sincronização de Arquivos do Azure para replicar arquivos do computador que atua como um servidor de arquivos para a nuvem.
3.  Use o recurso de plano de recuperação do Azure Site Recovery para adicionar scripts a fim de montar o compartilhamento de arquivos do Azure na VM do servidor de arquivos com failover no Azure.

As etapas abaixo detalham o uso do serviço Sincronização de Arquivos do Azure:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se você escolheu RA-GRS (armazenamento com redundância geográfica com acesso de leitura) (recomendado) para suas contas de armazenamento, terá acesso de leitura aos dados da região secundária em caso de desastre. Consulte as [estratégias de recuperação de desastre do compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para saber mais.
2. [Crie um compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implantar a Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de arquivos local.
4. Criar um Grupo de Sincronização: os pontos de extremidade em um Grupo de Sincronização serão mantidos em sincronia entre si. Um Grupo de Sincronização deve conter pelo menos um ponto de extremidade de Nuvem, que representa um compartilhamento de Arquivo do Azure e um ponto de extremidade do servidor, que representa um caminho no Windows Server local.
1. Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e seu servidor local.
6.  Em caso de desastre no seu ambiente local, faça failover usando um [plano de recuperação](site-recovery-create-recovery-plans.md) e adicione o script para montar o compartilhamento de arquivos do Azure e acessar o compartilhamento em sua máquina virtual.

> [!NOTE]
> Verifique se a porta 445 está aberta: os Arquivos do Azure usam o protocolo SMB. O SMB se comunica pela porta TCP 445: confira se o firewall não está bloqueando as portas TCP 445 do computador cliente.


## <a name="doing-a-test-failover"></a>Executar um failover de teste

1.  Acesse o Portal do Azure e selecione seu cofre do Serviço de Recuperação.
2.  Clique no plano de recuperação criado para o ambiente de servidor de arquivos.
3.  Clique em 'Failover de Teste'.
4.  Selecione o ponto e recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
5.  Quando o ambiente secundário estiver funcionando, você poderá executar sua validações.
6.  Depois que as validações forem concluídas, você poderá clicar em 'Limpar failover de teste' no plano de recuperação e o ambiente de failover de teste será limpo.

Para saber mais sobre como executar o failover de teste, confira [este documento](site-recovery-test-failover-to-azure.md).

Para obter diretrizes sobre como fazer failover de teste do AD e DNS, consulte [Considerações sobre failover de teste para o AD e DNS](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Executar um failover

1.  Vá para o Portal do Azure e selecione seu cofre dos Serviços de Recuperação.
2.  Clique no plano de recuperação criado para o ambiente de servidor de arquivos.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de failover.

Para saber mais sobre como executar o failover de teste, confira [este documento](site-recovery-failover.md).
