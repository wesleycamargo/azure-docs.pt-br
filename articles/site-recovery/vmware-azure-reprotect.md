---
title: Proteger novamente VMs do Azure para um site local | Microsoft Docs
description: Após o failover de máquinas virtuais no Azure, você pode iniciar um failback para trazer as máquinas virtuais de volta para o local. Saiba como proteger novamente antes de um failback.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 499f363dd6241612553e94e43dd56de6cfc8f71f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Proteger novamente computadores do Azure para um site local

Após [failover](site-recovery-failover.md) de VMs VMware ou servidores físicos para Azure, a primeira etapa de failback para o site local é proteger novamente as VMs do Azure que foram criadas durante o failover. Este artigo descreve como fazer isso. 

Para obter uma visão geral, assista a este vídeo sobre como fazer failover do Azure para um site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Antes de começar

Se você usou um modelo para criar suas máquinas virtuais, verifique se cada máquina virtual tem seus próprios UUIDs para os discos. Se o UUID da máquina virtual local estiver em conflito com o do destino mestre, porque ambos foram criados com base no mesmo modelo, a nova proteção falhará. Implante outro destino mestre que não tenha sido criado com base no mesmo modelo.
- Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter e o servidor de destino mestre estão descobertos. Um sintoma típico é que os armazenamentos de dados não ficam acessíveis ou visíveis na caixa de diálogo **Proteger Novamente**.
- Para replicar de volta para local, você precisará de uma política de failback. Essa política é criada automaticamente quando você cria uma política de direção de encaminhamento. Observe que:
    - Essa política é associada automaticamente ao servidor de configuração durante a criação.
    - Essa política não é editável.
    - Os valores definidos da política são (Limite do RPO = 15 min, Retenção do Ponto de Recuperação = 24 horas, Frequência de Instantâneo de Consistência do Aplicativo = 60 min)
- Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e conectado.
- Se um vCenter Server gerenciar as máquinas virtuais para as quais fará failback, certifique-se de que você possui as [permissões necessárias](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) para descoberta de VMs nos servidores vCenter.
- Exclua instantâneos no servidor de destino mestre antes da nova proteção. Se houver instantâneos na máquina virtual ou no destino mestre local, a nova proteção falhará. Os instantâneos na máquina virtual são mesclados automaticamente durante um trabalho de nova proteção.
- Todas as máquinas virtuais de um grupo de replicação devem ser do mesmo tipo de sistema operacional (todas com Windows ou todas com Linux). Atualmente, um grupo de replicação com sistemas operacionais misturados não têm suporte para proteger novamente e para fazer o failback no local. Isso ocorre porque o destino mestre deve ser do mesmo sistema operacional da máquina virtual e todas as máquinas virtuais de um grupo de replicação devem ter o mesmo destino mestre. 
- Um servidor de configuração é necessário localmente ao fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido. Faça o backup agendado regular de seu servidor. de configuração. Em caso de desastre, restaure o servidor com o mesmo endereço IP para que o failback funcione.
- A nova proteção e o failback exigem uma VPN site a site para replicar dados. Forneça a rede de modo que as máquinas virtuais que passaram por failover no Azure possam alcançar (executar ping) o servidor de configuração local. Você também pode implantar um servidor de processo na rede do Azure da máquina virtual que passou por failover. Esse servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local.
- Certifique-se de abrir as portas a seguir para failover e failback.

    ![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)

## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

Talvez seja necessário um servidor de processo no Azure antes de failback para o site local:
- O servidor de processo] recebe dados da máquina virtual protegida no Azure e envia dados para o site local.
- Uma rede de baixa latência é necessária entre o servidor de processo e a máquina virtual protegida. Em geral, é necessário considerar a latência ao decidir se você precisa de um servidor de processo no Azure:
    - Se você tem uma conexão do ExpressRoute configurada, você pode usar um servidor de processo local para enviar dados porque a latência entre a máquina virtual e o servidor de processo é baixa.
    - No entanto, se você tem apenas uma VPN S2S, é recomendável implantar o servidor de processo no Azure.
    - É recomendável usar um servidor de processo com base no Azure durante o failback. O desempenho da replicação será maior se o servidor de processo estiver mais próximo da máquina virtual de replicação (a máquina no Azure que passou pelo failover). Para uma prova de conceito, é possível utilizar o servidor de processo local em conjunto com ExpressRoute com consulta privada.

Implante da seguinte maneira:

