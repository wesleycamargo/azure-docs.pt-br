---
title: Replicar VMs do Hyper-V no VMM com SAN usando o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como replicar máquinas virtuais Hyper-V entre dois sites com o Azure Site Recovery usando a replicação SAN."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 75653b84d6ccbefe7d5230449bea81f498e10a98
ms.openlocfilehash: 09fb09bfdea2e18384851bb8ed9a4f8c08466dd2


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-using-san"></a>Replicar VMs Hyper-V de nuvens VMM em um site secundário com o Azure Site Recovery usando SAN


Use este artigo se desejar implantar [Site Recovery](site-recovery-overview.md) para gerenciar a replicação de VMs Hyper-V (gerenciadas em nuvens do System Center VMM) em um site secundário do VMM.


Ele inclui uma visão geral do cenário, as instruções para configurar o armazenamento SAN no VMM e a configurando da replicação no portal do Site Recovery. Ele é concluído com o teste do failover para verificar se tudo está funcionando conforme o esperado.

Poste comentários abaixo. Obtenha respostas para perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>Por que replicar com SAN e Site Recovery?

* SAN fornece uma solução de replicação de nível corporativo e dimensionável para que um site primário que contém Hyper-V com SAN possa replicar LUNs em um site secundário com SAN. O armazenamento é gerenciado pelo VMM e a replicação e o failover são gerenciados com o Site Recovery.
* O Site Recovery trabalhou com vários [parceiros de armazenamento SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) para oferecer replicação entre armazenamentos iSCSI e fibre channel.  
* Você aproveita sua infraestrutura de SAN para proteger aplicativos de missão crítica implantados em clusters Hyper-V. As VMs podem ser replicadas como um grupo para que os aplicativos de N camadas possam sofrer failover consistentemente.
* A replicação SAN garante a consistência da replicação em camadas diferentes de um aplicativo, com replicação sincronizada para RTO e RPO baixo, e replicação assíncrona para alta flexibilidade (dependendo das capacidades da matriz de armazenamento).  
* Você pode gerenciar o armazenamento SAN na malha do VMM e usar o SMI-S no VMM para descobrir armazenamentos existentes.  

Observe que:
- A replicação SAN de site a site não está disponível no portal do Azure. Ela só está disponível no portal clássico. Não é possível criar novos cofres no portal clássico. Os cofres existentes podem ser mantidos.
- Não há suporte para replicação SAN no Azure.
- Você não pode replicar VHDXs compartilhados ou LUNs (unidades lógicas) que estejam conectados diretamente às VMs por meio de iSCSI ou fibre channel. Os clusters de convidados podem ser replicados.


## <a name="architecture"></a>Arquitetura

