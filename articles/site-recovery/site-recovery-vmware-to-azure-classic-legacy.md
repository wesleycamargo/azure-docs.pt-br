<properties pageTitle="Replicar as máquinas virtuais VMware e os servidores físicos no Azure com o Azure Site Recovery (herdado) | Microsoft Azure" description="Descreve uma implantação herdada para a configuração do Azure Site Recovery a fim de administrar a replicação, o failover e a recuperação de máquinas virtuais VMware locais e de servidores físicos com o Windows/Linux no Azure." " services="site-recovery" documentationCenter="" authors="rayne-wiselman" manager="jwhit" editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="raynew"/>

# Replicar as máquinas virtuais VMware e os servidores físicos no Azure com o Azure Site Recovery (herdado)

> [AZURE.SELECTOR]
- [Avançado](site-recovery-vmware-to-azure-classic.md)
- [Herdado](site-recovery-vmware-to-azure-classic-legacy.md)


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para obter uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

## Visão geral

Este artigo descreve como:

- **Replicar máquinas virtuais do VMware no Azure** – Implante a Recuperação de Site para coordenar a replicação, o failover e a recuperação de máquinas virtuais locais do VMware no armazenamento do Azure.
- **Replicar servidores físicos no Azure** – Implante o Azure Site Recovery para coordenar a replicação, o failover e a recuperação de servidores físicos locais Windows e Linux no Azure.

>[AZURE.NOTE] O cenário descrito neste artigo inclui **instruções herdadas**. Não siga este artigo para novas implantações. Em vez disso, use as instruções de [implantação avançada](site-recovery-vmware-to-azure-classic.md) para o portal clássico). Se você já tiver implantado usando o método descrito neste artigo, recomendamos que você migre para a nova versão, como descrito abaixo.


## Migrar para a implantação avançada

Esta seção só será relevante se você já tiver implantado a replicação de máquinas virtuais VMware ou de servidores físicos com o Windows/Linux no Azure usando as instruções deste artigo.

Para migrar a implantação existente, você precisará:

1. Implantar novos componentes da Recuperação de Site no site local.
2. Configurar as credenciais do servidor vCenter e as máquinas de origem no novo servidor de configuração.
3. Descobrir o servidor vCenter existente com o novo servidor de configuração.
3. Criar um novo grupo de proteção com o novo servidor de configuração.


Antes de começar, observe que:

- Recomendamos o planejamento de uma janela de manutenção para migrar para a implantação avançada.
- A opção **Migrar Máquinas** só estará disponível se você tiver grupos de proteção existentes criados durante uma implantação herdada.
- Depois de concluir as etapas de migração, talvez demore 15 minutos ou mais para atualizar as credenciais e para descobrir e atualizar as máquinas virtuais para que você possa adicioná-las a um grupo de proteção. Você pode atualizar manualmente em vez de esperar. 

Migre da seguinte maneira:

1. Leia sobre os [recursos avançados](site-recovery-vmware-to-azure-classic.md#enhanced-deployment), verifique se você entendeu a nova [arquitetura](site-recovery-vmware-to-azure-classic.md#scenario-architecture) e verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) para a implantação avançada.
2. Desinstale o serviço de Mobilidade dos computadores que você está protegendo atualmente. Uma nova versão do serviço de Mobilidade será instalada nos computadores quando você adicioná-lo ao novo grupo de proteção
3. Obtenha uma [chave de registro do cofre](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) e [execute o assistente de instalação unificada](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) para instalar o servidor de configuração, o servidor de processo e os componentes do servidor de destino mestre no servidor de gerenciamento. Leia mais sobre o [planejamento de capacidade](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. Se você tiver um servidor VMware vCenter, [configure as credenciais](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) para acessá-lo de modo que a Recuperação de Site possa descobrir automaticamente as VMs que ele gerencia. Leia mais sobre as [permissões necessárias](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Adicione os [servidores vCenter ou hosts ESXi](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Pode demorar até 15 minutos para que o portal seja atualizado e as credenciais apareçam.
6. Crie um [novo grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Pode demorar até 15 minutos para que o portal seja atualizado e as máquinas virtuais sejam descobertas e exibidas. Se você não quiser esperar, realce o nome do servidor de gerenciamento (não clique nele) > **Atualizar**.
7. No novo grupo de proteção, clique em **Migrar Máquinas**.

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Em **Selecionar Máquinas**, selecione o grupo de proteção do qual você deseja migrar e as máquinas que deseja migrar.

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. Em **Definir Configurações de Destino**, especifique se deseja usar as mesmas configurações para todos os computadores e selecione o servidor de processo e a conta de armazenamento do Azure. Se você tiver configurado um servidor de gerenciamento único, o servidor de processo será o endereço IP desse servidor de gerenciamento.

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

10. Em **Especificar Contas**, selecione a conta criada para automaticamente enviar por push a nova versão do serviço de Mobilidade para os computadores protegidos.

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. A Recuperação de Site migrará os dados replicados para a conta de armazenamento do Azure que você forneceu. Opcionalmente, você pode reutilizar a conta de armazenamento usada na implantação herdada.
12. Após a conclusão do trabalho, as máquinas virtuais serão sincronizadas automaticamente. Após a conclusão da sincronização, você poderá excluir as máquinas virtuais do grupo de proteção herdado.
13. Após a migração de todas as máquinas, você poderá excluir o grupo de proteção herdado.
14. Lembre-se de especificar as propriedades de failover das máquinas e as configurações de rede do Azure após a conclusão da sincronização.
15. Se tiver planos de recuperação existentes, você poderá migrá-los para a implantação avançada com a opção **Plano de Recuperação de Migração**. Você só deverá fazer isso após a migração de todas as máquinas protegidas. 

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Depois de concluir as etapas de migração, você deve continuar com o [artigo avançado](site-recovery-vmware-to-azure-classic.md). Após a migração, o restante deste artigo herdado não é relevante, e você não precisa mais seguir as etapas descritas nele**.




## Do que eu preciso?

Este diagrama ilustra os componentes de implantação.

![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Você precisará de:

**Componente** | **Implantação** | **Detalhes**
--- | --- | ---
**Servidor de configuração** | <p>Implante como uma máquina virtual padrão A3 do Azure na mesma assinatura do Site Recovery.</p> <p>Configure no portal do Site Recovery</p> | Esse servidor coordena a comunicação entre computadores protegidos, o servidor de processo e os servidores de destino mestre no Azure. Ele configura a replicação e coordena a recuperação no Azure quando o failover ocorre.
**Servidor de destino mestre** | <p>Implante como uma máquina virtual do Azure, seja como um servidor Windows baseado em uma imagem da galeria do Windows Server 2012 R2 (para proteger computadores Windows), seja como um servidor Linux baseado em uma imagem da galeria do OpenLogic CentOS 6.6 (para proteger computadores Linux).</p> <p>Três opções de dimensionamento estão disponíveis – A4 padrão, D14 padrão e DS4 padrão.<p><p>O servidor está conectado à mesma rede do Azure que o servidor de configuração.</p><p>Configurar o portal de recuperação de Site</p> | <p>Ele recebe e mantém os dados replicados de seus computadores protegidos usando VHDs conectados criados no armazenamento de blob em sua conta de armazenamento do Azure.</p> <p>Selecione padrão DS4 especificamente para configuração da proteção para cargas de trabalho que exigem um alto desempenho consistente e baixa latência usando a conta de armazenamento Premium.</p>
**Servidor de processo** | <p>Implante como um servidor físico ou virtual local executando o Windows Server 2012 R2</p> <p>É recomendável colocá-lo no mesmo segmento LAN e rede que os computadores que deseja proteger, mas pode ser executado em uma rede diferente, desde que os computadores protegidos tenham visibilidade de rede L3 dele.<p>Configure-o e registre-o no servidor de configuração no portal do Site Recovery.</p> | <p>Os computadores protegidos enviam dados de replicação para o servidor de processo local. Ele tem dados de replicação de cache para cache baseada em disco que recebe. Ele executa várias ações nesses dados.</p><p>Ele otimiza dados armazenando-os em cache, compactando-os e criptografando-os antes de enviá-los ao servidor de destino mestre.</p><p>Ele trata da instalação por push do Serviço de Mobilidade.</p><p>Ele executa a descoberta automática das máquinas virtuais VMware.</p>
**Computadores locais** | Em máquinas virtuais locais executadas em um hipervisor VMware ou servidores físicos executando Windows ou Linux. | Defina configurações de replicação que se aplicam a máquinas virtuais e servidores. É possível fazer failover de um computador individual ou, mais frequentemente, como parte de um plano de recuperação que contém várias máquinas virtuais que fazem failover juntas.
**Serviço de mobilidade** | <p>É instalado em cada máquina virtual ou servidor físico que você deseja proteger</p><p>Pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção está habilitada para o servidor. | O serviço de mobilidade envia dados para o Servidor de processo como parte da replicação inicial (ressincronização.) Depois que o servidor atinge um estado protegido (após a ressincronização ser concluída), o Serviço de Mobilidade executa uma captura na memória de gravações em disco e a envia para o Servidor de processo. A consistência de aplicativos para servidores Windows é obtida com a estrutura VSS.
**Cofre do Azure Site Recovery** | Configure depois de assinar o serviço Site Recovery. | Registre os servidores em um cofre do Site Recovery. O cofre coordena e administra a replicação de dados, o failover e a recuperação entre o site local e o Azure.
**Mecanismo de replicação** | <p>**Pela Internet**: comunica e replica dados dos servidores locais protegidos e do Azure usando um canal de comunicação SSL/TLS seguro por uma conexão pública de Internet. Essa é a opção padrão.</p><p>**VPN/Rota Expressa**: comunica e replica dados entre servidores locais e o Azure por uma conexão VPN. Você precisará configurar uma conexão VPN site a site ou de Rota Expressa entre o site local e a rede do Azure.</p><p>Você selecionará como deseja replicar durante a implantação da Recuperação de Site. Não será possível alterar o mecanismo depois que ele tiver sido configurado sem afetar a proteção em servidores já protegidos.| <p>Nenhuma opção exige que você abra portas de rede de entrada em computadores protegidos. Toda a comunicação de rede é iniciada no site local.</p> 

## Planejamento da capacidade

As principais áreas de consideração são:

- **Ambiente de origem**—a infraestrutura, as configurações do computador de origem e os requisitos da VMware.
- **Servidores de componente**—o servidor de processo, o servidor de configuração e o servidor de destino mestre. 

### Considerações para o ambiente de origem

- **Tamanho máximo do disco**—o tamanho máximo atual do disco que pode ser conectado a uma máquina virtual é de 1 TB. Desse modo, o tamanho máximo de um disco de origem que pode ser replicado também é limitado a 1 TB.
- **Tamanho máximo por origem**—o tamanho máximo de um único computador de origem é de 31 TB (com 31 discos) e com uma instância D14 provisionada para o servidor de destino mestre. 
- **Número de origens por servidor de destino mestre**—vários computadores de origem podem ser protegidos com um único servidor de destino mestre. No entanto, um único computador de origem não pode ser protegido entre vários servidores de destino mestre, pois à medida que os discos são replicados, um VHD que espelha o tamanho do disco é criado no armazenamento de blobs do Azure e conectado como um disco de dados ao servidor de destino mestre.  
- **Taxa máxima de alteração diária por origem**—há três fatores que precisam ser lembrados ao considerar a taxa de alteração recomendada por origem. Para as considerações baseadas no destino, dois IOPS são necessários no disco de destino para cada operação na origem. Isso porque uma leitura de dados antigos e uma gravação de novos dados acontecerão no disco de destino. 
	- **Taxa de alteração diária com suporte do servidor de processo**—um computador de origem não pode abranger vários servidores de processo. Um único servidor de processo pode dar suporte a até 1 TB de taxa de alteração diária. Portanto, 1 TB é a taxa máxima de alteração de dados diária com suporte para uma máquina de origem. 
	- **Taxa de transferência máxima com suporte do disco de destino**—a variação máxima por disco de origem não pode ser superior a 144 GB/dia (com um tamanho de gravação de 8 K). Confira a tabela na seção de destino mestre para ver a taxa de transferência e o IOPs do destino de vários tamanhos de gravação. Esse número deve ser dividido por dois, pois cada IOP gera 2 IOPS no disco de destino. Leia sobre [Metas de desempenho e escalabilidade do Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar o destino para contas de armazenamento premium.
	- **Taxa de transferência máxima com suporte da conta de armazenamento**—uma origem não pode abranger várias contas de armazenamento. Supondo que uma conta de armazenamento obtenha um máximo de 20.000 solicitações por segundo e que cada IOP de origem gere 2 IOPS no servidor de destino mestre, é recomendável manter o número de IOPS na origem em 10.000. Leia sobre [Metas de desempenho e escalabilidade do Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar a origem para contas de armazenamento premium.

### Considerações para servidores de componente

A Tabela 1 resume os tamanhos de máquina virtual para os servidores de destino mestre e de configuração.

**Componente** | **Instâncias do Azure implantadas** | **Núcleos** | **Memória** | **Máx. de discos** | **Tamanho do disco**
--- | --- | --- | --- | --- | ---
Servidor de configuração | Padrão A3 | 4 | 7 GB | 8 | 1023 GB
Servidor de destino mestre | Padrão A4 | 8 | 14 GB | 16 | 1023 GB
 | Padrão D14 | 16 | 112 GB | 32 | 1023 GB
 | DS4 padrão | 8 | 28 GB | 16 | 1023 GB

**Tabela 1**

#### Considerações do servidor de processo

Normalmente, o dimensionamento do servidor de processo depende da taxa de alteração diária em todas as cargas de trabalho protegidas. As principais considerações incluem:

-	Você precisa de computação suficiente para executar tarefas como compactação e criptografia embutidas.
-	O servidor de processo usa cache baseado em disco. Verifique se o espaço em cache recomendado e a taxa de transferência de disco estão disponíveis para facilitar as alterações de dados armazenados em caso de afunilamento ou interrupção de rede. 
-	Garanta largura de banda suficiente para que o servidor de processo possa carregar os dados no servidor de destino mestre de modo a fornecer proteção de dados contínua. 

A Tabela 2 fornece um resumo das diretrizes do servidor de processo.

**Taxa de alteração de dados** | **CPU** | **Memória** | **Tamanho do disco de cache**| **Taxa de transferência do disco de cache** | **Entrada/saída da largura de banda**
--- | --- | --- | --- | --- | ---
Menos de 300 GB | 4 vCPUs (2 soquetes x 2 núcleos a 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB por segundo | 30 Mbps/21 Mbps
300 a 600 GB | 8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz) | 6 GB | 600 GB | 11 a 15 MB por segundo | 60 Mbps/42 Mbps
600 GB a 1 TB | 12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz) | 8 GB | 600 GB | 16 a 20 MB por segundo | 100 Mbps/70 Mbps
Mais de 1 TB | Implantar outro servidor de processo | | | | 

**Tabela 2**

Em que:

- Entrada é a largura de banda para download (intranet entre a origem e o servidor de processo).
- Saída é a largura de banda para carregamento (internet entre o servidor de processo e o servidor de destino mestre). Os números de saída presumem uma compactação média de 30% do servidor de processo.
- Para disco de cache, um disco separado do sistema operacional de 128 GB, no mínimo, é recomendável para todos os servidores de processo.
- Para taxa de transferência do disco de cache foi usado o seguinte armazenamento para parâmetros de comparação: 8 unidades SAS de RPM de 10 K com configuração RAID 10.

#### Considerações do servidor de configuração

Cada servidor de configuração pode dar suporte a até 100 computadores de origem com 3 a 4 volumes. Se esses números forem excedidos, é recomendável implantar outro servidor de configuração. Confira a Tabela 1 para ver as propriedades de máquina virtual padrão do servidor de configuração.

#### Considerações de conta de armazenamento e servidor de destino mestre

O armazenamento de cada servidor de destino mestre contém um disco de sistema operacional, um volume de retenção e discos de dados. A unidade de retenção mantém o diário das alterações do disco pelo tempo de duração da janela de retenção definida no portal do Site Recovery. Confira a Tabela 1 para ver as propriedades de máquina virtual do servidor de destino mestre. A Tabela 3 mostra como os discos de A4 são usados.

**Instância** | **Disco do sistema operacional** | **Retenção** | **Discos de dados**
--- | --- | --- | ---
 | | **Retenção** | **Discos de dados**
Padrão A4 | 1 disco (1 x 1023 GB) | 1 disco (1 x 1023 GB) | 15 discos (15 x 1023 GB)
Padrão D14 | 1 disco (1 x 1023 GB) | 1 disco (1 x 1023 GB) | 31 discos (15 x 1023 GB)
DS4 padrão | 1 disco (1 x 1023 GB) | 1 disco (1 x 1023 GB) | 15 discos (15 x 1023 GB)

**Tabela 3**

O planejamento de capacidade para o servidor de destino mestre depende:

- Das limitações e do desempenho do armazenamento do Azure
	- O número máximo de discos altamente utilizados para uma VM de camada padrão, é de cerca de 40 (20.000/500 IOPS por disco) em uma única conta de armazenamento. Leia sobre [metas de escalabilidade para contas de armazenamento padrão](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) e para [contas de armazenamento premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-	Da taxa de alteração diária 
-	Do armazenamento do volume de retenção.

Observe que:

- Uma fonte não pode incluir várias contas de armazenamento. Isso se aplica ao disco de dados que vai para as contas de armazenamento selecionadas quando você configura a proteção. O sistema operacional e os discos de retenção normalmente vão para a conta de armazenamento implantada automaticamente.
- O volume do armazenamento de retenção necessário depende da taxa de alteração diária e do número de dias de retenção. O armazenamento de retenção necessário por servidor de destino mestre = variação total da origem por dia x número de dias de retenção. 
- Cada servidor de destino mestre tem apenas um volume de retenção. O volume de retenção é compartilhado entre os discos conectados ao servidor de destino mestre. Por exemplo:
	- Se houver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8 K de tamanho) na origem, isso se traduzirá em 240 IOPS por disco (2 operações no disco de destino por E/S de origem). 240 IOPS está dentro do limite de 500 IOPS por disco do Azure.
	- No volume de retenção, isso significa 120 x 5 = 600 IOPS e isso pode se transformar em um afunilamento. Nesse cenário, uma boa estratégia é adicionar mais discos ao volume de retenção e estendê-lo, como uma configuração de distribuição de RAID. Isso melhora o desempenho, pois o IOPS é distribuído entre várias unidades. O número de unidades a ser adicionada ao volume de retenção será da seguinte maneira:
		- Total de IOPS do ambiente de origem / 500
		- Variação total por dia do ambiente de origem (descompactado) / 287 GB. 287 GB é a taxa de transferência máxima com suporte em um disco de destino por dia. Essa métrica variará com base no tamanho da gravação com um fator de 8 K, pois, nesse caso, 8 K é o tamanho da gravação assumido pela árvore. Por exemplo, se o tamanho da gravação for 4 K, a taxa de transferência será 287/2. E se o tamanho da gravação for 16 K, a taxa de transferência será 287 x 2.
- O número de contas de armazenamento necessárias = total de IOPs de origem/10000.


## Antes de começar

**Componente** | **Requisitos** | **Detalhes**
--- | --- | --- 
**Conta do Azure** | Você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](pricing/free-trial/).
**Armazenamento do Azure** | <p>Você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados</p><p>A conta deve ser uma [Conta de armazenamento com redundância geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage) ou uma [conta de Armazenamento Premium](../storage/storage-premium-storage.md).</p><p>Ele deve localizada na mesma região que o serviço de Azure Site Recovery e ser associada à mesma assinatura.</p><p>Para saber mais leia [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md)</p>
**Rede virtual do Azure** | Você precisará de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implantados. Ela deve estar na mesma assinatura e na mesma região que o cofre do Azure Site Recovery. Se você quiser replicar dados em uma conexão VPN ou da Rota Expressa, a rede virtual do Azure deve estar conectada à sua rede local através de uma conexão da Rota Expressa ou uma VPN Site a Site.
**Recursos do Azure** | Verifique se você tem recursos suficientes do Azure para implantar todos os componentes. Leia mais em [Limites de assinatura do Azure](../azure-subscription-service-limits.md).
**Máquinas virtuais do Azure** | <p>As máquinas virtuais que você deseja proteger devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-best-practices.md).</p><p>**Contagem de discos**—há suporte para um máximo de 31 discos em um único servidor protegido</p><p>**Tamanhos de disco**: a capacidade de disco individual não deve ultrapassar 1023 GB</p><p>**Clustering**: não há suporte para servidores clusterizados</p><p>**Inicialização**—não há suporte para inicialização de UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface)</p><p>**Volumes**—não há suporte para volumes criptografados pelo Bitlocker</p><p> **Nomes de servidor**—os nomes devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois que um computador é protegido, você pode modificar o nome do Azure.</p>
**Servidor de configuração** | <p>Uma máquina virtual padrão A3 baseada em uma imagem da galeria do Windows Server 2012 R2 do Azure Site Recovery será criada em sua assinatura para o servidor de configuração. Ela é criada como a primeira instância em um novo serviço de nuvem. Se você selecionar Internet pública como o tipo de conectividade para o servidor de configuração, o serviço de nuvem será criado com um endereço IP público reservado.</p><p>O caminho de instalação deve ter somente caracteres em inglês.</p>
**Servidor de destino mestre** | <p>Máquina virtual do Azure, A4 padrão, D14 ou DS4.</p><p>O caminho de instalação deve ter somente caracteres em inglês. Por exemplo, o caminho deve ser **/usr/local/ASR** para um servidor de destino mestre executando Linux.</p></p>
**Servidor de processo** | <p>Você pode implantar o servidor de processo no computador físico ou na máquina virtual quer executa o Windows Server 2012 R2 com as últimas atualizações. Instale em C:/.</p><p>É recomendável colocar o servidor na mesma rede e sub-rede que os computadores que deseja proteger.</p><p>Instale o VMware vSphere CLI 5.5.0 no servidor de processo. O componente VMware vSphere CLI é necessário no servidor de processo para descobrir máquinas virtuais gerenciadas por um Servidor vCenter ou máquinas virtuais que são executadas em um host ESXi.</p><p>O caminho de instalação deve ter somente caracteres em inglês.</p><p>O sistema de arquivos ReFS não tem suporte.</p>
**VMware** | <p>Um VMware Servidor vCenter gerenciando seus hipervisores do VMware vSphere. Ele deve executar o vCenter versão 5.1 ou 5.5 com as últimas atualizações.</p><p>Um ou mais hipervisores do vSphere contendo máquinas virtuais VMware que você deseja proteger. O hipervisor deve executar o ESX/ESXi versão 5.1 ou 5.5 com as últimas atualizações.</p><p>As máquinas virtuais VMware devem ter as ferramentas VMware instaladas e em execução.</p>  
**Computadores Windows** | <p>Máquinas virtuais VMware ou servidores físicos protegidos executando Windows têm vários requisitos.</p><p>Um sistema operacional de 64 bits com suporte: **Windows Server 2012 R2**, **Windows Server 2012** ou **Windows Server 2008 R2 com, pelo menos, SP1**.</p><p>O nome do host, os pontos de montagem, os nomes de dispositivo, o caminho do sistema Windows (por ex.: C:\\Windows) devem estar somente em inglês.</p><p>O sistema operacional deve estar instalado na unidade C:\\.</p><p>Somente discos básicos têm suporte. Não há suporte para discos dinâmicos.</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>Você precisará fornecer uma conta de administrador (deve ser um administrador local no computador Windows) para instalar por push o Serviço de Mobilidade nos servidores Windows. Se a conta fornecida não for uma conta de domínio, você precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso, adicione a entrada de registro LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Para adicionar a entrada de registro de uma CLI, abra o cmd ou o powershell e insira **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Saiba mais](https://msdn.microsoft.com/library/aa826699.aspx) sobre controle de acesso.</p><p>Após o failover, se desejar se conectar a máquinas virtuais do Windows no Azure com a Área de Trabalho Remota, verifique se a Área de Trabalho Remota está habilitada para o computador local. Se você não estiver se conectando por meio de uma VPN, as regras de firewall deverão permitir conexões da Área de Trabalho Remota pela Internet.</p>
**Computadores Linux** | <p> Um sistema operacional de 64 bits com suporte: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível do Red Hat ou Unbreakable Enterprise Kernel versão 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.</p><p>As regras de firewall em computadores protegidos devem permitir que eles acessem os servidores de destino mestre e de configuração no Azure.</p><p>Arquivos /etc/hosts em computadores protegidos devem conter entradas que mapeiem o nome do host local para endereços IP associados a todas as NICs </p><p>Se deseja se conectar a uma máquina virtual do Azure executando o Linux após o failover usando um cliente Secure Shell (ssh), certifique-se de que o serviço Secure Shell no computador protegido esteja definido para iniciar automaticamente na inicialização do sistema e que as regras de firewall permitam uma conexão ssh com ele.</p><p>O nome do host, os pontos de montagem, os nomes de dispositivo, assim como os nomes de arquivo e caminhos do sistema (por ex.: /etc/;/usr) devem estar somente em inglês.</p><p>A proteção pode ser habilitada para computadores locais com o armazenamento a seguir: -<br>Sistema de arquivos: EXT3, ETX4, ReiserFS, XFS<br>Multipath software-Device Mapper (vários caminhos)<br>Gerenciador de volumes: LVM2<br>Não há suporte para servidores físicos com armazenamento de controlador HP CCISS.</p>
**Terceiros** | Alguns componentes de implantação neste cenário dependem de software de terceiros para funcionar corretamente. Para obter uma lista completa, confira [Avisos e informações de software de terceiros](#third-party)

## Implantação

O elemento gráfico resume as etapas da implantação.

![Etapas de implantação.](./media/site-recovery-vmware-to-azure-classic-legacy/deployment-steps.png)

## Conectividade de rede

Você tem duas opções ao configurar a conectividade de rede entre o site local e a rede virtual do Azure no qual os componentes de infraestrutura (servidor de configuração, servidores de destino mestre) são implantados. Você precisará decidir qual opção de conectividade de rede vai usar antes de implantar o servidor de configuração. Você precisará escolher essa configuração no momento da implantação. Ele não pode ser alterado posteriormente.

**Internet Pública:** a comunicação e a replicação de dados entre os servidores locais (servidor de processo, computadores protegidos) e os servidores de componentes da infraestrutura do Azure (servidor de configuração, servidor de destino mestre) ocorrem em uma conexão SSL/TLS segura dos pontos de extremidade locais para os pontos de extremidade públicos nos servidores de destino mestre e de configuração. (A única exceção é a conexão entre o servidor de processo e o servidor de destino mestre na porta TCP 9080 que será criptografada. Somente as informações de controle relacionadas ao protocolo de replicação para a configuração de replicação são trocadas nesta conexão.)

![Diagrama de implementação para Internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: a comunicação e a replicação de dados entre os servidores locais (servidor de processo, computadores protegidos) e os servidores de componentes da infraestrutura do Azure (servidor de configuração, servidor de destino mestre) ocorrem em uma conexão VPN entre a rede local e a rede virtual do Azure em que o servidor de configuração e os servidores de destino mestre são implantados. Certifique-se de que sua rede local esteja conectada à rede virtual do Azure por uma conexão da Rota Expressa ou uma conexão VPN site a site.

![Diagrama de implementação para VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## Etapa 1: criar um cofre

1. Entre no [Portal de Gerenciamento](https://portal.azure.com).


2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.


3. Clique em **Criar Novo** > **Criação Rápida**.

4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](pricing/details/site-recovery/)

6. Clique em **Criar cofre**.

	![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.

## Etapa 2: implantar um servidor de configuração

### Definir configurações do servidor

1. Na página **Serviços de Recuperação**, clique no cofre para abrir na página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Ícone de Inicialização Rápida](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Na lista suspensa, selecione **Entre um site local com os servidores VMware/físicos e o Azure**.
3. Em **Preparar Recursos de Destino do Azure**, clique em **Implantar Servidor de Configuração**.

	![Implantar servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Em **Novos Detalhes do Servidor de Configuração**, especifique:

	- Um nome para o servidor de configuração e credenciais para se conectar a ele.
	- No menu suspenso com os tipos de conectividade de rede, selecione Internet pública ou VPN.[AZURE.NOTE] Esta configuração é uma escolha feita no momento da implantação, que não pode ser alterada posteriormente.  
	- Selecione a rede do Azure na qual o servidor deve estar localizado. Se você especificou VPN como o tipo de conectividade de rede, verifique se a vnet do Azure está conectada ao seu site local através de uma conexão da Rota Expressa ou uma VPN site a site.
	- O endereço IP interno e a sub-rede a serem atribuídos ao servidor. Observe que os quatro primeiros endereços IP em qualquer sub-rede são reservados para uso interno do Azure. Use qualquer outro endereço IP disponível.
	
	![Implantar servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Quando você clicar em **OK**, uma máquina virtual padrão A3 baseada em uma imagem da galeria do Windows Server 2012 R2 do Azure Site Recovery será criada em sua assinatura para o servidor de configuração. Ela é criada como a primeira instância em um novo serviço de nuvem. Se você especificou o tipo de conectividade de rede como Internet pública, o serviço de nuvem é criado com um endereço IP público reservado. Você pode monitorar o progresso na guia **Trabalhos**.

	![Monitorar o progresso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  **Esta etapa é aplicável somente se o tipo de conectividade for Internet pública.** Depois que o servidor de configuração for implantado, anote o endereço IP público atribuído a ele na página **Máquinas Virtuais** no portal do Azure. Em seguida, na guia **Pontos de Extremidade**, anote a porta pública HTTPS mapeada para a porta privada 443. Você precisará dessas informações posteriormente quando registrar is servidores de destino mestre e em processo no servidor de configuração. O servidor de configuração é implantado com estes pontos de extremidade:

	- HTTPS: a porta pública é usada para coordenar a comunicação entre servidores de componente e o Azure pela Internet. A porta privada 443 é usada para coordenar a comunicação entre servidores de componente e o Azure pela VPN.
	- Personalizado: a porta pública é usada para comunicação da ferramenta de failback pela Inter
	- net. A porta privada 9443 é usada para comunicação da ferramenta de failback pela VPN.
	- PowerShell: porta privada 5986
	- Área de trabalho remota: porta privada 3389
	
	![Pontos de extremidade da VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Não exclua nem altere o número da porta pública ou privada de qualquer um dos pontos de extremidade criados durante a implantação do servidor de configuração.

O servidor de configuração é implantado em um serviço de nuvem do Azure criado automaticamente com um endereço IP reservado. O endereço reservado é necessário para garantir que o endereço IP do serviço de nuvem do Servidor de Configuração permaneça o mesmo entre as reinicializações das máquinas virtuais (incluindo o servidor de configuração) no serviço de nuvem. O cancelamento da reserva do endereço IP público reservado deverá ser feito manualmente quando o servidor de configuração for desativado; caso contrário, ele continuará reservado. Há um limite padrão de 20 endereços IP públicos reservados por assinatura. [Saiba mais](../virtual-network/virtual-networks-reserved-private-ip.md) sobre endereços IP reservados.

### Registrar o servidor de configuração no cofre

1. Na página **Início Rápido**, clique em **Preparar Recursos de Destino** > **Baixar uma chave de registro**. O arquivo de chave é gerado automaticamente. Ele é válido por cinco dias após ter sido gerado. Copie-o no servidor de configuração.
2. Em **Máquinas Virtuais**, selecione o servidor de configuração na lista de máquinas virtuais. Abra a guia **Painel** e clique em **Conectar**. **Abra** o arquivo RDP baixado para fazer logon no servidor de configuração usando a Área de Trabalho Remota. Se o Servidor de configuração for implantado em uma rede VPN, use o endereço IP interno (o endereço IP especificado quando você implantou o servidor de configuração, que também pode ser visto na página do painel de máquinas virtuais da máquina virtual do servidor de configuração) do servidor de configuração para criar uma área de trabalho remota nela por meio de sua rede local. O Assistente para Instalação do Servidor de Configuração do Azure Site Recovery é executado automaticamente quando você faz logon pela primeira vez.

	![Registro](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Em **Instalação de Software de Terceiros**, clique em **Aceitar** para baixar e instalar o MySQL.

	![Instalação do MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. Em **Detalhes do Servidor MySQL**, crie credenciais para fazer logon na instância do servidor MySQL.

	![Credenciais do MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Em **Configurações da Internet**, especifique como o servidor de configuração se conectará à Internet. Observe que:

	- Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o provedor.
	- Quando você clicar em **Avançar**, um teste será executado para verificar a conexão proxy.
	- Se você usar um proxy personalizado ou se o seu proxy padrão exigir autenticação, será preciso inserir os detalhes do proxy, incluindo o endereço, a porta e as credenciais.
	- As URLs a seguir devem poder ser acessadas pelo proxy:
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Se você tiver regras de firewall baseadas no endereço IP, verifique se as regras estão definidas para permitir a comunicação do servidor de configuração com os endereços IP descritos em [Intervalos IP do datacenter do Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) e o protocolo HTTPS (443). Você terá que colocar os intervalos IP em uma lista de permissões da região do Azure que planeja usar e uma para o Oeste dos EUA.

	![Registro do proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Em **Configurações de Localização da Mensagem de Erro do Provedor**, especifique em que idioma você deseja que as mensagens de erro apareçam.

	![Registro de mensagem de erro](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. Em **Registro do Azure Site Recovery**, procure e selecione o arquivo de chave que você copiou para o servidor.

	![Registro do arquivo de chave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Na página de conclusão do assistente, selecione estas opções:

	- Selecione **Iniciar Caixa de Diálogo do Gerenciamento de Contas** para especificar se a caixa de diálogo Gerenciar Contas deve abrir depois que você concluir o assistente.
	- Selecione **Criar um ícone de área de trabalho para Cspsconfigtool** para adicionar um atalho de área de trabalho no servidor de configuração para que seja possível abrir a caixa de diálogo **Gerenciar Contas** em qualquer momento sem precisar executar o assistente novamente.

	![Concluir registro](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Clique em **Concluir** para finalizar o assistente. Uma senha é gerada. Copie-a para um local seguro. Você precisará dela para autenticar e registrar os servidores de processo e de destino mestre no servidor de configuração. Ela também é usada para garantir a integridade do canal nas comunicações do servidor de configuração. É possível gerar novamente a senha, mas, em seguida, você precisará registrar novamente os servidores de destino mestre e de processo usando a nova senha.

	![Senha](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Após o registro, o servidor de configuração será listado na página **Servidores de Configuração** no cofre.

### Configurar e gerenciar contas

Durante a implantação, o Site Recovery solicita credenciais para as seguintes ações:

- Quando você adiciona um servidor vCenter para detecção automática de máquinas virtuais gerenciadas pelo servidor vCenter. Uma conta do vCenter é necessária para a detecção automática de máquinas virtuais.
- Quando você adiciona computadores para proteção, para que o Site Recovery possa instalar o Serviço de Mobilidade neles.

Depois de registrar o servidor de configuração, você poderá abrir a caixa de diálogo **Gerenciar Contas** para adicionar e gerenciar as contas que deverão ser usadas para essas ações. Há algumas maneiras de fazer isso:

- Abra o atalho que você optou por criar para a caixa de diálogo na última página de instalação do servidor de configuração (cspsconfigtool).
- Abra a caixa de diálogo na conclusão da instalação do servidor de configuração.

1. Em **Gerenciar Contas**, clique em **Adicionar Conta**. Você também pode modificar e excluir contas existentes.

	![Gerenciar contas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Em **Detalhes da Conta**, especifique um nome de conta a ser usado no Azure e as credenciais (Domínio/nome de usuário).

	![Gerenciar contas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### Conectar-se ao servidor de configuração 

Há duas maneiras de se conectar ao servidor de configuração:

- Por uma conexão VPN site a site ou de Rota Expressa
- Pela Internet 

Observe que:

- Uma conexão com a Internet usa os pontos de extremidade da máquina virtual em conjunto com o endereço IP virtual público do servidor.
- Uma conexão VPN usa o endereço IP interno do servidor juntamente com as portas privadas do ponto de extremidade.
- É uma decisão ocasional optar por se conectar (dados de replicação e controle) de seus servidores locais a vários servidores de componentes (servidor de configuração, servidor de destino mestre) em execução no Azure por uma conexão VPN ou pela Internet. Você não pode alterar essa configuração posteriormente. Se o fizer, será preciso reimplantar o cenário e proteger os computadores novamente.  


## Etapa 3: implantar o servidor de destino mestre

1. Em **Preparar Recursos de Destino do Azure**, clique em **Implantar servidor de destino mestre**.
2. Especifique os detalhes e as credenciais do servidor de destino mestre. O servidor será implantado na mesma rede que o Azure como o servidor de configuração para o qual você o registrar. Quando você clicar em Concluir, uma máquina virtual do Azure será criada com uma imagem de galeria do Windows ou Linux.

	![Configurações do servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Observe que os quatro primeiros endereços IP em qualquer sub-rede são reservados para uso interno do Azure. Especifique qualquer outro endereço IP disponível.

>[AZURE.NOTE] Selecione DS4 padrão ao configurar a proteção para cargas de trabalho que exigem alto desempenho de E/S consistente e baixa latência para hospedar as cargas de trabalho intensivas de E/S usando a [conta de Armazenamento Premium](../storage/storage-premium-storage.md).


3. Uma máquina virtual do servidor de destino mestre do Windows é criada com esses pontos de extremidade (pontos de extremidade públicos são criados somente se o tipo de implantação for Internet pública):

	- Personalizado: a porta pública é usada pelo servidor de processo para enviar dados de replicação pela Internet. A porta privada 9443 é usada pelo servidor de processo para enviar dados de replicação ao servidor de destino mestre pela VPN.
	- Personalizado1: a porta pública é usada pelo servidor de processo para enviar metadados de controle pela Internet. A porta privada 9080 é usada pelo servidor de processo para enviar metadados de controle ao servidor de destino mestre pela VPN.
	- PowerShell: porta privada 5986
	- Área de trabalho remota: porta privada 3389

4. Uma máquina virtual do servidor de destino mestre do Linux é criada com esses pontos de extremidade (pontos de extremidade públicos são criados somente se o tipo de implantação for Internet pública):

	- Personalizado: a porta pública é usada pelo servidor de processo para enviar dados de replicação pela Internet. A porta privada 9443 é usada pelo servidor de processo para enviar dados de replicação ao servidor de destino mestre pela VPN.
	- Personalizado1: a porta pública é usada pelo servidor de processo para enviar metadados de controle pela Internet. A porta privada 9080 é usada pelo servidor de processo para enviar dados de controle ao servidor de destino mestre pela VPN
	- SSH: porta privada 22

    >[AZURE.WARNING] Não exclua nem altere o número da porta pública ou privada de qualquer um dos pontos de extremidade criados durante a implantação do servidor de destino mestre.

5. Em **Máquinas Virtuais**, aguarde até que a máquina virtual seja iniciada.

	- Se você tiver configurado o servidor com o Windows, anote os detalhes da área de trabalho remota.
	- Se você tiver configurado com o Linux e estiver se conectando por meio da VPN, anote o endereço IP interno da máquina virtual. Se você estiver se conectando pela Internet, anote o endereço IP público.

6.  Faça logon no servidor para concluir a instalação e registrá-lo no servidor de configuração.
7.  Se você estiver executando o Windows:

	1. Inicie uma conexão da área de trabalho remota para a máquina virtual. Na primeira vez que você fizer logon, um script será executado em uma janela do PowerShell. Não o feche. Quando ele for concluído, a ferramenta de Configuração do Agente de Host será aberta automaticamente para registrar o servidor.
	2. Em **Configuração do Agente de Host**, especifique o endereço IP interno do servidor de configuração e a porta 443. Você pode usar o endereço interno e a porta privada 443, mesmo que não esteja se conectando por meio da VPN, porque a máquina virtual está conectada à mesma rede do Azure que o servidor de configuração. Deixe a opção **Usar HTTPS** habilitada. Insira a senha do servidor de configuração que você anotou anteriormente. Clique em **OK** para registrar o servidor. Observe que você pode ignorar as opções de NAT na página. Elas não são usadas.
	3. Se a unidade de retenção estimada precisar de mais de 1 TB, você poderá configurar o volume de retenção (R:) usando um disco virtual e [espaços de armazenamento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
	
	![Servidor de destino mestre Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Se você estiver executando o Linux:
	1. Certifique-se de ter os LIS (Linux Integration Services) mais recentes instalados antes de instalar o Software do servidor de destino mestre. Você pode encontrar a versão mais recente dos LIS com instruções sobre como instalá-los [aqui](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie a máquina após instalar os LIS.
	2. Em **Preparar Recursos de Destino do Azure**, clique em **Baixar e Instalar software adicional (somente para o Servidor de Destino Mestre Linux)** para baixar o pacote do servidor de destino mestre do Linux. Copie o arquivo tar baixado na máquina virtual usando um cliente sftp. Como alternativa, você pode fazer logon no servidor de destino mestre Linux implantado e usar *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para baixar o arquivo.
2. Faça logon no servidor usando um cliente do Secure Shell. Observe que, se você estiver conectado à rede do Azure pela VPN, use o endereço IP interno. Caso contrário, use o endereço IP externo e o ponto de extremidade público do SSH.
	3. Extraia os arquivos do instalador compactado executando **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64*** ![Servidor de destino mestre Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
	4. Verifique se você está no diretório no qual extraiu o conteúdo do arquivo tar.
	5. Copie a senha do servidor de configuração para um arquivo local usando o comando **echo *`<passphrase>`* >passphrase.txt**
	6. Execute o comando “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**”.

	![Registrar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Espere alguns minutos (de 10 a 15) e, na página **Servidores** > **Servidores de Configuração**, verifique se o servidor de destino mestre está listado como registrado na guia **Detalhes do Servidor**. Se você estiver executando o Linux e ele não tiver sido registrado, execute a ferramenta de configuração de host novamente em /usr/local/ASR/Vx/bin/hostconfigcli. Você precisará definir permissões de acesso executando chmod como raiz.

	![Verificar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Observe que podem ser necessários 15 minutos após a conclusão do registro para que o servidor de destino mestre seja listado no servidor de configuração. Para atualizar imediatamente, atualize o servidor de configuração clicando no botão Atualizar na parte inferior da página dos servidores de configuração.

## Etapa 4: implantar o servidor de processo local

>[AZURE.NOTE] É recomendável configurar um endereço IP estático no servidor de processo para garantir que ele seja persistente entre as reinicializações.

1. Clique em Início Rápido > **Instalar Servidor de Processo no local** > **Baixar e instalar o servidor de processo**.

	![Instalar servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copie o arquivo zip baixado para o servidor no qual você vai instalar o servidor em processo. O arquivo zip contém dois arquivos de instalação:

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. Descompacte o arquivo morto e copie os arquivos de instalação para um local no servidor.
4. Execute o arquivo de instalação **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** e siga as instruções. Isso instala os componentes de terceiros necessários para a implantação.
5. Em seguida, execute **Microsoft-ASR\_CX\_8.4.0.0\_Windows***.
6. Na página **Modo de Servidor**, selecione **Servidor de Processo**.
7. Na página **Detalhes do Ambiente**, siga este procedimento:


	- Se quiser proteger as máquinas virtuais VMware, clique em **Sim**
	- Se deseja apenas proteger servidores físicos e, portanto, não precisa do VMware vCLI instalado no servidor de processo, Clique em **Não** e continue.

8. Observe o seguinte ao instalar o VMware vCLI:

	- **Há suporte apenas para o VMware vSphere CLI 5.5.0**. O servidor de processo não funciona com outras versões ou atualizações do vSphere CLI.
	- Baixe o vSphere CLI 5.5.0 [aqui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
	- Se você instalou o vSphere CLI pouco antes de começar a instalar o servidor de processo e definiu para não detectá-lo, aguarde até cinco minutos antes de tentar a instalação novamente. Isso garante que todas as variáveis de ambiente necessárias para detecção do vSphere CLI sejam inicializadas corretamente.

9.	Em **Seleção de NIC para Servidor de Processo**, selecione o adaptador de rede que o servidor de processo deve usar.

	![Selecionar adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10.	Em **Detalhes do Servidor de Configuração**:

	- Para o endereço IP e a porta, se você estiver se conectando por VPN, especifique o endereço IP interno do servidor de configuração e 443 para a porta. Caso contrário, especifique o endereço IP virtual público e o ponto de extremidade HTTP público mapeado.
	- Digite a senha do servidor de configuração.
	- Desmarque **Verificar assinatura de software do Serviço de Mobilidade** se quiser desabilitar a verificação ao usar o envio por push automático para instalar o serviço. A verificação de assinatura precisa de conectividade com a Internet do servidor em processo.
	- Clique em **Próximo**.

	![Registrar servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Em **Selecionar Unidade de Instalação**, selecione uma unidade de cache. O servidor de processo precisa de uma unidade de cache com pelo menos 600 GB de espaço livre. Em seguida, clique em **Instalar**.

	![Registrar servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Observe que talvez você precise reiniciar o servidor para concluir a instalação. Em **Servidor de Configuração** > **Detalhes do Servidor**, verifique se o servidor de processo aparece e foi registrado com êxito no cofre.

>[AZURE.NOTE]Podem ser necessários até 15 minutos após a conclusão do registro para que o servidor de processo apareça como listado no servidor de configuração. Para atualizar imediatamente, atualize o servidor de configuração clicando no botão Atualizar na parte inferior da página do servidor de configuração
 
![Validar servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Se você não desabilitou a verificação de assinatura para o Serviço de Mobilidade quando registrou o servidor de processo, será possível fazê-lo posteriormente, como se segue:

1. Faça logon no servidor em processo como administrador e abra o arquivo C:\\pushinstallsvc\\pushinstaller.conf para edição. Na seção **[PushInstaller.transport]** adicione esta linha: **SignatureVerificationChecks=”0”**. Salve e feche o arquivo.
2. Reinicie o serviço InMage PushInstall.


## Etapa 5: instalar últimas atualizações

Antes de continuar, verifique se você tem as últimas atualizações instaladas. Lembre-se de instalar as atualizações na seguinte ordem:

1. Servidor de configuração
2. Servidor de processo
3. Servidor de destino mestre
4. Ferramenta de failback (vContinuum)

Você pode obter as atualizações no **Painel** do Site Recovery. Para instalação do Linux, extraia os arquivos do instalador compactado e execute o comando "sudo ./install" para instalar a atualização

Para baixar a atualização mais recente da **ferramenta de failback (vContinuum)**, clique [aqui](http://go.microsoft.com/fwlink/?LinkID=533813)

Se estiver executando máquinas virtuais ou servidores físicos que já tenham o Serviço de Mobilidade instalado, você poderá obter atualizações para o serviço da seguinte maneira:

- Baixe atualizações para o serviço seguindo estes links:
	- [Windows Server (somente 64 bits)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
	- [CentOS 6.4,6.5,6.6 (somente 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
	- [Oracle Enterprise Linux 6.4,6.5 (somente 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
	- [SUSE Linux Enterprise Server SP3 (somente 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- Se preferir, depois de atualizar o servidor de processo, você pode obter a versão atualizada do Serviço de Mobilidade na pasta C:\\pushinstallsvc\\repository no servidor de processo.
- Se tiver um aparelho já protegido com uma versão mais antiga do Serviço Mobilidade instalada, você também pode atualizar automaticamente o Serviço de Mobilidade nos computadores protegidos no portal de gerenciamento. Para fazer isso, selecione o grupo de proteção ao qual o computador pertence, realce o computador protegido e clique no botão Atualizar Serviço de Mobilidade na parte inferior. O botão Atualizar Serviço de Mobilidade será ativado somente se houver uma versão mais recente do Serviço de Mobilidade. Certifique-se de que o Servidor de processo está executando a versão mais recente do software do Servidor de processo antes de atualizar o serviço de mobilidade. O servidor protegido precisa atender a todos os [pré-requisitos de instalação por push automático](#install-the-mobility-service-automatically) para que o serviço de mobilidade de atualização funcione.

![Selecionar Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Ao selecionar as contas, especifique a conta de administrador a ser usada para atualizar o serviço de mobilidade no servidor protegido. Clique em OK e aguarde a conclusão do trabalho acionado.


## Etapa 6: adicionar Servidores vCenter ou hosts ESXi

1. Na guia **Servidores** > **Servidores de Configuração**, selecione o servidor de configuração e clique em **ADICIONAR SERVIDOR VCENTER** para adicionar um servidor vCenter ou um host ESXi.

	![Selecionar Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Especifique os detalhes do Servidor vCenter ou host ESXi e selecione o servidor de processo que será usado para descobri-lo.

	- Se o Servidor vCenter não estiver em execução na porta 443 padrão, especifique o número da porta em que o Servidor vCenter está em execução.
	- O servidor de processo deve estar na mesma rede que o Servidor vCenter/host ESXi e deve ter o VMware vSphere CLI 5.5.0 instalado.

	![Configurações do Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Após a conclusão da descoberta, o Servidor vCenter será listado nos detalhes do servidor de configuração.

	![Configurações do Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Se estiver usando uma conta de não administrador para adicionar o Servidor vCenter ou host ESXi, verifique se a conta tem os seguintes privilégios:

	- Contas do vCenter devem ter os privilégios Datacenter, Repositório de dados, Pasta, Host, Rede, Recurso, exibições de Armazenamento, Máquina virtual e vSphere Distributed Switch habilitados.
	- Contas do host ESXi devem ter os privilégios Datacenter, Repositório de dados, Pasta, Host, Rede, Recurso, Máquina virtual e vSphere Distributed Switch habilitados.



## Etapa 7: criar um grupo de proteção

1. Abra **Itens Protegidos** > **Grupo de Proteção** e clique para adicionar um grupo de proteção.

	![Criar grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Na página **Especificar Configurações do Grupo de Proteção**, especifique um nome para o grupo e selecione o servidor de configuração no qual deseja criar o grupo.

	![Configurações do grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Na página **Especificar Configurações de Replicação**, defina as configurações de replicação que serão usadas para todos os computadores do grupo.

	![Replicação do grupo de proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Configurações:
	- **Consistência de várias VMs**: se você ativar essa configuração, ela cria pontos de recuperação compartilhados consistentes com aplicativos nos computadores do grupo de proteção. Essa configuração é mais relevante quando todos os computadores no grupo de proteção estão executando a mesma carga de trabalho. Todos os computadores serão recuperados para o mesmo ponto de dados. Disponível somente para servidores do Windows.
	- **Limite de RPO**: alertas são gerados quando o RPO de replicação de proteção de dados contínua excede o valor do limite de RPO configurado.
	- **Retenção do ponto de recuperação**: especifica a janela de retenção. Computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
	- **Frequência do instantâneo consistente com aplicativo**: especifica com que frequência são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos.

Você pode monitorar o grupo de proteção à medida que eles são criados na página **Itens Protegidos**.

## Etapa 8: configurar computadores que deseja proteger

Você precisará instalar o Serviço de Mobilidade em máquinas virtuais e servidores físicos que deseja proteger. É possível fazer isso de duas formas:

- Do servidor de processo, enviar por push e instalar automaticamente o serviço em cada computador.
- Instalar o serviço manualmente. 

### Instalar o Serviço de Mobilidade automaticamente

Quando você adiciona computadores a um grupo de proteção, o Serviço de mobilidade é enviado automaticamente e instalado em cada computador pelo servidor em processo.

**Enviar por push e instalar automaticamente o Serviço de Mobilidade em servidores Windows:**

1. Instale as atualizações mais recentes do servidor de processo como descrito na [Etapa 5: Instalar as atualizações mais recentes](#step-5-install-latest-updates) e verifique se o servidor de processo está disponível. 
2. Verifique se há conectividade de rede entre o computador de origem e o servidor de processo, e se o computador de origem pode ser acessado do servidor de processo.  
3. Configure o Firewall do Windows para permitir **Compartilhamento de Arquivo e Impressora** e **Instrumentação de Gerenciamento do Windows**. Em configurações do Firewall do Windows, selecione a opção "Permitir um aplicativo ou recurso pelo Firewall" e selecione os aplicativos, como mostrado na figura abaixo. Para computadores que pertencem a um domínio, você pode configurar a política de firewall com um GPO.

	![Configurações de firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. A conta usada para executar a instalação por push deve estar do grupo Administradores no computador que você deseja proteger. Essas credenciais são usadas apenas para instalação por push do Serviço de Mobilidade e você as fornecerá quando adicionar um computador a um grupo de proteção.
5. Se a conta fornecida não for uma conta de domínio, você precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso, adicione a entrada de registro LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Para adicionar a entrada de registro de uma CLI, abra o cmd ou o powershell e insira **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Enviar por push e instalar automaticamente o Serviço de Mobilidade em servidores Linux:**

1. Instale as atualizações mais recentes do servidor de processo como descrito na [Etapa 5: Instalar as atualizações mais recentes](#step-5-install-latest-updates) e verifique se o servidor de processo está disponível.
2. Verifique se há conectividade de rede entre o computador de origem e o servidor de processo, e se o computador de origem pode ser acessado do servidor de processo.  
3. Verifique se a conta é um usuário raiz no servidor Linux de origem.
4. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todas as NICs.
5. Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja proteger.
6. Verifique se SSH está habilitado e em execução na porta 22. 
7. Habilite a autenticação de subsistema e senha SFTP no arquivo sshd\_config, como se segue: 

	- a) Faça logon como raiz.
	- b) No arquivo /etc/ssh/sshd\_config, localize a linha que começa com **PasswordAuthentication**.
	- c) Remova a marca de comentário da linha e altere o valor de "no" para "yes".

		![Mobilidade do Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

	- d) Localize a linha que começa com "Subsystem" e remova a marca de comentário da linha.
	
		![Mobilidade por push do Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)

8. Verifique se há suporte para a variante Linux do computador de origem.
 
### Instalar o Serviço de Mobilidade manualmente

Os pacotes de software usados para instalar o Serviço de Mobilidade estão no servidor de processo em C:\\pushinstallsvc\\repository. Faça logon no servidor de processo e copie o pacote de instalação apropriado no computador de origem com base na tabela abaixo:-

| Sistema operacional de origem | Pacote do Serviço de Mobilidade no servidor de processo |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server (somente 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (somente 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Para instalar o serviço de Mobilidade manualmente em um servidor Windows**, siga este procedimento:

1. Copie o pacote **Microsoft-ASR\_UA\_8.4.0.0\_Windows\_GA\_28Jul2015\_release.exe**, do caminho de diretório do servidor de processo descrito na tabela acima, no computador de origem.
2. Instale o Serviço de Mobilidade executando o executável no computador de origem.
3. Siga as instruções do instalador.
4. Selecione **Serviço de Mobilidade** como a função e clique em **Avançar**.
	
	![Instalar serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Deixe o diretório de instalação como o caminho de instalação padrão e clique em **Instalar**.
6. Em **Configuração do Agente de Host**, especifique o endereço IP e a porta HTTPS do servidor de configuração.

	- Se você estiver se conectando pela Internet, especifique o endereço IP virtual público e o ponto de extremidade HTTPS público como a porta.
	- Se você estiver se conectando pela VPN, especifique o endereço IP interno e 443 para a porta. Deixe marcada a opção **Usar HTTPS**.

	![Instalar serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Especifique a senha do servidor de configuração e clique em **OK** para registrar o Serviço Mobilidade no servidor de configuração.

**Para executar da linha de comando:**

1. Copie a senha de CX no arquivo "C:\\connection.passphrase" no servidor e execute este comando. Em nosso exemplo, CX i 104.40.75.37, e a porta HTTPS é 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalar o Serviço de Mobilidade manualmente em um servidor Linux**:

1. Copie o arquivo tar apropriado, com base na tabela acima, do servidor de processo no computador de origem.
2. Abra um programa de shell e extraia o arquivo tar compactado em um caminho local executando `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Crie um arquivo passphrase.txt no diretório local no qual você extraiu o conteúdo do arquivo tar inserindo *`echo <passphrase> >passphrase.txt`* no shell.
4. Para instalar o Serviço de Mobilidade, insira *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP e a porta:

	- Caso esteja se conectando ao servidor de configuração pela Internet, especifique o endereço IP público virtual e o ponto de extremidade HTTPS público do servidor de configuração em `<IP address>` e em `<port>`.
	- Se você estiver se conectando por uma conexão VPN, especifique o endereço IP interno e 443.

**Para executar na linha de comando:**

1. Copie a senha do CX no arquivo "passphrase.txt" no servidor e execute este comando. Em nosso exemplo, CX i 104.40.75.37, e a porta HTTPS é 62519:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Para instalar no servidor de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Quando você adiciona computadores a um grupo de proteção que já está executando uma versão adequada do Serviço de Mobilidade, a instalação por push é ignorada.


## Etapa 9: habilitar proteção

Para habilitar a proteção, adicione máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, observe que:

- As máquinas virtuais são descobertas a cada 15 minutos, podendo demorar até 15 minutos para que elas apareçam no Azure Site Recovery após a descoberta.
- As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) também podem levar até 15 minutos para serem atualizadas no Site Recovery.
- Você pode verificar a hora da última descoberta no campo **ÚLTIMO CONTATO EM** do Servidor vCenter/host ESXi, na página **Servidores de Configuração**.
- Se tiver um grupo de proteção já criado e adicionar um Servidor vCenter ou host ESXi depois disso, levará 15 minutos para que o portal do Azure Site Recovery seja atualizado e para que as máquinas virtuais sejam listadas na caixa de diálogo **Adicionar computadores a um grupo de proteção**.
- Se quiser continuar imediatamente com a adição de computadores ao grupo de proteção sem precisar esperar pela descoberta agendada, destaque o servidor de configuração (não clique nele) e clique no botão **Atualizar**.
- Quando você adiciona máquinas virtuais ou computadores físicos a um grupo de proteção, o servidor de processo envia por push e instala automaticamente o Serviço de Mobilidade no servidor de origem, caso ele ainda não esteja instalado.
- Para que o mecanismo de envio automático funcione, verifique se você configurou seus computadores protegidos conforme descrito na etapa anterior.

Adicione computadores como se segue:

1. **Itens Protegidos** > **Grupo de Proteção** > guia **Computadores**. Clique em **ADICIONAR MÁQUINAS**. Como uma prática recomendada, os grupos de proteção devem espelhar suas cargas de trabalho para que você adicione computadores que executam um aplicativo específico ao mesmo grupo.
2. Em **Selecionar Máquinas Virtuais**, se estiver protegendo servidores físicos, vá para o assistente **Adicionar Máquinas Físicas** e forneça o endereço IP e um nome amigável. Em seguida, selecione a família do sistema operacional.

	![Adicionar Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Em **Selecionar Máquinas Virtuais**, se estiver protegendo máquinas virtuais VMware, selecione um Servidor vCenter que esteja gerenciando suas máquinas virtuais, ou o host ESXi no qual elas estão em execução, e selecione os computadores.

	![Adicionar Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)	
4. Em **Especificar Recursos de Destino**, selecione os servidores de destino mestre e o armazenamento a serem usados para replicação e selecione se as configurações devem ser usadas para todas as cargas de trabalho. Selecione [Conta de Armazenamento Premium](../storage/storage-premium-storage.md) quando configurar a proteção para cargas de trabalho que exigem alto desempenho de E/S consistente e baixa latência para hospedar as cargas de trabalho intensivas de E/S. Se você quiser usar uma conta de Armazenamento Premium para os discos de carga de trabalho, você precisa usar o Destino mestre da série DS. Mas você não pode usar discos de Armazenamento Premium com Destino mestre que não pertençam às séries DS.

	![Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. Em **Especificar Contas**, escolha a conta que deseja usar para instalar o Serviço de Mobilidade em computadores protegidos. As credenciais da conta são necessárias para instalação automática do Serviço de Mobilidade. Se não for possível selecionar uma conta, configure uma conforme descrito na Etapa 2. Observe que essa conta não pode ser acessada pelo Azure. Para o servidor do Windows, a conta deve ter privilégios de administrador no servidor de origem. Para Linux, a conta deve ser raiz.

	![Credenciais do Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Clique na marca de seleção para terminar de adicionar computadores ao grupo de proteção e iniciar a replicação inicial para cada computador. Você pode monitorar o status na página **Trabalhos**.

	![Adicionar Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. Além disso, você pode monitorar o status de proteção clicando em **Itens Protegidos** > nome do grupo de proteção > **Máquinas Virtuais**. Depois que a replicação inicial for concluída e os computadores estiverem sincronizando dados, eles mostrarão o status **Protegido**.

	![Trabalhos da máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### Definir propriedades de computador protegido

1. Quando um computador tem o status **Protegido**, você pode configurar as respectivas propriedades de failover. Nos detalhes do grupo de proteção, escolha o computador e abra a guia **Configurar**.
2. É possível modificar o nome que será dado ao computador no Azure após o failover e o tamanho da máquina virtual do Azure. Você também pode selecionar a rede do Azure à qual o computador será conectado após o failover.

	![Definir propriedades da máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Observe que:

- O nome do computador Azure deve estar em conformidade com os requisitos do Azure.
- Por padrão, as máquinas virtuais replicadas no Azure não são conectadas a uma rede do Azure. Se você quiser que as máquinas virtuais replicadas se comuniquem, defina a mesma rede do Azure para elas.
- Se você redimensionar um volume em uma máquina virtual VMware ou um servidor físico, ele entrará em estado crítico. Se precisar modificar o tamanho, siga este procedimento:

	- a) Altere a configuração de tamanho.
	- b) Na guia **Máquinas Virtuais**, escolha a máquina virtual e clique em **Remover**.
	- c) Em **Remover Máquina Virtual**, selecione a opção **Desativar proteção (Use para a análise da recuperação e o redimensionamento do volume)**. Essa opção desabilita a proteção, mas retém os pontos de recuperação no Azure.

		![Definir propriedades da máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

	- d) Reabilite a proteção para a máquina virtual. Quando você reabilitar a proteção, os dados do volume redimensionado serão transferidos para o Azure.

	

## Etapa 10: executar um failover

No momento, você pode executar somente failovers não planejados para as máquinas virtuais VMware e os servidores físicos protegidos. Observe o seguinte:



- Antes de iniciar um failover, assegure-se de que a configuração e os servidores de destino mestre estejam em execução e íntegros. Caso contrário, o failover falhará.
- As máquinas de origem não são desligadas como parte de um failover não planejado. Executar um failover não planejado interrompe a replicação de dados para servidores protegidos. Você precisará excluir os computadores do grupo de proteção e adicioná-los novamente para iniciar a proteção de computadores novamente após a conclusão do failover não planejado.
- Se deseja fazer failover sem perder dados, verifique se as máquinas virtuais do site primário foram desativadas antes de iniciar o failover.

1. Na página **Planos de Recuperação**, adicione um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como destino.

	![Configurar plano de recuperação](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Em **Selecionar Máquina Virtual**, selecione um grupo de proteção, bem como os computadores do grupo a serem adicionados ao plano de recuperação. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Se necessário, você pode personalizar o plano para criar grupos e sequenciar a ordem em que os computadores no plano de recuperação serão submetidos a failover. Você também pode adicionar prompts para ações manuais e scripts. Durante a recuperação para o Azure, os scripts podem ser adicionados usando [Runbooks de Automação do Azure](site-recovery-runbook-automation.md).

4. Na página **Planos de Recuperação**, escolha o plano e clique em **Failover Não Planejado**.
5. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação para o failover.
6. Aguarde até que o trabalho de failover seja concluído e, em seguida, verifique se o failover funcionou conforme o esperado e se as máquinas virtuais replicadas são iniciadas com êxito no Azure.




## Etapa 11: fazer failback de computadores do Azure

[Saiba mais](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre como fazer failover de computadores em execução no Azure de volta no ambiente local.


## Gerenciar servidores de processo

O servidor de processo envia dados de replicação para o servidor de destino mestre no Azure, além de descobrir novas máquinas virtuais VMware adicionadas a um Servidor vCenter. Nas circunstâncias a seguir, talvez você queira alterar o servidor de processo na sua implantação:

- Se o servidor de processo atual ficar inativo
- Se o seu RPO (objetivo de ponto de recuperação) subir para um nível inaceitável para sua organização.

Se necessário, é possível mover a replicação de alguns ou de todos os servidores físicos e máquinas virtuais VMware locais para outro servidor de processo. Por exemplo:

- **Falha**: se um servidor de processo falhar ou não estiver disponível, você poderá mover a replicação do computador protegido para outro servidor de processo. Os metadados do computador de origem e do computador de réplica serão movidos para o novo servidor de processo e os dados serão sincronizados novamente. O novo servidor de processo se conectará automaticamente ao Servidor vCenter para executar a descoberta automática. É possível monitorar o estado dos servidores de processo no painel do Site Recovery.
- **Balanceamento de carga para ajustar o RPO**: para melhorar o balanceamento de carga, você pode selecionar outro servidor de processo no portal da Recuperação de Site e mover a replicação de um ou mais computadores se ele for para balanceamento de carga manual. Nesse caso, os metadados dos computadores de réplica e origem selecionados serão movidos para o novo servidor de processo. O servidor de processo original permanece conectado ao Servidor vCenter. 

### Monitorar o servidor de processo

Se um servidor de processo estiver em um estado crítico, um aviso será exibido no Painel do Site Recovery. Você pode clicar no status para abrir a guia Eventos e analisar detalhadamente trabalhos específicos na guia Trabalhos.

### Modificar o servidor de processo usado para replicação

1. Vá para a página **SERVIDORES DE CONFIGURAÇÃO** em **SERVIDORES**
2. Clique no nome do Servidor de Configuração e vá para **Detalhes do Servidor**.
3. Na lista **Servidores de Processo**, clique em **Alterar Servidor de Processo** ao lado do servidor que deseja modificar. ![Alterar o Servidor de processo 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
4. Na caixa de diálogo **Alterar Servidor de Processo**, selecione o novo servidor em **Servidor de Processo de Destino** e selecione as máquinas virtuais que deseja replicar no novo servidor. Clique no ícone de informações próximo ao nome do servidor para obter informações sobre ele, incluindo espaço livre e memória usada. O espaço médio que será exigido para replicar cada máquina virtual selecionada no novo servidor de processo será exibido para lhe ajudar a tomar decisões sobre a carga. ![Alterar o Servidor de processo 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
5. Clique na marca de seleção para começar a replicar no novo servidor de processo. Se você remover todas as máquinas virtuais de um servidor de processo que era essencial, um aviso crítico não deverá mais ser exibido no painel.


## Avisos e informações de softwares de terceiros

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0302_2016-->