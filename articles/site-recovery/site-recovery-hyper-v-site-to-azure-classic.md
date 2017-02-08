---
title: "Replicar entre máquinas virtuais Hyper-V locais e o Azure (sem o VMM) com o Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar máquinas virtuais Hyper-V para Azure com o Azure Site Recovery quando as máquinas não são gerenciadas em nuvens do VMM."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 3f4c4483-e3dd-495a-bd02-c16e9e28c88d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: ea89244efea6afa7d7b9d60f400117284fb5d1e1
ms.openlocfilehash: 3c5e51c562d9251f2ad40eeb1939d1651c845391


---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-without-vmm-with-azure-site-recovery"></a>Replicar entre máquinas virtuais Hyper-V no local e o Azure (sem VMM) com o Azure Site Recovery
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Portal clássico](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

Bem-vindo ao Azure Site Recovery!

A Recuperação de Site é um serviço do Azure que contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre). A Recuperação de Site orquestra a replicação de máquinas virtuais e servidores físicos locais para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo descreve como replicar máquinas virtuais Hyper-V locais para o Azure, usando o Azure Site Recovery no portal do Azure. Nesse cenário, os servidores Hyper-V não são gerenciados em nuvens do VMM.

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).





## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de Site no portal do Azure

O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o Azure Resource Manager e o clássico. O Azure também tem dois portais: portal clássico do Azure e portal do Azure.

Este artigo descreve como implantar no portal clássico. O portal clássico pode ser usado para manter os cofres existentes. Não é possível criar novos cofres usando o portal clássico.

## <a name="site-recovery-in-your-business"></a>Recuperação de Site em sua empresa

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado e como recuperarão as condições normais de trabalho assim que possível. Aqui está o que a Recuperação de Site pode fazer:

* Proteção externa para aplicativos de negócios em execução em VMs Hyper-V.
* Um único local para configurar, gerenciar e monitorar replicação, failover e recuperação.
* Failover simples no Azure e failback (restauração) do Azure para servidores host Hyper-V no site local.
* Planos de recuperação que incluem várias VMs, para que cargas de trabalho de aplicativo em camadas façam failover juntas.

## <a name="azure-prerequisites"></a>Pré-requisitos do Azure
* Você precisa de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Você precisa de uma conta de armazenamento do Azure para armazenar os dados replicados. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. [Saiba mais sobre o Armazenamento do Azure](../storage/storage-introduction.md). Observe que não há suporte para a movimentação de contas de armazenamento criadas usando o [novo portal do Azure](../storage/storage-create-storage-account.md) entre grupos de recursos.
* Você precisará de uma rede virtual do Azure para que as máquinas virtuais do Azure estejam conectadas a uma rede quando o failover de seu site primário for executado.