![Arquitetura de SAN](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: configure um cofre do Site Recovery no portal do Azure.
- **Armazenamento SAN**: o armazenamento SAN é gerenciado na malha do VMM. Adicione o provedor de armazenamento, crie classificações de armazenamento e configure pools de armazenamento.
- **VMM e Hyper-V**: recomendamos um servidor VMM em cada site. Configure nuvens privadas do VMM e coloque os clusters Hyper-V nessas nuvens. Durante a implantação, o Provedor do Azure Site Recovery é instalado em cada servidor do VMM e o servidor é registrado no cofre. O Provedor se comunica com o serviço Site Recovery para gerenciar a replicação, o failover e o failback.
- **Replicação**: depois de configurar o armazenamento no VMM e configurar a replicação no Site Recovery, a replicação ocorre entre os armazenamentos SAN primário e secundário. Nenhum dado de replicação é enviado ao Site Recovery.
- **Failover**: habilite o failover no portal do Site Recovery. Não há perda de dados durante o failover porque a replicação é síncrona.
- **Failback**: para realizar failback, habilite a replicação inversa para transferir as alterações delta do site secundário para o site primário. Após a conclusão da replicação inversa, execute um failover planejado do secundário para o primário. Esse failover planejado interrompe as VMs de réplica no site secundário e as inicializa no site primário.


## <a name="before-you-start"></a>Antes de começar


**Pré-requisitos** | **Detalhes**
--- | ---
**As tabelas** |Você precisa de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. Crie um cofre do Azure Site Recovery para configuração e para gerenciar replicação e failover.
**VMM** | Você pode usar um único servidor VMM e replicar entre nuvens diferentes, mas recomendamos ter um VMM no site primário e outro no site secundário. O VMM pode ser implantado como um servidor físico ou virtual autônomo ou como um cluster. <br/><br/>O servidor VMM deve estar executando pelo menos o System Center 2012 R2 com as últimas atualizações cumulativas<br/><br/> Você precisa de pelo menos uma nuvem configurada no servidor VMM primário que deseja proteger e uma nuvem configurada no servidor do VMM secundário que deseja usar para failover.<br/><br/> A nuvem de origem precisa conter um ou mais grupos de hosts do VMM.<br/><br/> Todas as nuvens VMM devem ter o perfil de Capacidade do Hyper-V definido.<br/><br/> Saiba mais sobre como configurar nuvens do VMM (https://technet.microsoft.com/pt-br/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Você precisa de um ou mais clusters Hyper-V nas nuvens do VMM primárias e secundárias.<br/><br/> O cluster do Hyper-V de origem deve conter uma ou mais VMs.<br/><br/> Os grupos de hosts do VMM nos sites primário e secundário devem conter pelo menos um dos clusters Hyper-V.<br/><br/>Os servidores Hyper-V host e de destino devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente caso tenha um cluster baseado em endereços IP estáticos. Você precisará configurá-lo manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) na entrada do blog de Aidan Finn.
**Armazenamento SAN** | Você pode replicar as máquinas virtuais de cluster convidado com o armazenamento iSCSI ou de canal usando discos rígidos virtuais compartilhados (vhdx).<br/><br/> Você precisa de duas matrizes de SAN, uma no site primário e uma no secundário.<br/><br/> Uma infraestrutura de rede deve ser configurada entre as matrizes. Devem ser configurados o emparelhamento e a replicação. Licenças de replicação devem ser configuradas de acordo com os requisitos de matriz de armazenamento.<br/><br/>Configure rede entre os servidores de host Hyper-V e a matriz de armazenamento para que os hosts possam se comunicar com as LUNs de armazenamento usando ISCSI ou Fibre Channel.<br/><br/> Verificar [matrizes de armazenamento com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> Provedores de SMI-S (de fabricantes de matriz de armazenamento) devem ser instalados e as matrizes de SAN devem ser gerenciadas pelo provedor. Configure o Provedor de acordo com as instruções do fabricante.<br/><br/>Verifique se o provedor de SMI-S da matriz está em um servidor que o servidor do VMM pode acessar na rede com um endereço IP ou FQDN.<br/><br/> Cada matriz SAN deve ter um ou mais pools de armazenamento disponíveis.<br/><br/> O servidor VMM primário deve gerenciar a matriz primária e o servidor VMM secundário, a matriz secundária.
**Mapeamento de rede** | Defina o mapeamento de rede para assegurar que as máquinas virtuais de réplica sejam colocadas da melhor forma possível em servidores host Hyper-V secundários após o failover e que possam se conectar às redes de VM apropriadas. Se você não configurar a réplica de mapeamento de rede, as VMs não serão conectadas a nenhuma rede após o failover.<br/><br/> Você precisa fazer com que as redes VMM estejam configuradas corretamente para poder configurar o mapeamento de rede durante a implantação do Site Recovery. No VMM, as VMs às quais o host Hyper-V de origem deve estar conectado em uma rede de VM do VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.<br/><br/> A nuvem de destino deverá ter uma rede VM correspondente, que, por sua vez, deverá estar vinculada a uma rede lógica correspondente associada à nuvem de destino.<br/><br/>[Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Etapa 1: preparar a infraestrutura do VMM
Para preparar a infraestrutura do VMM, é necessário:

1. Verificar nuvens do VMM
2. Integre e classifique o armazenamento de SAN no VMM
3. Criar LUNs e alocar armazenamento
4. Criar grupos de replicação
5. Configurar redes de VMM

### <a name="verify-vmm-clouds"></a>Verificar nuvens do VMM

Verifique se suas nuvens do VMM estão configuradas corretamente antes de começar a implantação do Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Integre e classifique o armazenamento SAN no VMM

1. No console do VMM, clique em **Fabric** > **Armazenamento** > **Adicionar Recursos** > **Dispositivos de Armazenamento**.
2. No Assistente de Adição de Dispositivos de Armazenamento > **Selecionar um tipo de provedor de armazenamento**, selecione **Dispositivos SAN e NAS descobertos e gerenciados por um provedor SMI-S**.


    ![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)
3. Na página **Especificar Protocolo e Endereço do Provedor SMI-S de Armazenamento** selecione **SMI-S CIMXML** e especifique as configurações para conectar-se ao provedor.
4. No **Endereço IP do provedor ou FQDN** e **porta TCP/IP**, especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

    ![Conexão com o provedor](./media/site-recovery-vmm-san/connect-settings.png)
5. Em **Executar como conta** especifique uma conta Executar como VMM que possa acessar o provedor ou crie uma nova conta.
6. Na página **Reunir Informações** , o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Para repetir a descoberta, clique em **Examinar Provedor**. Se o processo de descoberta for bem-sucedido, as matrizes de armazenamento, pools de armazenamento, fabricante, modelo e capacidade descobertos serão listados na página.

    ![Descoberta do armazenamento](./media/site-recovery-vmm-san/discover.png)
7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN serão importadas dos pools de armazenamento. Crie LUNs de acordo com os aplicativos que você precisa proteger, seus requisitos de capacidade e o requisito para os que precisam replicar juntos.

    ![Classificação do armazenamento](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Criar LUNs e alocar armazenamento

Crie LUNs de acordo com os aplicativos que você precisa proteger, com seus requisitos de capacidade e com o que precisam replicar juntos.

1. Depois que o armazenamento é exibido na malha do VMM, você pode [provisionar LUNs](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > Não adicione VHDs à VM que está habilitada para replicação nas LUNs. Se essas LUNs não estiverem em um grupo de replicação do Site Recovery. Caso faça isso, eles não serão detectados pelo Site Recovery.
     >
     
2. Aloque a capacidade de armazenamento ao cluster de host Hyper-V para que o VMM possa implantar dados da máquina virtual para o armazenamento provisionado:

   * Antes de alocar o armazenamento ao cluster, você precisará alocá-lo ao grupo de hosts do VMM no qual o cluster reside. Saiba mais sobre [alocar unidades lógicas de armazenamento](https://technet.microsoft.com/library/gg610686.aspx) e [pools de armazenamento](https://technet.microsoft.com/library/gg610635.aspx) para um grupo de hosts.</a>.
   * Em seguida, aloque a capacidade de armazenamento para o cluster conforme descrito em [Como configurar o armazenamento em um cluster de host Hyper-V no VMM](https://technet.microsoft.com/library/gg610692.aspx)</a>.

    ![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)
3. Em **Especificar Protocolo e Endereço do Provedor de SMI-S de Armazenamento**, selecione **SMI-S CIMXML**. Especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

    ![Conexão com o provedor](./media/site-recovery-vmm-san/connect-settings.png)
4. Em **Executar como conta**, especifique uma conta Executar como de VMM que possa acessar o provedor ou crie uma nova conta.
5. Em **Reunir Informações** , o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Se você precisar repetir, clique em **Examinar Provedor**. Quando o processo de descoberta é bem-sucedido, as matrizes de armazenamento, os pools de armazenamento, o fabricante, o modelo e a capacidade são listados na página.

    ![Descoberta do armazenamento](./media/site-recovery-vmm-san/discover.png)
7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN serão importadas dos pools de armazenamento.

    ![Classificação do armazenamento](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Criar grupos de replicação

Crie um grupo de replicação que inclui todos os LUNs que precisarão ser replicados juntos.

1. No console do VMM, abra a guia **Grupos de Replicação** das propriedades de matriz de armazenamento e clique em **Novo**.
2. Crie o grupo de replicação.


    ![Grupo de replicação de SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configurar redes

Se você quiser configurar o mapeamento de rede, faça o seguinte:

1. [Leia sobre](site-recovery-network-mapping.md) mapeamento de rede.
2. Prepare redes de VM no VMM:

   * [Configure redes lógicas](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Configure as redes de VMM](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Etapa 2: criar um cofre

1. Entre no [Portal de Gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar no cofre.
2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre do Site Recovery**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região** , selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-san/create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de **Serviços de Recuperação**.

### <a name="register-the-vmm-servers"></a>Registrar os servidores VMM

1. Abra a página Início Rápido na página **Serviços de Recuperação** . O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

    ![Ícone de Inicialização Rápida](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Na lista suspensa, selecione **Entre o site local do Hyper-V usando a replicação de matriz**.

    ![Chave de registro](./media/site-recovery-vmm-san/select-san.png)
3. Em **Preparar servidores VMM**, baixe a versão mais recente do arquivo de instalação do Provedor do Azure Site Recovery.
4. Execute esse arquivo no servidor VMM de origem. Se o VMM for implantado em um cluster e você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós. Se estiver atualizando o Provedor, você precisará fazer a atualização em todos os nós para que tenham a mesma versão do Provedor.
5. O instalador verifica os pré-requisitos e solicita permissão para interromper o serviço VMM para iniciar a instalação do Provedor. O serviço será reiniciado automaticamente quando a instalação for finalizada. Em um cluster do VMM, você será solicitado a parar a função Cluster.
6. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-san/ms-update.png)
    
7. Por padrão, o local de instalação do Provedor é definido como <SystemDrive>\Arquivos de Programas\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Clique em **Instalar** para começar.

    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)
    
8. Após a instalação do Provedor, clique em **Registrar** para registrar o servidor VMM no cofre.

    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)
    
9. Em **Conexão de Internet** , especifique como o Provedor se conecta à Internet. Selecione **Usar configurações de proxy padrão do sistema** se quiser usar as configurações de conexão com a Internet padrão no servidor.

    ![Configurações da Internet](./media/site-recovery-vmm-san/proxy-details.png)

   * Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o Provedor. Quando você definir as configurações personalizadas de proxy, um teste será executado para verificar a conexão proxy.
   * Se usar um proxy personalizado ou se seu proxy padrão exigir autenticação, você deverá inserir os detalhes do proxy, incluindo a porta e o endereço.
   * Essas [URLs](site-recovery-best-practices.md#url-access) devem poder ser acessadas do servidor VMM.
   * Se você usar um proxy personalizado, uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada. Você pode modificar as configurações de conta Executar como no console do VMM (**Configurações** > **Segurança** > **Contas Executar Como** > **DRAProxyAccount**). Você precisa reiniciar o serviço do VMM para que a alteração tenha efeito.
10. Em **Chave de Registro**, selecione a chave que você baixou do portal e copiou para o servidor VMM.
11. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.

    ![Registros do servidor](./media/site-recovery-vmm-san/vault-creds.png)
12. A configuração de criptografia é usada apenas para o VMM na replicação do Azure. Você pode ignorá-la.

    ![Registros do servidor](./media/site-recovery-vmm-san/encrypt.png)
13. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
14. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM. Esta ação só precisa acontecer uma vez em cada servidor. Se você não desejar sincronizar todas as nuvens, deixe essa configuração desmarcada e sincronize cada nuvem individualmente nas propriedades da nuvem no console do VMM.


    ![Registros do servidor](./media/site-recovery-vmm-san/friendly-name.png)
15. Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido em **Servidores** > **Servidores VMM** no cofre.

### <a name="command-line-installation"></a>Instalação de linha de comando

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

* **/ Credenciais**: parâmetro obrigatório para o local em que o arquivo de chave de registro está localizado  
* **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no portal do Azure Site Recovery.
* **/EncryptionEnabled**: parâmetro usado somente ao replicar do VMM para o Azure.
* **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
* **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
* **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exigir autenticação).
* **/proxyPassword** : parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exigir autenticação).

## <a name="step-3-map-storage-arrays-and-pools"></a>Etapa 3: Mapear as matrizes e pools de armazenamento

Mapeie as matrizes primária e secundária para especificar qual pool de armazenamento secundário recebe os dados de replicação do pool principal. Mapeie o armazenamento antes de configurar a replicação porque as informações de mapeamento são usadas quando você ativa a proteção para grupos de replicação.

Antes de começar, verifique se as nuvens VMM aparecem no cofre. As nuvens são detectadas quando você sincroniza todas as nuvens durante a instalação do Provedor ou quando sincroniza uma nuvem específica no console do VMM.

1. Clique em **Recursos** > **Armazenamento do Servidor** > **Mapear Matrizes de Origem e Destino**.
    ![Registro do servidor](./media/site-recovery-vmm-san/storage-map.png)

2. Selecione as matrizes de armazenamento no site primário e mapeie-as para armazenar as matrizes no site secundário. Em **Pools de Armazenamento**, selecione um pool de armazenamento de origem e de destino para mapear.


   ![Registros do servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Etapa 4: Definir configurações de replicação

Depois que os servidores VMM são registrados, defina as configurações de proteção de nuvem.

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos** , selecione a nuvem > **Configuração**.
3. Em **Destino**, selecione **VMM**.
4. Em **Local de destino**, selecione o servidor VMM que gerencia a nuvem que você deseja usar para a recuperação.
5. Em **Nuvem de destino**, selecione a nuvem de destino que você deseja usar para o failover da VM.
   * É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para máquinas virtuais que você protegerá.
   * Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
6. O Site Recovery verifica se as nuvens têm acesso ao armazenamento SAN e se as matrizes de armazenamento foram mapeadas.
7. Se a verificação for bem-sucedida, em **Tipo de replicação**, selecione **SAN**.

Depois de salvar as configurações, um trabalho será criado e poderá ser monitorado na guia **Trabalhos** . As configurações podem ser modificadas na guia **Configurar**. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.

## <a name="step-5-enable-network-mapping"></a>Etapa 5: Habilitar o mapeamento de rede

1. Na página Início Rápido, clique em **Mapear redes**.
2. Selecione o servidor VMM de origem e o servidor VMM de destino para os quais as redes serão mapeadas. A lista de redes de origem e suas redes de destino associadas é exibida. Um valor vazio é mostrado para redes que não são mapeadas. Clique no ícone de informações ao lado dos nomes de rede de origem e de destino para exibir as sub-redes de cada rede.
3. Selecione uma rede em **Rede na origem** e clique em **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe.

    ![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Selecione uma das redes VM do servidor VMM de destino.


    ![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map2.png)
5. Quando você seleciona uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. As redes de destino disponíveis também são exibidas. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens que você está usando para replicação.
6. Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho começará a acompanhar o andamento. Você pode exibi-lo na Guia **Trabalhos** .

## <a name="step-6-enable-replication-for-replication-groups"></a>Etapa 6: habilitar a replicação para grupos de replicação

Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento.

1. Na página de propriedades da nuvem primária no portal do Site Recovery, abra a guia **Máquinas Virtuais** e clique em **Adicionar Grupo de Replicação**.
2. Selecione um ou mais grupos de replicação do VMM que estão associados com a nuvem, verifique se os conjuntos de origem e de destino e especifique a frequência de replicação.

Site Recovery, VMM e os provedores de SMI-S provisionam as LUNs de armazenamento do site de destino e habilitam a replicação de armazenamento. Se o grupo de replicação já foi replicado, o Site Recovery reutiliza a relação de replicação existente e atualiza as informações.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: habilitar a proteção para máquinas virtuais


Depois que um grupo de armazenamento estiver replicando, habilite a proteção das VMs no console do VMM usando um dos seguintes métodos:

* **Nova máquina virtual**— quando você cria uma nova VM, habilite a replicação e associe a VM ao grupo de replicação.
  Com essa opção, o VMM usa a colocação inteligente para otimizar o local do armazenamento de VM nas LUN do grupo de replicação. O Site Recovery orquestra a criação de uma VM de sombra no local secundário e aloca a capacidade para que as VMs de réplica possam ser iniciadas após o failover.
* **Máquina virtual existente**: se uma máquina virtual já estiver implantada no VMM, habilite a replicação e realize uma migração de armazenamento para um grupo de replicação. Após a conclusão, o VMM e o Site Recovery detectam a nova VM e começam a gerenciá-la no Site Recovery. Uma VM sombra é criada no site secundário e a capacidade é alocada para que a VM de réplica possa ser iniciada após o failover.


    ![Habilitar proteção](./media/site-recovery-vmm-san/enable-protect.png)

Depois que as VMs são habilitadas para replicação, elas aparecem no console do Site Recovery. Você pode exibir propriedades da VM, acompanhar o status e o failover de grupos de replicação que contêm várias VMs. Na replicação de SAN, todas as VMs associadas a um grupo de replicação devem fazer failover juntas. Isso ocorre porque o failover ocorre na camada de armazenamento pela primeira vez. É importante agrupar seus grupos de replicação corretamente e colocar apenas as VMs juntas.

> [!NOTE]
> Depois de habilitar a replicação para uma VM, você não deve adicionar VHDs para ela para LUNs que não estejam localizadas em um grupo de replicação do Site Recovery. Caso faça isso, eles não serão detectados pelo Site Recovery.
>
>

Você pode acompanhar o andamento, inclusive da replicação inicial, na guia **Trabalhos**. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover.

![Trabalho de proteção da máquina virtual](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Etapa 8: testar a implantação

Teste a implantação para ter certeza de que as VMs realizam failover conforme o esperado. Para isso, crie um plano de recuperação e execute um teste de failover.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter VMs que são habilitadas para failover e recuperação. Selecione **SAN** para exibir somente as nuvens que estão configuradas para replicação de SAN.

    ![Criar plano de recuperação](./media/site-recovery-vmm-san/r-plan.png)

3. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as VMs associadas ao grupo são adicionadas ao plano de recuperação. Essas VMs são adicionadas ao grupo padrão do plano de recuperação (Grupo 1). Você pode adicionar mais grupos, se necessário. Após a replicação,as VMs iniciam de acordo com a ordem dos grupos do plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Depois que um plano de recuperação é criado, ele aparece na lista da guia **Planos de Recuperação** . Selecione o plano e clique em **Failover de Teste**.
5. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Com essa opção habilitada, as VMs de réplica com failover não serão conectadas a nenhuma rede. Isso testa se as VMs fazem failover como esperado, mas não testa o ambiente de rede. [Leia mais](site-recovery-failover.md#run-a-test-failover) sobre outras opções de rede.

    ![Selecionar rede de teste](./media/site-recovery-vmm-san/test-fail1.png)


1. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.
6. A VM de teste é criada no mesmo host no qual existe a VM de réplica. Ele não será adicionado na nuvem em que a VM de réplica está localizada.
2. Após a replicação a VM de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, ele será atualizado automaticamente. Se você não estiver usando DHCP e quiser os mesmos endereços, precisará executar alguns scripts.
3. Execute o script para recuperar o endereço IP.


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

Depois de executar um failover de teste para verificar se o seu ambiente está funcionando conforme o esperado, [saiba mais sobre](site-recovery-failover.md) os diferentes tipos de failover.




<!--HONumber=Jan17_HO5-->