1. Se você precisa implantar um servidor de processo no Azure, siga [estas instruções](vmware-azure-set-up-process-server-azure.md)
2. As VMs do Azure enviarão dados de replicação para o servidor de processo. Configure redes, de modo que as VMs do Azure possam alcançar o servidor de processo.
3. Lembre-se de que a replicação do Azure para local pode ocorrer apenas por meio de VPN S2S, ou através do emparelhamento privado da rede do ExpressRoute. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

## <a name="deploy-a-separate-master-target-server"></a>Implantar um servidor de destino mestre separado

O servidor de destino mestre recebe dados de failback. Por padrão, o servidor de destino mestre é executado no servidor de configuração local. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback. Veja como criar um:

    * [Crie um servidor de destino mestre Linux](vmware-azure-install-linux-master-target.md) para failback de VMs do Linux. Isso é necessário.
    * Opcionalmente, crie um servidor de destino mestre separado para failback de VM do Windows. Para fazer isso, você executa novamente a Instalação Unificada e seleciona para criar um servidor de destino mestre. [Saiba mais](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Após criar um servidor de destino mestre, faça o seguinte:

- Se a máquina virtual está presente localmente no servidor vCenter, o servidor de destino mestre precisa acessar o VMDK da máquina virtual local. O acesso é necessário para gravar os dados replicados nos discos da máquina virtual. Verifique se o armazenamento de dados da máquina virtual local está montado no host de destino mestre com acesso de leitura/gravação.
- Se a máquina virtual não está presente localmente no servidor vCenter, o serviço do Site Recovery precisa criar uma nova máquina virtual durante a nova proteção. Essa máquina virtual é criada no host ESX em que você criou o destino mestre. Escolha o host ESX com cuidado para que a máquina virtual de failback seja criada no host desejado.
- Não é possível utilizar o Storage vMotion para o servidor de destino mestre*. Isso pode causar a falha do failback. A máquina virtual não pode ser iniciada porque os discos não estão disponíveis para ela. Para evitar esse problema, exclua os servidores de destino mestre de sua lista do vMotion.
- Se um destino mestre passar por uma tarefa Armazenamento de vMotion depois da nova proteção, os discos das máquinas virtuais protegidas que estiverem anexados ao destino mestre serão migrados para o destino da tarefa vMotion. Se você tentar fazer failback após isso, a ação de desanexar o disco resultará em falha porque os discos não foram encontrados. Assim, ficará difícil localizar os discos em suas contas de armazenamento. Você precisará localizá-los manualmente e anexá-los à máquina virtual. Depois disso, a máquina virtual local poderá ser reinicializada.
- Adicione uma unidade de retenção ao servidor de destino mestre existente do Windows. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a máquina virtual replica para o site local. A seguir estão alguns critérios de uma unidade de retenção. Sem esses critérios, a unidade não será listada para o servidor de destino mestre.
    - O volume não é usado para nenhuma outra finalidade, como um destino de replicação.
    - O volume não está no modo de bloqueio.
    - O volume não é um volume de cache. A instalação do destino mestre não deve existir nesse volume. O volume de instalação personalizada para o servidor de processo e o destino mestre não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.
    - O tipo de sistema de arquivos do volume não é FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção padrão para o Windows é o volume R.
    - O volume de retenção padrão para o Linux é /mnt/retention.
- Você precisará adicionar uma nova unidade se estiver usando um servidor de processo/computador de servidor de configuração existente ou uma escala ou um servidor de processo/computador de servidor de destino mestre. A nova unidade deve atender aos requisitos anteriores. Se a unidade de retenção não estiver presente, ela não aparecerá na lista suspensa de seleção no portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no portal. Você também poderá atualizar o servidor de configuração se a unidade não aparecer depois de 15 minutos.
- Instalar ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, os armazenamentos de dados no host de ESXi do destino mestre não podem ser detectados.
- Defina a configuração `disk.EnableUUID=true` nos parâmetros de configuração da máquina virtual de destino mestre no VMware. Se essa linha não existir, adicione-a. Essa configuração é necessária para fornecer um UUID consistente para o VMDK (disco de máquina virtual) para que ele monte corretamente.
- O host ESX em que o destino mestre é criado deve ter pelo menos um datastore VMFS anexado a ele. Se não houver nenhum, a entrada do **Repositório de Dados** na página de nova proteção estará vazia e você não poderá continuar.
- O servidor de destino mestre não pode ter instantâneos nos discos. Se houver instantâneos, a nova proteção e o failback falharão.
- O destino mestre não pode ter um controlador Paravirtual SCSI. O controlador só pode ser um controlador LSI Logic. Sem um controlador de Lógica LSI, a nova proteção falha.
- Em qualquer instância em particular, o destino mestre pode ter no máximo 60 discos conectados a ele. Se o número de máquinas virtuais que estiverem sendo reprotegidas para o destino mestre no local for maior do que a soma total de 60 discos, as novas proteções para o destino mestre começarão a falhar. Certifique-se de que você tenha slots de disco suficientes no destino mestre ou implante servidores de destino mestre adicionais.
    

## <a name="enable-reprotection"></a>Habilitar nova proteção

Depois que uma máquina virtual é reinicializada no Azure, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, não será possível proteger novamente e uma mensagem de erro indicará que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e tente proteger novamente.


1. Em **Vault** > **Itens replicados**, clique com o botão direito do mouse na máquina virtual em que foi executado o failover e, em seguida, selecione **Proteger Novamente**. Também é possível clicar no computador e selecionar **Proteger Novamente** nos botões de comando.
2. Verifique se a direção da proteção, **Azure para Local**, já está selecionada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para **Repositório de Dados**, selecione o repositório de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Clique em **OK** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** . Depois que nova proteção for bem-sucedida, a máquina virtual entrará em um estado protegido.

Observe que:
- Se você quiser recuperar para um local alternativo (quando a máquina virtual local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo repositório de dados que o servidor de destino mestre. Então, uma nova máquina virtual é criada no vCenter.
- Se você quiser recuperar a máquina virtual do Azure para uma máquina virtual local existente, monte os repositórios de dados da máquina virtual local com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.
    ![Proteger novamente a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)

- Também é possível proteger novamente no nível de um plano de recuperação. Um grupo de replicação pode ser protegido novamente somente por meio de um plano de recuperação. Ao proteger novamente usando um plano de recuperação, você precisará fornecer os valores para cada computador protegido.
- Use o mesmo servidor de destino mestre para proteger novamente um grupo de replicação. Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não poderá fornecer um ponto comum no tempo.
- A máquina virtual local é desativada durante a nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação. Não ative a máquina virtual após o fim da nova proteção.



## <a name="common-issues"></a>Problemas comuns


- Atualmente, o Azure Site Recovery fornece suporte para failback somente para um VMFS (sistema de arquivos de máquina virtual) ou armazenamento de dados vSAN. Não há suporte para um armazenamento de dados NFS. Devido a essa limitação, a entrada de seleção do repositório de dados na tela de nova proteção fica vazia no caso de repositórios de dados NFS ou exibe o repositório de dados vSAN, mas falha durante o trabalho. Se você pretende realizar failback, é possível criar um repositório de dados VMFS local e realizar failback nele. Este failback causará o download completo do VMDK.
- Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. Durante a nova proteção, o failover falha porque não é possível descobrir os armazenamentos de dados. Um sintoma é que os repositórios de dados não estão listados durante a nova proteção. Para resolver esse problema, você pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repetir o trabalho. 
- Ao executar failback de uma máquina virtual Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. A desinstalação ocorre porque o pacote do Gerenciador de Rede é removido quando a máquina virtual é recuperada no Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido do DHCP. Quando você executa o failover localmente, a máquina virtual continua a usar o DHCP para obter o endereço IP. Conecte-se manualmente à máquina e defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual Windows pode adquirir novamente seu endereço IP estático.
- Se você usa a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover tem êxito, mas não acontece. Para permitir o failback, atualize para a licença de avaliação dos programas.
- Se você não conseguir acessar o servidor de configuração do servidor de processo, use Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. O servidor de processo também deve ter uma pulsação quando for conectado ao servidor de configuração.
- Um servidor Windows Server 2008 R2 SP1 que é protegido, já que um servidor local físico não pode fazer failback do Azure para um site local.
- Não é possível fazer failback nas seguintes circunstâncias:
    - Você migrou computadores para o Azure. [Saiba mais](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM foi movida para outro grupo de recursos.
    - A VM do Azure foi excluída.
    - A proteção da VM foi desabilitada.
    - A VM foi criada manualmente no Azure. A máquina deve ter sido inicialmente protegida no local e ter tido o failover para o Azure antes da nova proteção.
    - Só é possível failback para um host ESXi. Não é possível failback de VMs VMware ou servidores físicos para hosts do Hyper-V, computadores físicos ou estações de trabalho VMware.


## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual entrou em um estado protegido, você poderá [iniciar um failback](vmware-azure-failback.md). O failback desligará a máquina virtual no Azure e inicializará a máquina virtual local. Espere algum tempo de inatividade do aplicativo. Escolha um horário para realizar failback quando o aplicativo puder tolerar o tempo de inatividade.