## <a name="hyper-v-prerequisites"></a>Pré-requisitos do Hyper-V
* No site local de origem será necessário um ou mais servidores executando o **Windows Server 2012 R2** com a função Hyper-V instalada ou o **Microsoft Hyper-V Server 2012 R2**. O servidor deve:
* Conter uma ou mais máquinas virtuais.
* Estar conectado à Internet, diretamente ou por meio de um proxy.
* Executar as correções descritas na KB [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977").

## <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual
As máquinas virtuais que você deseja proteger devem estar em conformidade com os [Requisitos de máquinas virtuais do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

## <a name="provider-and-agent-prerequisites"></a>Pré-requisitos de provedor e do agente
Como parte da implantação do Azure Site Recovery, você instalará o Provedor do Azure Site Recovery e o Agente dos Serviços de Recuperação do Azure em cada servidor Hyper-V. Observe que:

* Recomendamos que você sempre execute as versões mais recentes do provedor e do agente. Elas estão disponíveis no portal do Site Recovery.
* Todos os servidores Hyper-V em um cofre devem ter as mesmas versões do provedor e do agente.
* O provedor em execução no servidor deve se conectar ao Site Recovery pela Internet. Você pode fazer isso sem um proxy, com as configurações de proxy definidas no servidor Hyper-V ou com as configurações personalizadas de proxy definidas durante a instalação do provedor. Você precisa garantir que o servidor proxy que deseja usar é capaz acessar essas URLs para conectar ao Azure:

  * *.accesscontrol.windows.net
  * *.backup.windowsazure.com
  * *.hypervrecoverymanager.windowsazure.com
  * *.store.core.windows.net      
  * *.blob.core.windows.net
  - https://www.msftncsi.com/ncsi.txt
  - time.windows.com
  - time.nist.gov
* Além disso, permita os endereços IP descritos em [Intervalos de IP do armazenamento de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) e o protocolo HTTPS (443). Você precisa permitir os intervalos IP válidos da região do Azure que você planeja usar e aqueles do Oeste dos EUA.

O gráfico abaixo mostra os diferentes canais de comunicação e portas usados pela Recuperação de Site para coordenação e a replicação

![Topologia B2A](./media/site-recovery-hyper-v-site-to-azure-classic/b2a-topology.png)

## <a name="step-1-create-a-vault"></a>Etapa 1: criar um cofre
1. Entre no [Portal de Gerenciamento](https://portal.azure.com).
2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre do Site Recovery**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure-classic/vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de Serviços de Recuperação.

## <a name="step-2-create-a-hyper-v-site"></a>Etapa 2: criar um site do Hyper-V
1. Na página Serviços de Recuperação, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

    ![Início rápido](./media/site-recovery-hyper-v-site-to-azure-classic/quick-start-icon.png)
2. Na lista suspensa, selecione **Entre um site do Hyper-V local e o Azure**.

    ![Cenário de site do Hyper-V](./media/site-recovery-hyper-v-site-to-azure-classic/select-scenario.png)
3. Em **Criar um Site do Hyper-V**, clique em **Criar Site do Hyper-V**. Especifique um nome e salve.

    ![Site do Hyper-V](./media/site-recovery-hyper-v-site-to-azure-classic/create-site.png)

## <a name="step-3-install-the-provider-and-agent"></a>Etapa 3: instalar o Provedor e o agente
Instale o provedor e o agente em cada servidor Hyper-V onde haja VMs que você deseja proteger.

Se você estiver instalando em um cluster do Hyper-V, execute as etapas 5 a 11 em cada nó no cluster de failover. Após o registro de todos os nós e a habilitação da proteção, as máquinas virtuais serão protegidas mesmo se migrarem entre nós no cluster.

1. Em **Preparar servidores Hyper-V**, clique em **Baixar um arquivo de chave de registro**.
2. Na página **Baixar a Chave de Registro**, clique em **Baixar** ao lado do site. Baixe a chave para um local seguro onde possa ser facilmente acessada pelo servidor Hyper-V. A chave é válida para 5 dias após ser gerada.

    ![Chave de Registro](./media/site-recovery-hyper-v-site-to-azure-classic/download-key.png)
3. Clique em **Baixar o Provedor** para obter a versão mais recente.
4. Execute o arquivo em cada servidor Hyper-V que você deseja registrar no cofre. O arquivo instala dois componentes:
   * **Provedor do Azure Site Recovery**— Lida com a comunicação e a coordenação entre o servidor Hyper-V e o portal do Azure Site Recovery.
   * **Agente de Serviços de Recuperação do Azure**— Lida com o transporte de dados entre máquinas virtuais em execução no servidor de origem Hyper-V e o armazenamento do Azure.
5. No **Microsoft Update** você pode optar por atualizações. Com essa configuração habilitada, o Provedor e o Agente de atualizações serão instaladas de acordo com a diretiva do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-hyper-v-site-to-azure-classic/provider1.png)
6. Em **Instalação** especifique onde você deseja instalar o Provedor e o Agente no servidor Hyper-V.

    ![Local de instalação](./media/site-recovery-hyper-v-site-to-azure-classic/provider2.png)
7. Após a instalação continue para registrar o servidor no cofre.
8. Na página **Configurações do Cofre**, clique em **Procurar ** para selecionar o arquivo da chave. Especifique a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

    ![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure-classic/provider8.PNG)
9. Na página **Conexão com a Internet** , especifique como o Provedor se conecta ao Azure Site Recovery. Selecione **Usar configurações de proxy padrão do sistema** para usar as configurações de conexão com a Internet definidas no servidor. Se você não especificar um valor, as configurações padrão serão usadas.

   ![Configurações da Internet](./media/site-recovery-hyper-v-site-to-azure-classic/provider7.PNG)
10. Registro começa a registrar o servidor no cofre.

    ![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure-classic/provider15.PNG)
11. Após a conclusão do registro, os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido na guia **Sites do Hyper-V** da página **Servidores** no cofre.

### <a name="install-the-provider-from-the-command-line"></a>Instalar o provedor usando a linha de comando
Como alternativa, você pode instalar o provedor do Azure Site Recovery usando a linha de comando. Você deve usar esse método se quiser instalar o provedor em um computador que executa o Windows Server Core 2012 R2. Execute da linha de comando da seguinte forma:

1. Baixar o arquivo de instalação do provedor e a chave de registro em uma pasta. Por exemplo, C:\ASR.
2. Execute um prompt de comando como Administrador e digite

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o provedor executando:

        C:\ASR> setupdr.exe /i
4. Execute o seguinte para concluir o registro:

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Em que os parâmetros incluem:

* **/Credentials**: especifique o local da chave de registro que você baixou.
* **/FriendlyName**: especifique um nome para identificar o servidor host Hyper-V. Esse nome aparecerá no portal
* **/EncryptionEnabled**: opcional. Especifique se deseja criptografar máquinas virtuais de réplica no Azure (com criptografia em repouso).
* **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: Opcional. Especifique parâmetros de proxy se quiser usar um proxy personalizado; do contrário, o proxy existente exigirá autenticação.

## <a name="step-4-create-an-azure-storage-account"></a>Etapa 4: criar uma conta de armazenamento do Azure
* Na página **Preparar recursos**, selecione **Criar Conta de Armazenamento** para criar uma conta de armazenamento do Azure, caso você ainda não tenha uma. A conta deve ter a replicação geográfica habilitada. Ela deve estar na mesma região que o cofre do Azure Site Recovery e estar associada à mesma assinatura.

    ![Criar Conta de Armazenamento](./media/site-recovery-hyper-v-site-to-azure-classic/create-resources.png)

> [!NOTE]
> 1. Não há suporte para a movimentação das contas de armazenamento criadas usando o [novo portal do Azure](../storage/storage-create-storage-account.md) entre os grupos de recursos.
> 2. [Migração de contas de armazenamento](../azure-resource-manager/resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as contas de armazenamento usadas para a implantação do Site Recovery.
>

## <a name="step-5-create-and-configure-protection-groups"></a>Etapa 5: criar e configurar grupos de proteção
Grupos de proteção são agrupamentos lógicos de máquinas virtuais que você deseja proteger usando as mesmas configurações de proteção. Aplique as configurações de proteção em um grupo de proteção e essas configurações serão aplicadas em todas as máquinas virtuais que você adicionar ao grupo.

1. Em **Criar e configurar grupos de proteção**, clique em **Criar um grupo de proteção**. Se algum pré-requisito não estiver em vigor, uma mensagem será emitida e você poderá clicar em **Exibir detalhes** para saber mais.
2. Na guia **Grupos de Proteção** , adicione um grupo de proteção. Especifique um nome, o site de origem Hyper-V, o **Azure**de destino, o nome da sua assinatura do Azure Site Recovery e a conta de armazenamento do Azure.

    ![Grupo de proteção](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group.png)
3. Em **Configurações de replicação**, defina a **Frequência de cópia** para especificar com que frequência o delta de dados deve ser sincronizado entre a origem e o destino. Você pode definir como 30 segundos, 5 minutos ou 15 minutos.
4. Em **Reter pontos de recuperação** , especifique quantas horas de histórico de recuperação devem ser armazenadas.
5. Em **Frequência de instantâneos consistentes com o aplicativo** , é possível especificar se deve obter instantâneos que usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Por padrão, eles não obtidos. Defina essa opção com um valor menor do que o número dos pontos de recuperação adicionais que você configurar. Isso só terá suporte se a máquina virtual estiver sendo executada um sistema operacional Windows.
6. Em **Hora de início da replicação** , especifique quando a replicação inicial das máquinas virtuais no grupo de proteção deve ser enviada ao Azure.

    ![Grupo de proteção](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group2.png)

## <a name="step-6-enable-virtual-machine-protection"></a>Etapa 6: habilitar a proteção da máquina virtual
Adicione máquinas virtuais aos grupos de proteção para protegê-las.

> [!NOTE]
> Proteger VMs que executam o Linux com um endereço IP estático sem suporte.
>
>

1. Na guia **Computadores** do grupo de proteção, clique** em Adicionar máquinas virtuais aos grupos de proteção para habilitar a proteção**.
2. Na página **Habilitar Proteção da Máquina Virtual** selecione as máquinas virtuais que você deseja proteger.

    ![Habilitar Proteção da Máquina Virtual](./media/site-recovery-hyper-v-site-to-azure-classic/add-vm.png)

    Os trabalhos de Habilitar a Proteção são iniciados. Você pode acompanhar o andamento na guia **Trabalhos** . Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover.
3. Após a configuração da proteção, é possível:

   * Exibir as máquinas virtuais em **Itens Protegidos** > **Grupos de Proteção** > *protectiongroup_name* > **Máquinas Virtuais** É possível detalhar as informações sobre a máquina na guia **Propriedades**.
   * Configure as propriedades de failover de uma máquina virtual em **Itens Protegidos** > **Grupos de Proteção** > *protectiongroup_name* > **Máquinas Virtuais** *virtual_machine_name* > **Configurar**. Você pode configurar:

     * **Nome**: o nome da máquina virtual no Azure.
     * **Tamanho**: o tamanho de destino da máquina virtual que executa o failover.

       ![Configurar as propriedades da máquina virtual](./media/site-recovery-hyper-v-site-to-azure-classic/vm-properties.png)
   * Defina as configurações adicionais de máquina virtual em *Itens Protegidos** > **Grupos de Proteção** > *protectiongroup_name* > **Máquinas Virtuais** *virtual_machine_name* > **Configurar**, incluindo:

     * **Adaptadores de rede**: o número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino. Verifique o número de nics com suporte pelo tamanho da máquina virtual nas [especificações de tamanho de máquina virtual](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) .

       Quando você altera a dimensão de uma máquina virtual e salva as configurações, o número do adaptador de rede é alterado na próxima vez em que você abrir a página **Configurar** . O número de adaptadores de rede de máquinas virtuais de destino é o mínimo do número de adaptadores de rede na máquina virtual de origem e o número máximo de adaptadores de rede compatíveis com o tamanho da máquina virtual selecionada. É explicado abaixo:

       * Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
       * Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
       * Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
       
     * **Rede Azure**: especifique a rede na qual a máquina virtual devem executar failover. Se a máquina virtual tiver vários adaptadores de rede, todos deverão estar conectados à mesma rede do Azure.
     * **Sub-rede** Para cada adaptador de rede na máquina virtual, selecione a sub-rede na rede do Azure à qual a máquina deve se conectar após o failover.
     * **Endereço IP de destino**: se o adaptador de rede da máquina virtual de origem estiver configurado para usar um endereço IP estático, você poderá especificar o endereço IP da máquina virtual de destino para garantir que a máquina tenha o mesmo endereço IP após o failover.  Se você não especificar um endereço IP, qualquer endereço disponível no momento será atribuído durante o failover. Se você especificar um endereço que está sendo usado, o failover falhará.

     > [!NOTE] 
     > [Migração de redes](../azure-resource-manager/resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as redes usadas para a implantação do Site Recovery.
     >

     ![Configurar as propriedades da máquina virtual](./media/site-recovery-hyper-v-site-to-azure-classic/multiple-nic.png)




## <a name="step-7-create-a-recovery-plan"></a>Etapa 7: criar um plano de recuperação
Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais. [Saiba mais](site-recovery-create-recovery-plans.md) sobre a criação de um plano de recuperação.

## <a name="step-8-test-the-deployment"></a>Etapa 8: testar a implantação
Há duas maneiras de executar um failover de teste no Azure.

* **Failover de teste sem uma rede do Azure**– esse tipo de failover de teste verifica se a máquina virtual é mostrada corretamente no Azure. A máquina virtual não será conectada a nenhuma rede do Azure após o failover.
* **Failover de teste com uma rede do Azure**– esse tipo de failover verifica se o ambiente de replicação inteiro é mostrado conforme o esperado e se as máquinas virtuais com failover serão conectadas à rede do Azure de destino especificada. Observe que para o failover de teste, a sub-rede da máquina virtual de teste será determinada com base na sub-rede da máquina virtual de réplica. Isso é diferente da replicação normal quando a sub-rede de uma máquina virtual de réplica baseia-se na sub-rede da máquina virtual de origem.

Se você quiser executar um failover de teste sem especificar uma rede do Azure, não precisará de preparo prévio.

Para executar um failover de teste com uma rede do Azure de destino, você precisará criar uma nova rede do Azure isolada da rede de produção do Azure (o comportamento padrão quando você cria uma nova rede no Azure). Leia [Executar um failover de teste](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes.

Para testar totalmente a replicação e a implantação da rede, você precisará configurar a infraestrutura para que a máquina virtual replicada funcione como esperado. Uma maneira de fazer isso é configurar uma máquina virtual como um controlador de domínio com DNS e replicá-la para o Azure usando a Recuperação de Site para criá-la na rede de teste com a execução de um failover de teste.  [Leia mais sobre](site-recovery-active-directory.md#test-failover-considerations) as considerações sobre failover de teste para o Active Directory.

Execute o failover de teste da seguinte maneira:

> [!NOTE]
> Para obter o melhor desempenho ao fazer um failover para o Azure, não deixe de instalar o agente do Azure no computador protegido. Isso ajuda na inicialização mais rápida e também no diagnóstico em caso de problemas. O agente do Linux pode ser encontrado [aqui](https://github.com/Azure/WALinuxAgent) e o agente do Windows pode ser encontrado [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)
>
>

1. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
2. Na página **Confirmar Failover de Teste**, selecione **Nenhum** ou uma rede específica do Azure.  Observe que, se você selecionar **Nenhum** , o failover de teste verificará se a máquina virtual foi replicada corretamente para o Azure, mas não verificará sua configuração de rede de replicação.

    ![Failover de Teste](./media/site-recovery-hyper-v-site-to-azure-classic/test-nonetwork.png)
3. Na guia **Trabalhos** , você pode acompanhar o progresso do failover. Você também deverá ver a réplica de teste de máquina virtual no portal do Azure. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
4. Quando o failover atinge a fase **Concluir teste**, clique em **Concluir Teste** para concluir o failover de teste. Você pode fazer uma busca detalhada na guia **Trabalho** para acompanhar o progresso e o status do failover, e para realizar as ações necessárias.
5. Após o failover, você poderá ver a réplica de teste da máquina virtual no portal do Azure. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.

   1. Verificar se as máquinas virtuais foram iniciadas com êxito.
   2. Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover. Você também precisará adicionar um ponto de extremidade RDP na máquina virtual. É possível aproveitar um [runbook de automação do Azure](site-recovery-runbook-automation.md) para fazer isso.
   3. Após o failover, se você usar um endereço IP público para se conectar à máquina virtual no Azure usando a Área de Trabalho Remota, verifique se você não tem qualquer política de domínio que impeça a conexão com uma máquina virtual usando um endereço público.
6. Após a conclusão do teste, faça o seguinte:

   * Clique em **Failover de teste concluído**. Limpe o ambiente de teste para desligar automaticamente e excluir as máquinas virtuais de teste.
   * Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
7. Quando o failover atinge a fase **Testes completos** , conclua a verificação da seguinte maneira:
   1. Visualize a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual foi iniciada com êxito.
   2. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
   3. Clique em **Concluir o teste** para concluí-lo.
   4. Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
   5. Clique em **Failover de teste concluído**. Limpa o ambiente de teste para desligar automaticamente e excluir a máquina virtual de teste.

## <a name="next-steps"></a>Próximas etapas
Depois que a implantação é configurada e está em funcionamento, [saiba mais](site-recovery-failover.md) sobre o failover.



<!--HONumber=Dec16_HO4-->


