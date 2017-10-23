---
title: Replicar as VMs do Hyper-V no VMM com SAN usando o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como replicar máquinas virtuais Hyper-V entre dois sites com o Azure Site Recovery usando a replicação SAN."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 3df38802fcdc86e4553253d38c49faff455f873e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>Replicar as VMs do Hyper-V nas nuvens do VMM em um site secundário com o Azure Site Recovery usando a SAN


Use este artigo se quiser implantar o [Azure Site Recovery](site-recovery-overview.md) para gerenciar a replicação das VMs do Hyper-V (gerenciadas nas nuvens do VMM do System Center) em um site secundário do VMM, usando o Azure Site Recovery no portal clássico. Este cenário não está disponível no novo portal do Azure.



Poste comentários no final deste artigo. Obtenha respostas para perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>Por que replicar com SAN e Site Recovery?

* A SAN fornece uma solução de replicação no nível corporativo e dimensionável para que um site primário, que contém o Hyper-V com a SAN, possa replicar as LUNs em um site secundário com a SAN. O armazenamento é gerenciado pelo VMM e a replicação e o failover são gerenciados com o Site Recovery.
* O Site Recovery trabalhou com vários [parceiros de armazenamento SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) para oferecer a replicação no Fibre Channel e no armazenamento iSCSI.  
* Use sua infraestrutura SAN existente para proteger os aplicativos críticos implantados nos clusters do Hyper-V. As VMs podem ser replicadas como um grupo para que os aplicativos com N camadas possam sofrer failover consistentemente.
* A replicação SAN garante a consistência da replicação nas diferentes camadas de um aplicativo com a replicação síncrona para o RTO e RPO baixos, e a replicação assíncrona para a alta flexibilidade (dependendo das capacidades da matriz de armazenamento).  
* Você pode gerenciar o armazenamento SAN no fabric do VMM e usar o SMI-S no VMM para descobrir o armazenamento existente.  

Observe que:
- A replicação SAN de site a site não está disponível no portal do Azure. Ela só está disponível no portal clássico. Não é possível criar novos cofres no portal clássico. Os cofres existentes podem ser mantidos.
- Não há suporte para replicação SAN no Azure.
- Não é possível replicar os VHDXs compartilhados ou as unidades lógicas (LUNs) conectadas diretamente às VMs via iSCSI ou Fibre Channel. Os clusters de convidados podem ser replicados.


## <a name="architecture"></a>Arquitetura

