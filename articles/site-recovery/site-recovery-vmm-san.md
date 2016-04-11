<properties
	pageTitle="Replicar VMs Hyper-V em uma nuvem VMM para um site secundário com o Azure Site Recovery usando SAN | Microsoft Azure"
	description="Este artigo descreve como replicar máquinas virtuais Hyper-V entre dois sites com o Azure Site Recovery usando a replicação SAN."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="raynew"/>

# Replicar VMs Hyper-V em uma nuvem VMM para um site secundário com o Azure Site Recovery usando SAN

Neste artigo, você aprenderá a implantar o Site Recovery para orquestrar e automatizar a replicação de SAN e o failover para as máquinas virtuais Hyper-V localizadas em nuvens do System Center VMM para um site secundário do VMM.

Depois de ler, publique quaisquer comentários ou perguntas ao final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR está centrada em soluções que mantêm os dados comerciais seguros e passíveis de recuperação, e as cargas de trabalho continuamente disponíveis, mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. A Recuperação de Site pode ser usada em vários cenários e pode proteger diversas cargas de trabalho. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo inclui instruções para configurar a replicação de VMs do Hyper-V de um site do VMM para outro usando replicação de SAN. Ele inclui uma visão geral da arquitetura, além de pré-requisitos de implantação e instruções. Você irá detectar e classificar o armazenamento de SAN no VMM, provisionar LUN e alocar armazenamento para clusters Hyper-V. Ele é concluído com o teste do failover para verificar se tudo está funcionando conforme o esperado.


## Por que replicar com SAN?

Aqui está o que esse cenário fornece:

- O fornecimento de uma solução corporativa de replicação escalonável automatizada pela Recuperação de Site.
- Utilização dos recursos de replicação de SAN fornecidos pelos parceiros de armazenamento corporativo por meio do armazenamento fibre channel e iSCSI. Confira nossos [Parceiros de armazenamento SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
- Aproveita de sua infraestrutura de SAN atual para proteger aplicativos fundamentais implantados em clusters Hyper-V.
- Fornece suporte para clusters de convidado.
- Garantia de consistência da replicação em camadas diferentes de um aplicativo, com replicação sincronizada para RTO e RPO baixo, e replicação assíncrona para alta flexibilidade, dependendo das capacidades da matriz de armazenamento.  
- A integração com o VMM fornece gerenciamento de SAN no console do VMM, e o SMI-S no VMM descobre o armazenamento existente.  

## Arquitetura

Este cenário protege suas cargas de trabalho por meio do backup das máquinas virtuais Hyper-V de um site local do VMM para outro usando a replicação SAN.

![Arquitetura de SAN](./media/site-recovery-vmm-san/architecture.png)

Nesse cenário, os componentes incluem:

- **Máquinas virtuais locais**: seus servidores Hyper-V locais gerenciados em nuvens privadas do VMM contêm máquinas virtuais que você deseja proteger.
- **Servidores locais do VMM local**: um ou mais servidores do VMM em execução no site primário que você deseja proteger e no site secundário.
- **Armazenamento SAN**: uma matriz de SAN no site primário e outra no site secundário
-  **Cofre do Azure Site Recovery**: o cofre coordena e orquestra a réplica de dados, o failover e a recuperação entre os sites locais.
- **Provedor Azure Site Recovery**: o provedor é instalado em cada servidor VMM.

## Antes de começar

Verifique se estes pré-requisitos estão em vigor:

**Pré-requisitos** | **Detalhes** 
--- | ---
**As tabelas**| Você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. 
**VMM** | Você precisará de pelo menos um servidor do VMM implantado como servidor físico ou virtual autônomo ou como um cluster virtual. <br/><br/>O servidor do VMM deve executar o System Center 2012 R2 com as últimas atualizações cumulativas.<br/><br/>Você precisará de pelo menos uma nuvem configurada no servidor do VMM primário que deseja proteger e uma nuvem configurada no servidor do VMM secundário que deseja usar para proteção e recuperação<br/><br/>A nuvem de origem que você deseja proteger deve conter um ou mais grupos de hosts do VMM.<br/><br/>Todas as nuvens do VMM devem ter o perfil de Capacidade do Hyper-V definido.<br/><br/>Saiba mais sobre como configurar nuvens do VMM em [Configuração da malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) e [Passo a passo: criação de nuvens privadas com o System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
**Hyper-V** | Você precisará de um ou mais clusters Hyper-V nos sites primário e secundário, além de uma ou mais VMs no cluster Hyper-V de origem. Os grupos de hosts do VMM nos locais primários e secundários devem ter um ou mais clusters Hyper-V em cada grupo.<br/><br/>Os servidores Hyper-V host e de destino devem executar pelo menos o Windows Server 2012 com a função Hyper-V e ter as atualizações mais recentes instaladas.<br/><br/>Qualquer servidor Hyper-V com VMs que você deseja proteger deve estar localizado em uma nuvem VMM.<br/><br/>Se você estiver executando o Hyper-V em um cluster, observe que o agente de cluster não será criado automaticamente caso tenha um cluster baseado em endereços IP estáticos. Você precisará configurar o agente de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) na entrada do blog de Aidan Finn.
**Armazenamento SAN** | Usando a replicação SAN, é possível replicar máquinas virtuais armazenadas em cluster convidado com armazenamento iSCSI ou Fibre Channel ou usando discos rígidos virtuais (vhdx) compartilhados.<br/><br/>Você precisará de duas matrizes SAN configuradas, uma no site primário e outra no site secundário.<br/><br/>A infraestrutura de rede deve ser configurada entre as matrizes. Devem ser configurados o emparelhamento e a replicação. As licenças de replicação devem ser definidas de acordo com os requisitos de matriz de armazenamento.<br/><br/>A rede deve ser configurada entre os servidores de host Hyper-V e a matriz de armazenamento para que os hosts possam se comunicar com os LUNs de armazenamento usando ISCSI ou Fibre Channel.<br/><br/> Confira a lista de [matrizes de armazenamento com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/>Os provedores de SMI-S, fornecidos pelos fabricantes de matriz de armazenamento, devem ser instalados e as matrizes de SAN devem ser gerenciadas pelo Provedor. Configure o Provedor de acordo com sua documentação.<br/><br/>Verifique se o provedor de SMI-S para a matriz está em um servidor que o servidor do VMM possa acessar via rede pelo endereço IP ou FQDN.<br/><br/>Cada matriz SAN deve ter um ou mais pools de armazenamento disponíveis para uso nessa implantação. O servidor do VMM no site primário precisará gerenciar a matriz primária e o servidor do VMM secundário gerenciará a matriz secundária.<br/><br/>O servidor do VMM no site primário deverá gerenciar a matriz primária e o servidor do VMM secundário deverá gerenciar a matriz secundária.
**Mapeamento de rede** | Você pode definir o mapeamento de rede para assegurar que as máquinas virtuais de réplica sejam colocadas da melhor forma possível em servidores host Hyper-V secundários após o failover e que possam se conectar às redes VM apropriadas. Se você não configurar a réplica de mapeamento de rede, as VMs não serão conectadas a nenhuma rede após o failover.<br/><br/>Para configurar o mapeamento de rede durante a implantação, certifique-se de que as máquinas virtuais no servidor host Hyper-V de origem estejam conectadas a uma rede VM do VMM. Essa rede deve estar vinculada a uma rede lógica associada à nuvem.<br/<br/>A nuvem de destino no servidor do VMM secundário que você usar para recuperação deverá ter uma rede VM correspondente configurada, que, por sua vez, deverá estar vinculada a uma rede lógica correspondente associada à nuvem de destino.<br/><br/>[Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede.


## Etapa 1: preparar a infraestrutura do VMM

Para preparar a infraestrutura do VMM, é necessário:

1. Certificar-se de que as nuvens do VMM estejam configuradas
2. Integre e classifique o armazenamento de SAN no VMM
3. Criar LUNs e alocar armazenamento
4. Criar grupos de replicação
5. Configurar redes de VMM

### Certificar-se de que as nuvens do VMM estejam configuradas

A Recuperação de Site coordena a proteção de máquinas virtuais localizadas nos servidores do host Hyper-V em nuvens do VMM. Será necessário garantir que essas nuvens estejam configuradas corretamente antes de começar a implantação da Recuperação de Site. Saiba mais em [Criar nuvens privadas](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blog de Keith Mayer.

### Integre e classifique o armazenamento de SAN no VMM

Adicionar e classificar SANs no console do VMM:

1. No espaço de trabalho **Malha**, clique em **Armazenamento**. Clique em **Início** > **Adicionar Recursos** > **Dispositivos de Armazenamento** para iniciar o Assistente para Adicionar Dispositivos de Armazenamento.
2. Na página **Selecionar um tipo de provedor de armazenamento**, selecione **Dispositivos SAN e NAS descobertos e gerenciados por um provedor de SMI-S**.

	![Tipo de provedor](./media/site-recovery-vmm-san/provider-type.png)

3. Na página **Especificar o protocolo e o endereço do armazenamento do provedor de SMI-S** selecione **SMI-S CIMXML** e especifique as configurações para conectar-se ao provedor.
4. No **Endereço IP do Provedor ou FQDN** e **porta TCP/IP**, especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

	![Conexão com o provedor](./media/site-recovery-vmm-san/connect-settings.png)

5. Em **Executar como conta** especifique uma conta Executar como VMM que possa acessar o provedor ou crie uma nova conta.
6. Na página **Reunir Informações**, o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Para repetir a descoberta, clique em **Examinar Provedor**. Se o processo de descoberta for bem-sucedido, as matrizes de armazenamento, pools de armazenamento, fabricante, modelo e capacidade descobertos serão listados na página.

	![Descoberta do armazenamento](./media/site-recovery-vmm-san/discover.png)

7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN serão importadas dos pools de armazenamento. Crie LUNs de acordo com os aplicativos que você precisa proteger, seus requisitos de capacidade e o requisito para os que precisam replicar juntos.

	![Classificação do armazenamento](./media/site-recovery-vmm-san/classify.png)

### Criar LUNs e alocar armazenamento

1. Depois que o armazenamento SAN estiver integrado ao VMM, crie LUNs (unidades lógicas (provisão).

	- [Como selecionar um método para criar unidades lógicas no VMM](https://technet.microsoft.com/library/gg610624.aspx)
	- [Como provisionar armazenamento de unidades lógicas no VMM](https://technet.microsoft.com/library/gg696973.aspx)

	>[AZURE.NOTE] Depois de habilitar a replicação para uma máquina, você não deve adicionar VHDs para ela para LUNs que não estejam localizadas em um grupo de replicação de Recuperação de Site. Caso faça isso, eles não serão detectados pela Recuperação de Site.

2. Aloque a capacidade de armazenamento ao cluster de host Hyper-V para que o VMM possa implantar dados da máquina virtual para o armazenamento provisionado:

	- Antes de alocar o armazenamento ao cluster, você precisará alocá-lo ao grupo de hosts do VMM no qual o cluster reside. Confira [Como alocar unidades lógicas de armazenamento para um grupo de hosts](https://technet.microsoft.com/library/gg610686.aspx) e [Como alocar pools de armazenamento a um grupo de hosts](https://technet.microsoft.com/library/gg610635.aspx).</a>
	- Em seguida, aloque a capacidade de armazenamento para o cluster conforme descrito em [Como configurar o armazenamento em um cluster de host Hyper-V no VMM](https://technet.microsoft.com/library/gg610692.aspx).</a>.

### Criar grupos de replicação

Crie um grupo de replicação que inclui todos os LUNs que precisarão ser replicados juntos.

1. No console do VMM, abra a guia **Grupos de Replicação** das propriedades de matriz de armazenamento e clique em **Novo**.
2. Em seguida, crie o grupo de replicação.

	![Grupo de replicação de SAN](./media/site-recovery-vmm-san/rep-group.png)

### Configurar redes

Se você quiser configurar o mapeamento de rede, faça o seguinte:

1. [Leia sobre](site-recovery-network-mapping.md) mapeamento de rede.
2. Prepare redes de VM no VMM:

	- [Configure redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
	- [Configure as redes de VMM](https://technet.microsoft.com/library/jj721575.aspx).

## Etapa 2: criar um cofre


1. Entre no [Portal de Gerenciamento](https://portal.azure.com) do servidor VMM que você deseja registrar.

2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.

3. Clique em **Criar Novo** > **Criação Rápida**.

4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

6. Clique em **Criar cofre**.

	![Novo cofre](./media/site-recovery-vmm-san/create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.


### Registrar os servidores VMM

1. Abra a página Início Rápido na página **Serviços de Recuperação**. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Ícone de Inicialização Rápida](./media/site-recovery-vmm-san/quick-start-icon.png)

2. Na lista suspensa, selecione **Entre o site local do Hyper-V usando a replicação de matriz**.

	![Chave de Registro](./media/site-recovery-vmm-san/select-san.png)


3. Em **Preparar servidores VMM**, baixe a versão mais recente do arquivo de instalação do Provedor do Azure Site Recovery.
4. Execute esse arquivo no servidor VMM de origem. Se o VMM for implantado em um cluster e você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós. Observe que, se estiver atualizando o Provedor você precisará fazer a atualização em todos os nós porque todos eles devem estar executando a mesma versão do Provedor.
5. O Instalador faz algumas **Verificações de Pré-requisitos** e solicita permissão para interromper o serviço VMM para iniciar a instalação do Provedor. O Serviço VMM será reiniciado automaticamente quando a instalação for finalizada. Se estiver instalando em um cluster do VMM, você deverá parar a função de Cluster.
6. No **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

	![Atualizações da Microsoft](./media/site-recovery-vmm-san/ms-update.png)

7. O local de instalação é definido como **<SystemDrive>\\Arquivos de Programas\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Clique no botão Instalar para iniciar a instalação do Provedor.

	![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. Após o Provedor ser instalado, clique no botão 'Registrar' para registrar o servidor no cofre.

	![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. Em **Conexão de Internet**, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione **Usar configurações de proxy padrão do sistema** para usar as configurações de conexão com a Internet definidas no servidor.

	![Configurações da Internet](./media/site-recovery-vmm-san/proxy-details.png)

	- Se quiser usar um proxy personalizado, você deverá configurá-lo antes de instalar o provedor. Quando você definir as configurações personalizadas de proxy, será executado um teste para verificar a conexão proxy.
	- Se usar um proxy personalizado ou se seu proxy padrão exigir autenticação, você precisará inserir os detalhes do proxy, incluindo a porta e o endereço do proxy.
	- As URLs a seguir devem estar acessíveis no servidor do VMM e nos hosts Hyper-V
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Permita os endereços IP descritos em [Intervalos de IP do armazenamento de dados do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443). Você teria que fazer uma lista de intervalos IP válidos da região do Azure que você planeja usar e do oeste dos EUA.
	- Se você usar um proxy personalizado, uma conta RunAs VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações da conta RunAs VMM podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho Configurações, expanda Segurança, clique em contas Executar como e modifique a senha de DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração entre em vigor.

10. Em **Chave de registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
11. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado. 

	![Registros do servidor](./media/site-recovery-vmm-san/vault-creds.png)

12. A configuração de criptografia é usada apenas para o Cenário VMM para Azure; se você for um usuário que usa exclusivamente VMM para VMM, você pode ignorar esta tela.

	![Registros do servidor](./media/site-recovery-vmm-san/encrypt.png)

13. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
14. Em **Sincronização inicial dos metadados de nuvem**, especifique um nome amigável para o servidor que aparecerá no cofre e selecione se deseja sincronizar os metadados para todas as nuvens no servidor do VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.

	![Registros do servidor](./media/site-recovery-vmm-san/friendly-name.png)

15. Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido na guia *Servidores VMM* da página **Servidores** no cofre.

### Instalação de linha de comando

O Provedor do Azure Site Recovery também pode ser instalado usando a linha de comando a seguir. Esse método pode ser usado para instalar o provedor em um Server CORE para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do Provedor e a chave de registro em uma pasta, por exemplo, C:\\ASR
2. Parar o Serviço System Center Virtual Machine Manager
3. Extrair o instalador do Provedor executando o seguinte de um prompt de comando com privilégios de **Administrador**:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instalar o provedor executando o seguinte:

		C:\ASR> setupdr.exe /i

5. Registrar o provedor executando o seguinte:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Em que os parâmetros são:

 - **/Credentials**: parâmetro obrigatório que especifica o local no qual o arquivo da chave de registro está localizado  
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no portal do Azure Site Recovery.
 - **/EncryptionEnabled**: parâmetro opcional que você precisa usar apenas no Cenário VMM para Azure se precisar da criptografia de suas máquinas virtuais em repouso no Azure. Certifique-se de que o nome do arquivo que você fornecer tem uma extensão **. pfx**.
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exige autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a Senha para autenticação com o servidor proxy (se o proxy exige autenticação).


## Etapa 3: Mapear as matrizes e pools de armazenamento

Mapeie as matrizes para especificar qual pool de armazenamento secundário recebe os dados de replicação do pool principal. Você deve mapear o armazenamento antes de configurar a proteção em nuvem, pois as informações de mapeamento são usadas quando você ativa a proteção para grupos de replicação.

Antes de começar, verifique se as nuvens aparecem no cofre. As nuvens são detectadas tanto pela seleção de sincronizar todas as nuvens quando você instala o provedor ou selecionando para sincronizar uma nuvem específica na guia **Geral** das propriedades da nuvem no console do VMM. Mapear as matrizes de armazenamento da seguinte forma:

1. Clique em **Recursos** > **Armazenamento do Servidor** > **Mapear as Matrizes de Origem e Destino**. ![Registros do servidor](./media/site-recovery-vmm-san/storage-map.png).
2. Selecione as matrizes de armazenamento no site primário e mapeie-as para armazenar as matrizes no site secundário.
3.  Mapeie os pools de armazenamento de origem e de destino nas matrizes. Para fazer isso, em **Pools de Armazenamento** selecione um pool de armazenamento de origem e de destino para mapear.

	![Registros do servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## Etapa 4: definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia <b>Itens Protegidos </b> no cofre.

![Publicar uma nuvem](./media/site-recovery-vmm-san/clouds-list.png)

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. Observe que:
3. Em **Destino**, selecione **VMM**.
4. Em **Local de Destino**, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para a recuperação.
5. Em **Nuvem de Destino**, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:
	- É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para máquinas virtuais que você protegerá.
	- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
6. A Recuperação de Site do Azure verifica se as nuvens têm acesso ao armazenamento capaz de replicação de SAN e se as matrizes de armazenamento estão igualadas. Os pares de matrizes participantes são exibidos.
7. Se a verificação for bem-sucedida, em **Tipo de replicação**, selecione **SAN**.

Depois de salvar as configurações, um trabalho será criado e poderá ser monitorado pela guia **Trabalhos**. As configurações de nuvem podem ser modificadas na guia **Configurar**. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.

## Etapa 5: Habilitar o mapeamento de rede

1. Na página Início Rápido, clique em **Mapear redes**.
2. Selecione o servidor VMM de origem do qual você deseja mapear redes e o servidor VMM de destino para o qual as redes serão mapeadas. A lista de redes de origem e suas redes de destino associadas é exibida. Um valor vazio é mostrado para redes que não estão mapeadas no momento. Clique no ícone de informações ao lado dos nomes de rede de origem e de destino para exibir as sub-redes de cada rede.
3. Selecione uma rede em **Rede na origem** e clique em **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe.

	![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map1.png)

4. Na caixa de diálogo exibida, selecione uma das redes de VM no servidor VMM de destino.

	![Arquitetura de SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quando você seleciona uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. As redes de destino disponíveis que estão associados às nuvens usadas para proteção também são exibidas. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens que você está usando para proteção.
6.  Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho é iniciado para rastrear o progresso do mapeamento. Você pode exibi-lo na Guia **Trabalhos**.


## Etapa 6: habilitar a replicação para grupos de replicação

Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento.

1. No portal do Azure Site Recovery, na página de propriedades da nuvem primária, abra a guia **Máquinas Virtuais**. Clique em **Adicionar Grupo de Replicação**.
2. Selecione um ou mais grupos de replicação do VMM que estão associados com a nuvem, verifique se os conjuntos de origem e de destino e especifique a frequência de replicação.

Quando essa operação for concluída, Recuperação de Site do Azure, juntamente com o VMM e os provedores de SMI-S provisionam o armazenamento do site de destino LUNs e habilitam a replicação de armazenamento. Se o grupo de replicação já foi replicado, a Recuperação de Site do Azure reutiliza a relação de replicação existente e atualiza as informações na Recuperação de Site do Azure.

## Etapa 7: habilitar a proteção para máquinas virtuais

Depois que um grupo de armazenamento estiver replicando, habilite a proteção para máquinas virtuais no console do VMM usando um dos seguintes métodos:

- **Nova máquina virtual**: no console do VMM, ao criar uma nova máquina virtual, habilite a proteção do Azure Site Recovery e associe a máquina virtual ao grupo de replicação. Com essa opção, o VMM usa a colocação inteligente para otimizar o local do armazenamento de máquina virtual nos LUN do grupo de replicação. A Recuperação de Site do Azure orquestra a criação de uma máquina virtual de sombra no local secundário e aloca a capacidade para que as máquinas virtuais de réplica possam ser iniciadas após o failover.
- **Máquina virtual existente**: se uma máquina virtual já estiver implantada no VMM, habilite a proteção do Azure Site Recovery e realize uma migração de armazenamento para um grupo de replicação. Após a conclusão, o VMM e a Recuperação de Site do Azure detectam a nova máquina virtual e começam a gerenciá-la na Recuperação de Site do Azure para proteção. Uma máquina virtual de sombra é criada no site secundário e a capacidade alocada para que a máquina virtual de réplica possa ser iniciada após o failover.

	![Habilitar proteção](./media/site-recovery-vmm-san/enable-protect.png)

Depois que as máquinas virtuais forem habilitadas para proteção, elas aparecem no console de Recuperação de Site do Azure. Você pode exibir propriedades da máquina virtual, acompanhar o status e o failover de grupos de replicação que contêm várias máquinas virtuais. Observe que na replicação de SAN, todas as máquinas virtuais associadas a um grupo de replicação devem fazer failover juntas. Isso ocorre porque o failover ocorre na camada de armazenamento pela primeira vez. É importante agrupar seus grupos de replicação corretamente e colocar apenas as máquinas virtuais juntas.

>[AZURE.NOTE] Depois de habilitar a replicação para uma máquina, você não deve adicionar VHDs para ela para LUNs que não estejam localizadas em um grupo de replicação de Recuperação de Site. Caso faça isso, eles não serão detectados pela Recuperação de Site.

Pode-se acompanhar o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover.

![Trabalho de proteção da máquina virtual](./media/site-recovery-vmm-san/job-props.png)

## Etapa 8: testar a implantação

Teste a implantação para garantir que as máquinas virtuais e os dados executem o failover conforme o esperado. Para fazer isso, você criará um plano de recuperação selecionando grupos de replicação. Em seguida, execute um failover de teste no plano.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação. Selecione **SAN** para exibir somente as nuvens que estão configuradas para replicação de SAN.
3.
	![Criar plano de recuperação](./media/site-recovery-vmm-san/r-plan.png)

4. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo padrão do plano de recuperação — Grupo 1. Você pode adicionar mais grupos se necessário. Observe que, depois da replicação, as máquinas virtuais serão inicializadas de acordo com a ordem dos grupos de plano de recuperação.

	![Adicionar máquinas virtuais](./media/site-recovery-vmm-san/r-plan-vm.png)
5. Depois que um plano de recuperação tiver sido criado, ele aparecerá na lista da guia **Planos de Recuperação**.
6. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
7. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que com essa opção habilitada a falha em máquinas virtuais de réplica não será conectada a nenhuma rede. Isso irá testar se a máquina virtual falha conforme o esperado, mas não testa seu ambiente de rede de replicação. Veja como [executar um failover de teste](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes sobre como usar diferentes opções de rede.


	![Selecionar rede de teste](./media/site-recovery-vmm-san/test-fail1.png)

8. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.
9. Após a replicação a máquina virtual de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, em seguida, será atualizado automaticamente. Se você não estiver usando o DHCP e quiser verificar se os endereços são os mesmos, você precisará executar alguns scripts.
10. Execute este script de exemplo para recuperar o endereço IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Próximas etapas

Depois de executar um failover de teste para verificar se o seu ambiente está funcionando conforme o esperado, [saiba mais sobre](site-recovery-failover.md) os diferentes tipos de failover.

<!---HONumber=AcomDC_0330_2016-->