![Arquitetura de SAN](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: configure um cofre do Site Recovery no portal do Azure.
- **Armazenamento SAN**: o armazenamento SAN é gerenciado na malha do VMM. Adicione o provedor de armazenamento, crie classificações de armazenamento e configure pools de armazenamento.
- **VMM e Hyper-V**: recomendamos um servidor VMM em cada site. Configure as nuvens privadas do VMM e coloque os clusters Hyper-V nessas nuvens. Durante a implantação, o Provedor do Azure Site Recovery é instalado em cada servidor do VMM e o servidor é registrado no cofre. O Provedor se comunica com o serviço Site Recovery para gerenciar a replicação, o failover e o failback.
- **Replicação**: depois de configurar o armazenamento no VMM e configurar a replicação no Site Recovery, a replicação ocorre entre os armazenamentos SAN primário e secundário. Nenhum dado de replicação é enviado ao Site Recovery.
- **Failover**: habilite o failover no portal do Site Recovery. Não há perda de dados durante o failover porque a replicação é síncrona.
- **Failback**: para realizar o failback, habilite a replicação inversa para transferir as alterações delta do site secundário para o site primário. Após a conclusão da replicação inversa, execute um failover planejado do secundário para o primário. Esse failover planejado interrompe as VMs de réplica no site secundário e inicializa-as no site primário.


## <a name="before-you-start"></a>Antes de começar


**Pré-requisitos** | **Detalhes**
--- | ---
**As tabelas** |Você precisa de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. Crie um cofre do Azure Site Recovery para configurar e gerenciar a replicação e o failover.
**VMM** | Você pode usar um único servidor VMM e replicar entre nuvens diferentes, mas recomendamos ter um VMM no site primário e outro no site secundário. Um VMM pode ser implantado como um servidor físico ou virtual autônomo, ou como um cluster. <br/><br/>O servidor VMM deve estar executando o System Center 2012 R2 ou posterior com as atualizações cumulativas mais recentes.<br/><br/> Você precisa de pelo menos uma nuvem configurada no servidor VMM primário que deseja proteger e uma nuvem configurada no servidor do VMM secundário que deseja usar para failover.<br/><br/> A nuvem de origem precisa conter um ou mais grupos de hosts do VMM.<br/><br/> Todas as nuvens VMM devem ter o perfil de Capacidade do Hyper-V definido.<br/><br/> Para obter mais informações sobre como configurar as nuvens do VMM, consulte [Implantar uma nuvem VM privada](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Você precisa de um ou mais clusters Hyper-V nas nuvens do VMM primárias e secundárias.<br/><br/> O cluster do Hyper-V de origem deve conter uma ou mais VMs.<br/><br/> Os grupos de hosts do VMM nos sites primário e secundário devem conter pelo menos um dos clusters Hyper-V.<br/><br/>Os servidores Hyper-V de host e destino devem estar executando o Windows Server 2012 ou posterior com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Se você estiver executando o Hyper-V em um cluster e tiver um cluster baseado em endereços IP estáticos, o agente do cluster não será criado automaticamente. Você deve configurá-lo manualmente. Para obter mais informações, consulte [Preparando os clusters de hosts para a réplica do Hyper-V](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters).
**Armazenamento SAN** | Você pode replicar as máquinas virtuais em cluster convidadas com o armazenamento iSCSI ou de canal, ou usando discos rígidos virtuais compartilhados (vhdx).<br/><br/> Você precisa de duas matrizes de SAN: uma no site primário e outra no secundário.<br/><br/> Uma infraestrutura de rede deve ser configurada entre as matrizes. Devem ser configurados o emparelhamento e a replicação. Licenças de replicação devem ser configuradas de acordo com os requisitos de matriz de armazenamento.<br/><br/>Configure uma rede entre os servidores de host Hyper-V e a matriz de armazenamento para que os hosts possam comunicar-se com as LUNs de armazenamento usando o iSCSI ou o Fibre Channel.<br/><br/> Verificar [matrizes de armazenamento com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> Os provedores SMI-S, dos fabricantes da matriz de armazenamento, devem ser instalados e as matrizes de SAN devem ser gerenciadas pelo provedor. Configure o Provedor de acordo com as instruções do fabricante.<br/><br/>Verifique se o provedor SMI-S da matriz está em um servidor que o servidor VMM pode acessar na rede com um endereço IP ou FQDN.<br/><br/> Cada matriz SAN deve ter um ou mais pools de armazenamento disponíveis.<br/><br/> O servidor VMM primário deve gerenciar a matriz primária e o servidor VMM secundário, a matriz secundária.
**Mapeamento de rede** | Configure o mapeamento de rede para que as máquinas virtuais replicadas sejam colocadas da melhor forma possível nos servidores de host Hyper-V secundários após o failover e que sejam conectadas às redes de VM apropriadas. Se você não configurar a réplica de mapeamento de rede, as VMs não serão conectadas a nenhuma rede após o failover.<br/><br/> Verifique se as redes de VMM estão configuradas corretamente para poder configurar o mapeamento da rede durante a implantação do Site Recovery. No VMM, as VMs no host Hyper-V de origem devem ser conectadas a uma rede de VMs do VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.<br/><br/> A nuvem de destino deverá ter uma rede VM correspondente, que, por sua vez, deverá estar vinculada a uma rede lógica correspondente associada à nuvem de destino.<br/><br/>.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Etapa 1: preparar a infraestrutura do VMM
Para preparar a infraestrutura do VMM, é necessário:

1. verificar as nuvens do VMM;
2. integrar e classificar o armazenamento de SAN no VMM.
3. criar LUNs e alocar armazenamento;
4. criar grupos de replicação;
5. configurar as redes de VMs.

### <a name="verify-vmm-clouds"></a>Verificar nuvens do VMM

Verifique se suas nuvens do VMM estão configuradas corretamente antes de começar a implantação do Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Integre e classifique o armazenamento SAN no VMM

1. No console do VMM, vá para **Fabric** > **Armazenamento** > **Adicionar Recursos** > **Dispositivos de Armazenamento**.
2. No assistente para **Adicionar Dispositivos de Armazenamento**, escolha **Selecionar um tipo de provedor de armazenamento** e selecione **Dispositivos SAN e NAS descobertos e gerenciados por um provedor SMI-S**.

    ![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)

3. Na página **Especificar Protocolo e Endereço do Provedor SMI-S de Armazenamento**, selecione **SMI-S CIMXML** e especifique as configurações para conectar o provedor.
4. No **Endereço IP do provedor ou FQDN** e **porta TCP/IP**, especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

    ![Conexão com o provedor](./media/site-recovery-vmm-san/connect-settings.png)
5. Em **Executar como conta**, especifique uma conta Executar VMM Como que possa acessar o provedor ou crie uma conta.
6. Na página **Reunir Informações** , o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Para repetir a descoberta, clique em **Examinar Provedor**. Se o processo de descoberta for bem-sucedido, as matrizes de armazenamento, pools de armazenamento, fabricante, modelo e capacidade descobertos serão listados na página.

    ![Descoberta do armazenamento](./media/site-recovery-vmm-san/discover.png)
7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN são importadas dos pools de armazenamento. Crie LUNs com base nos aplicativos que você precisa proteger, seus requisitos de capacidade e requisitos para o que precisa replicar junto.

    ![Classificação do armazenamento](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Criar LUNs e alocar armazenamento

Crie LUNs com base nos aplicativos que você precisa proteger, requisitos de capacidade e requisitos para o que precisa replicar junto.

1. Depois que o armazenamento é exibido na malha do VMM, você pode [provisionar LUNs](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > Não adicione VHDs à VM que está habilitada para replicação nas LUNs. Se as LUNs não estiverem em um grupo de replicação do Site Recovery, elas não serão detectadas pelo Site Recovery.
     >

2. Aloque a capacidade de armazenamento para o cluster de hosts do Hyper-V para que o VMM possa implantar os dados da máquina virtual no armazenamento provisionado:

   * Antes de alocar o armazenamento para o cluster, você precisa alocá-lo para o grupo de hosts do VMM no qual o cluster reside. Para obter mais informações, consulte [Como alocar unidades lógicas de armazenamento para um grupo de hosts no VMM](https://technet.microsoft.com/library/gg610686.aspx) e [Como alocar pools de armazenamento para um grupo de hosts no VMM](https://technet.microsoft.com/library/gg610635.aspx).
   * Aloque a capacidade de armazenamento para o cluster como descrito em [Como configurar o armazenamento em um cluster de hosts do Hyper-V no VMM](https://technet.microsoft.com/library/gg610692.aspx).

    ![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)
3. Em **Especificar Protocolo e Endereço do Provedor de SMI-S de Armazenamento**, selecione **SMI-S CIMXML**. Especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para o CIMXML do SMI-S.

    ![Conexão com o provedor](./media/site-recovery-vmm-san/connect-settings.png)
4. Em **Executar como conta**, especifique uma conta Executar VMM Como que possa acessar o provedor ou crie uma conta.
5. Em **Reunir Informações** , o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Se você precisar repetir, clique em **Examinar Provedor**. Quando o processo de descoberta é bem-sucedido, as matrizes de armazenamento, os pools de armazenamento, o fabricante, o modelo e a capacidade são listados na página.

    ![Descoberta do armazenamento](./media/site-recovery-vmm-san/discover.png)
7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN são importadas dos pools de armazenamento.

    ![Classificação do armazenamento](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Criar grupos de replicação

Crie um grupo de replicação que inclui todas as LUNs que precisarão ser replicadas junto.

1. No console do VMM, abra a guia **Grupos de Replicação** das propriedades da matriz de armazenamento e clique em **Novo**.
2. Crie o grupo de replicação.

    ![Grupo de replicação de SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configurar redes

Se você quiser configurar o mapeamento de rede, faça o seguinte:

1. Consulte o mapeamento de rede do Site Recovery.
2. Prepare redes de VM no VMM:

   * [Configure redes lógicas](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Configure as redes de VMM](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Etapa 2: criar um cofre

1. Entre no [portal do Azure](https://portal.azure.com) a partir do servidor VMM que você deseja registrar no cofre.
2. Expanda os **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre do Site Recovery**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte os [Detalhes do Preço do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-san/create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de **Serviços de Recuperação**.

### <a name="register-the-vmm-servers"></a>Registrar os servidores VMM

1. Abra a página **Início Rápido** na página **Serviços de Recuperação**. O Início Rápido também pode ser aberto a qualquer momento escolhendo o ícone.

    ![Ícone de Inicialização Rápida](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Na caixa suspensa, selecione **Entre o site local do Hyper-V usando a replicação de matrizes**.

    ![Chave de registro](./media/site-recovery-vmm-san/select-san.png)
3. Em **Preparar servidores VMM**, baixe a versão mais recente do arquivo de instalação do Provedor do Azure Site Recovery.
4. Execute esse arquivo no servidor VMM de origem. Se o VMM for implantado em um cluster e você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós. Se estiver atualizando o Provedor, você precisará fazer a atualização em todos os nós para que tenham a mesma versão do Provedor.
5. O instalador verifica os requisitos e solicita permissão para interromper o serviço VMM para iniciar a instalação do Provedor. O serviço será reiniciado automaticamente quando a instalação for finalizada. Em um cluster do VMM, você será solicitado a parar a função Cluster.
6. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-san/ms-update.png)

7. Por padrão, o local de instalação do Provedor é <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Clique em **Instalar** para começar.

    ![Local de Instalação](./media/site-recovery-vmm-san/install-location.png)

8. Após a instalação do Provedor, clique em **Registrar** para registrar o servidor VMM no cofre.

    ![Instalação Concluída](./media/site-recovery-vmm-san/install-complete.png)

9. Em **Conexão de Internet**, especifique como o Provedor conecta a Internet. Selecione **Usar configurações de proxy padrão do sistema** se quiser usar as configurações de conexão com a Internet padrão no servidor.

    ![Configurações da Internet](./media/site-recovery-vmm-san/proxy-details.png)

   * Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o Provedor. Quando você definir as configurações personalizadas de proxy, um teste será executado para verificar a conexão proxy.
   * Se você usar um proxy personalizado ou se seu proxy padrão exigir autenticação, você deverá inserir os detalhes do proxy, incluindo a porta e o endereço.
   * As URLs devem poder ser acessadas do servidor VMM.
   * Se você usar um proxy personalizado, uma conta Executar VMM Como (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada. Você pode modificar as configurações da conta Executar Como no console do VMM (**Configurações** > **Segurança** > **Contas Executar Como** > **DRAProxyAccount**). Você precisa reiniciar o serviço do VMM para a alteração ter efeito.
10. Em **Chave de Registro**, selecione a chave que você baixou do portal e copiou para o servidor VMM.
11. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.

    ![Registros do servidor](./media/site-recovery-vmm-san/vault-creds.png)
12. A configuração de criptografia é usada apenas para o VMM na replicação do Azure. Você pode ignorá-la.

    ![Registros do servidor](./media/site-recovery-vmm-san/encrypt.png)
13. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
14. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.

    ![Registros do servidor](./media/site-recovery-vmm-san/friendly-name.png)

15. Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido em **Servidores** > **Servidores VMM** no cofre.

### <a name="command-line-installation"></a>Instalação da linha de comando

O Provedor do Azure Site Recovery também pode ser instalado usando a linha de comando a seguir. Esse método pode ser usado para instalar o provedor em um Server Core para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do provedor e a chave de registro em uma pasta. Por exemplo, C:\ASR.
2. Pare o serviço do VMM.
3. Extraia o instalador do Provedor. Execute estes comandos como administrador:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Instale o Provedor:

        C:\ASR> setupdr.exe /i
5. Registre o Provedor:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Parâmetros:

* **/Credentials**: parâmetro obrigatório para o local no qual o arquivo da chave de registro está localizado.  
* **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no portal do Azure Site Recovery.
* **/EncryptionEnabled**: parâmetro usado somente ao replicar do VMM para o Azure.
* **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
* **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
* **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exigir autenticação).
* **/proxyPassword** : parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exigir autenticação).

## <a name="step-3-map-storage-arrays-and-pools"></a>Etapa 3: Mapear as matrizes e pools de armazenamento

Mapeie as matrizes primária e secundária para especificar qual pool de armazenamento secundário recebe os dados de replicação do pool primário. Mapeie o armazenamento antes de configurar a replicação porque as informações de mapeamento são usadas quando você ativa a proteção para grupos de replicação.

Antes de começar, verifique se as nuvens VMM aparecem no cofre. As nuvens são detectadas quando você sincroniza todas as nuvens durante a instalação do Provedor ou quando sincroniza uma nuvem específica no console do VMM.

1. Clique em **Recursos** > **Armazenamento do Servidor** > **Mapear Matrizes de Origem e Destino**.
    ![Registro do servidor](./media/site-recovery-vmm-san/storage-map.png)

2. Selecione as matrizes de armazenamento no site primário e mapeie-as para armazenar as matrizes no site secundário. Em **Pools de Armazenamento**, selecione um pool de armazenamento de origem e de destino para mapear.

    ![Registros do servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Etapa 4: Definir configurações de replicação

Depois dos servidores VMM serem registrados, defina as configurações de proteção da nuvem.

1. Na página **Início Rápido**, clique em **Definir proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, selecione a **Configuração** da nuvem.
3. Em **Destino**, selecione **VMM**.
4. Em **Local de destino**, selecione o servidor VMM que gerencia a nuvem que você deseja usar para a recuperação.
5. Em **Nuvem de destino**, selecione a nuvem de destino que você deseja usar para o failover da VM.
   * É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para as máquinas virtuais que você protege.
   * Uma nuvem só pode pertencer a um único par de nuvens - como uma nuvem primária ou de destino.
6. O Site Recovery verifica se as nuvens têm acesso ao armazenamento SAN e se as matrizes de armazenamento foram mapeadas.
7. Se a verificação for bem-sucedida, em **Tipo de replicação**, selecione **SAN**.

Depois de salvar as configurações, um trabalho é criado e pode ser monitorado na guia **Trabalhos**. As configurações podem ser modificadas na guia **Configurar**. Se você quiser modificar o local de destino ou a nuvem de destino, deverá remover a configuração da nuvem, em seguida, reconfigurar a nuvem.

## <a name="step-5-enable-network-mapping"></a>Etapa 5: Habilitar o mapeamento de rede

1. Na página **Início Rápido**, clique em **Mapear redes**.
2. Selecione o servidor VMM de origem e o servidor VMM de destino para os quais as redes serão mapeadas. A lista de redes de origem e suas redes de destino associadas é exibida. Um valor vazio é mostrado para redes que não são mapeadas. Clique no ícone de informações ao lado dos nomes de rede de origem e de destino para exibir as sub-redes de cada rede.
3. Selecione uma rede em **Rede na origem** e clique em **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe.

    ![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Selecione uma das redes VM do servidor VMM de destino.

    ![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quando você seleciona uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. As redes de destino disponíveis também são exibidas. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens que você está usando para replicação.
6. Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho inicia e acompanha o andamento. Você pode exibi-lo na Guia **Trabalhos** .

## <a name="step-6-enable-replication-for-replication-groups"></a>Etapa 6: habilitar a replicação para grupos de replicação

Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento.

1. Na página **Propriedades** da nuvem primária no portal do Site Recovery, abra a guia **Máquinas Virtuais** e clique em **Adicionar Grupo de Replicação**.
2. Selecione um ou mais grupos de replicação do VMM que estão associados com a nuvem, verifique se os conjuntos de origem e de destino e especifique a frequência de replicação.

Os provedores do Site Recovery, VMM e SMI-S provisionam as LUNs de armazenamento do site de destino e habilitam a replicação do armazenamento. Se o grupo de replicação já foi replicado, o Site Recovery reutilizará a relação de replicação existente e atualizará as informações.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: habilitar a proteção para máquinas virtuais


Quando um grupo de armazenamento estiver replicando, habilite a proteção das VMs no console do VMM com um dos seguintes métodos:

* **Nova máquina virtual**: quando você criar uma nova VM, habilite a replicação e associe a VM ao grupo de replicação.
  Com essa opção, o VMM usa a colocação inteligente para otimizar o local do armazenamento de VM nas LUN do grupo de replicação. O Site Recovery orquestra a criação de uma VM de sombra no site secundário e aloca a capacidade para que as VMs de réplica possam ser iniciadas após o failover.
* **Máquina virtual existente**: se uma máquina virtual já estiver implantada no VMM, você poderá habilitar a replicação e executar uma migração de armazenamento para um grupo de replicação. Após a conclusão, o VMM e o Site Recovery detectam a nova VM e começam a gerenciá-la no Site Recovery. Uma VM sombra é criada no site secundário e a capacidade é alocada para que a VM de réplica possa ser iniciada após o failover.

![Habilitar proteção](./media/site-recovery-vmm-san/enable-protect.png)

Depois que as VMs são habilitadas para replicação, elas aparecem no console do Site Recovery. Você pode exibir as propriedades da VM, acompanhar o status e o failover dos grupos de replicação que contêm várias VMs. Na replicação de SAN, todas as VMs associadas a um grupo de replicação devem fazer failover juntas. Isso ocorre porque o failover ocorre na camada de armazenamento pela primeira vez. É importante agrupar seus grupos de replicação corretamente e reunir apenas as VMs associadas.

> [!NOTE]
> Depois de habilitar a replicação para uma VM, não adicione VHDs às LUNs, a menos que estejam localizadas em um grupo de replicação do Site Recovery. Os VHDs só serão detectados pelo Site Recovery se estiverem localizados em um grupo de replicação do Site Recovery.
>
>

Você pode acompanhar o andamento, inclusive a replicação inicial, na guia **Trabalhos**. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover.

![Trabalho de proteção da máquina virtual](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Etapa 8: testar a implantação

Teste a implantação para ter certeza de que as VMs realizam failover conforme o esperado. Para isso, crie um plano de recuperação e execute um teste de failover.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e selecione os servidores do VMM de origem e destino. O servidor de origem deve ter VMs que são habilitadas para failover e recuperação. Selecione **SAN** para exibir somente as nuvens que estão configuradas para replicação de SAN.

    ![Criar plano de recuperação](./media/site-recovery-vmm-san/r-plan.png)

3. Em **Selecionar Máquinas Virtuais**, selecione os grupos de replicação. Todas as VMs associadas ao grupo são adicionadas ao plano de recuperação. Essas VMs são adicionadas ao grupo padrão do plano de recuperação (Grupo 1). Você pode adicionar mais grupos, se necessário. Após a replicação, as VMs são numeradas de acordo com a ordem dos grupos do plano de recuperação.

    ![Selecionar máquinas virtuais](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Depois que um plano de recuperação é criado, ele aparece na lista da guia **Planos de Recuperação** . Selecione o plano e escolha **Failover de Teste**.
5. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Com essa opção habilitada, as VMs de réplica com failover não serão conectadas a nenhuma rede. Isso testa se as VMs fazem failover como o esperado, mas não testa o ambiente de rede. Para saber mais sobre outras opções de rede, consulte [Failover do Site Recovery](site-recovery-failover.md).

    ![Selecionar rede de teste](./media/site-recovery-vmm-san/test-fail1.png)

6. A VM de teste é criada no mesmo host no qual existe uma VM de réplica. Ele não será adicionado na nuvem em que a VM de réplica está localizada.
2. Após a replicação, a VM de réplica terá um endereço IP diferente da máquina virtual primária. Se você estiver emitindo endereços do DHCP, ele será atualizado automaticamente. Se você não estiver usando o DHCP e quiser os mesmos endereços, precisará executar alguns scripts.
3. Execute o script para recuperar o endereço IP:

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. Execute este script de exemplo para atualizar o DNS. Especifique o endereço IP recuperado.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>Próximas etapas

Depois de executar um failover de teste para verificar se seu ambiente está funcionando como o esperado, consulte [Failover do Site Recovery](site-recovery-failover.md) para conhecer os diferentes tipos de failover.
