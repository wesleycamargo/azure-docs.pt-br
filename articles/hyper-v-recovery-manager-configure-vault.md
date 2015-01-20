<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introdução à Recuperação de Site do Azure: Local para proteção de site local do VMM com a replicação do Hyper-V" metaKeywords =" Recuperação de Site do Azure, VMM, nuvens, recuperação de desastres" description="A recuperação do site do Azure coordena a replicação, o failover e a recuperação das máquinas virtuais do Hyper-V entre os sites VMM no local." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery:  On-Premises to On-Premises VMM Site Protection with Hyper-V Replication" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Introdução à Recuperação de Site do Azure:  Local para proteção de site local do VMM com a replicação do Hyper-V


<div class="dev-callout"> 

<p>A recuperação de Site do Azure colabora com sua estratégia de continuidade de negócios e carga de trabalho ao gerenciar replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação.<p>

<P>Este tutorial descreve como implantar a recuperação de Site do Azure para orquestrar e automatizar a proteção para cargas de trabalho em execução em um site do VMM local para outro site VMM local, usando a replicação do Hyper-V.  O tutorial usa o caminho de implantação e as configurações padrões mais rápidas, sempre que possível.</P>

<UL>
<LI>Você pode ler instruções de implantação completa dos guias de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planejamento</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Implementação</a>.</LI>
<LI>Você pode ler sobre cenários adicionais de implantação de recuperação de Site do Azure em <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Visão geral de recuperação do site do Azure</a>.</LI>
<LI>Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Recuperação de Site do Azure: Cenários comuns de erro e resoluções</a> ou publique suas perguntas no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum dos Serviços de Recuperação do Azure</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Pré-requisitos</h2> 
<div class="dev-callout"> 
<P>Verifique se tudo está certo antes de iniciar o tutorial.</P>

<UL>
<LI><b>Conta do Azure</b>-Você precisará de uma conta do Azure. Se você não tem uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obtenha informações de preço em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de Preços do Gerenciador de Recuperação de Site do Azure</a>.</LI>
<LI><b>Servidor do VMM</b> - Você precisará de pelo menos um servidor VMM executando no System Center 2012 SP1 ou o System Center 2012 R2.</LI>
<LI><b>Nuvem VMM</b> - Você precisará de pelo menos uma nuvem no servidor do VMM de origem que você deseja proteger e uma nuvem no servidor VMM de destino. Se você estiver executando um servidor VMM, ele precisará de duas nuvens. A nuvem principal que você deseja proteger deve conter o seguinte:<UL>
	<LI>Um ou mais grupos de hosts do VMM</LI>
	<LI>Um ou mais servidores de host do Hyper-V ou clusters em cada grupo de host.</LI>
	<li>Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem.</li>
		</UL></LI>
<LI>**Redes**- você também pode definir o mapeamento de rede para assegurar que as máquinas virtuais de réplica são colocadas idealmente em servidores host Hyper-V após o failover, e que elas possam se conectar às redes VM apropriadas. Quando o mapeamento de rede estiver habilitado, uma máquina virtual no local principal será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada. Se você não configurar o mapeamento de rede, as máquinas virtuais não serão conectadas às redes VM após o failover. Este tutorial descreve as configurações de instruções passo a passo mais simples e não inclui o mapeamento de rede, mas você pode ler mais em:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mapeamento de rede</a> no guia de planejamento.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522293">Habilitar o mapeamento de rede</a> no guia de implantação de SAN.</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>Etapas do tutorial</h2> 

Depois de verificar os pré-requisitos, faça o seguinte:
<UL>
<LI><a href="#vault">Etapa 1: Criar um cofre</a>-Crie um cofre de Recuperação de Site do Azure.</LI>
<LI><a href="#download">Etapa 2: Instalar o provedor em servidores do VMM</a>- Gere uma chave de registro no cofre e baixe o arquivo de configuração do provedor. Execute a instalação do servidor VMM para instalar o Provedor e registrar o servidor do VMM no cofre.</LI>
<LI><a href="#clouds">Etapa 3: Configurar a proteção da nuvem</a>-Defina as configurações de proteção para as nuvens VMM. Essas configurações de segurança são aplicadas a todas as máquinas virtuais na nuvem que podem ser habilitadas para proteção de Recuperação de Site do Azure.</LI>
<LI><a href="#storagemapping">Etapa 4: Configurar o mapeamento de armazenamento</a>- Se você quiser especificar onde são armazenados os dados de replicação, você pode configurar o mapeamento de armazenamento. Isso mapeia as classificações de armazenamento no servidor VMM de origem para classificações de armazenamento no servidor de destino.</LI>
<LI><a href="#enablevirtual">Etapa 5: Habilitar proteção para máquinas virtuais</a>-Habilite a proteção para máquinas virtuais localizadas em nuvens VMM protegidas</LI>
<LI><a href="#recovery plans">Etapa 6: Configurar e executar planos de recuperação</a>- Crie um plano de recuperação e execute um teste de failover para o plano para verificar se ele está funcionando.</LI>

</UL>




<a name="vault"></a> <h2>Etapa 1: Criar um cofre</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda os <b>Serviços de Dados</b>, expanda os <b>Serviços de Recuperação</b> e clique em <b>Cofre de Recuperação de Site</b>.


3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	
4. Em <b>Nome</b>, digite um nome amigável para identificar o cofre.

5. Em <b>Região</b>, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalhes de Preços de Recuperação de Site do Azure</a>

6. Clique em <b>Criar cofre</b>.  

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página de Serviços de Recuperação.</P>

<a name="upload"></a> <h2>Etapa 2: Configurar o cofre</h2>


1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre dois sites Hyper-V no local**.
3. Em **Preparar Servidores VMM**, clique no arquivo **Gerar chave de registro**. A chave é válida para 5 dias após ser gerada. Copie o arquivo para o servidor VMM. Você precisará dele quando for configurar o Provedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>Etapa 3: Instalar o Provedor de Recuperação do Site do Azure</h2>
	

1. Na página <b>Início Rápido</b>, em **Preparar servidores VMM**, clique <b>Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM</b> para obter a versão mais recente do arquivo de instalação do Provedor.

2. Execute esse arquivo nos servidores VMM de início e de destino.

3. Na **Verificação dos Pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza.  

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Em **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

5. Na página Conexão com a Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione <b>Usar configurações do proxy do sistema padrão</b> para usar as configurações de conexão à Internet padrão configuradas no servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Em **Chave de Registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
7. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.


7. Em **Criptografia de Dados** você gera um certificado que é usado para criptografar dados protegidos no Azure.  
Essa opção não é relevante para o cenário descrito neste tutorial.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Clique em <b>Registrar</b> para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido no final da guia <b>Recursos</b> da página **Servidores** no cofre.




<h2><a id="clouds"></a>Etapa 4: Definir as configurações da proteção de nuvem</h2>

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia <b>Itens Protegidos</b> no cofre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. Observe que:
3. Em <b>Destino</b>, selecione <b>VMM</b>.
4. Em <b>Local de destino</b>, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para a recuperação.
4. Em <b>Nuvem de destino</b>, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:
	- É recomendável selecionar uma nuvem de destino que atenda aos requisitos de recuperação para máquinas virtuais que você protegerá.
	- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.

6. Em <b>Copiar frequência</b>, deixe a configuração padrão. Esse valor especifica a frequência com que dados devem ser sincronizados entre os locais de origem e de destino. Só é relevante quando o host do Hyper-V estiver executando o Windows Server 2012 R2. Para outros servidores, é usada uma configuração padrão de cinco minutos.
7. Em <b>Pontos de recuperação adicionais</b>, deixe a configuração padrão. Este valor especifica se você quer criar pontos de recuperação adicionais. O valor padrão de zero especifica que apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
8. Em <b>Frequência dos instantâneos consistentes de aplicativo</b>, mantenha a configuração padrão. Esse valor especifica a frequência de criação de instantâneos. Os instantâneos usam o Serviço de cópias de sombra de volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido.  Se você deseja definir esse valor para o tutorial passo a passo, certifique-se de que ele está definido para menos do que o número de pontos de recuperação adicional que você configurar.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. Em <b>Compactação de Transferência de Dados</b>, especifique se os dados replicados transferidos devem ser compactados.  
10. Em <b>Autenticação</b>, especifique como o tráfego é autenticado entre os servidores de host do Hyper-V primário e de recuperação. Para o propósito deste passo a passo, selecione HTTPS, a menos que você tenha um ambiente Kerberos configurado em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Nenhuma configuração manual é necessária. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
11. Em <b>Porta</b>, deixe a configuração padrão. Esse valor define o número da porta em que os computadores de origem e destino do host Hyper-V ouvem o tráfego de duplicação.
12. Em <b>Método de replicação</b>, especifique como a replicação inicial de dados de locais de origem para destino será tratada, antes de iniciar a replicação normal.  
	- <b>Pela rede</b>- A cópia de dados pela rede pode ser demorada e consumir muitos recursos. Recomendamos usar essa opção se a nuvem contiver máquinas virtuais com discos rígidos virtuais relativamente pequenos e se o servidor VMM primário estiver conectado ao servidor VMM secundário através de uma conexão com banda larga ampla. Você pode especificar que a cópia deve iniciar imediatamente ou selecionar uma hora. Se você usar a replicação de rede, recomendamos que você agende fora dos horários de pico.
	- <b>Offline</b>- Esse método especifica que a replicação inicial será executada usando mídia externa. É útil se você deseja evitar a degradação no desempenho da rede ou para locais geograficamente remotos. Para usar esse método, você especifica o local de exportação na nuvem de origem e o local de importação na nuvem de destino. Ao ativar a proteção de uma máquina virtual, o disco rígido virtual é copiado para o local de exportação especificado. Você envia para o local de destino e copia para o local de importação. O sistema copia as informações importadas para as máquinas virtuais de réplica. Para uma lista completa dos pré-requisitos da replicação offline, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Etapa 3: Definir configurações de proteção para nuvens VMM</a> no Guia de implantação.
13. Selecione **Excluir Máquina Virtual de Réplica** para especificar que a máquina virtual de réplica deve ser excluída se você parar de proteger a máquina virtual ao selecionar a opção **Excluir proteção para a máquina virtual** na guia Máquinas Virtuais das propriedades da nuvem. Com esta configuração habilitada, quando você desabilita a proteção, a máquina virtual é removida da Recuperação de Site do Azure, as configurações da Recuperação de Site para a máquina virtual são removidas no console VMM, e a réplica é excluída.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia <b>Trabalhos</b>. Todos os servidores do host do Hyper-V na nuvem de origem do VMM serão configurados para replicação. As configurações de nuvem podem ser modificadas na guia <b>Configurar</b>. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.</p>

<h2><a id="storagemapping"></a>Etapa 5: Configurar o mapeamento de armazenamento</h2>

<p>Este tutorial descreve o caminho mais simples para implantar a Recuperação de Site do Azure em um ambiente de teste. Se você deseja configurar o mapeamento de armazenamento como parte deste tutorial, siga as etapas para <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Configurar mapeamento de armazenamento</a> no guia de implantação.</p>


<h2><a id="enablevirtual"></a>Etapa 6: Habilitar a proteção da máquina virtual</h2>
<p>Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem.</p>
<OL>
<li>Na guia <b>Máquinas Virtuais</b> na nuvem em que a máquina virtual está localizada, clique em <b>Habilitar proteção</b> e, em seguida, selecione <b>Adicionar máquinas virtuais</b>. </li>
<li>A partir da lista de máquinas virtuais na nuvem, selecione uma que você quer proteger.</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>Acompanhe o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover. Após a proteção estar habilitada e as máquinas virtuais serem replicadas, você será capaz de visualizá-los no Azure.</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>Etapa 7: Testar a implantação</h2>

Teste a implantação para garantir que as máquinas virtuais e os dados executem o failover conforme o esperado. Para fazer isso, você criará um plano de recuperação selecionando grupos de replicação. Em seguida, execute um failover de teste no plano.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação. Selecione **Hyper-V** para exibir somente as nuvens que estão configuradas para replicação do Hyper-V.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo padrão do plano de recuperação - Grupo 1. Você pode adicionar mais grupos se necessário. Observe que, depois da replicação, as máquinas virtuais serão inicializadas de acordo com a ordem dos grupos de plano de recuperação.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. Depois que um plano de recuperação ter sido criado, ele aparece na lista da guia **Planos de Recuperação**.  
5. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
6. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que com essa opção habilitada a falha em máquinas virtuais de réplica não será conectada a nenhuma rede. Isso irá testar se a máquina virtual falha conforme o esperado, mas não testa seu ambiente de rede de replicação. Se você quiser executar um failover de teste mais abrangente, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testar uma implantação local no MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.
8. Após a replicação a máquina virtual de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, o DNS será atualizado automaticamente. Se você não estiver usando o DHCP e quiser verificar se os endereços são os mesmos, você precisará executar alguns scripts.
9. Execute este script de exemplo para recuperar o endereço IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**


<h3><a id="runtest"></a>Monitorar atividade</h3>
<p>Você pode usar a guia <b>Trabalhos</b> e <b>Painel</b> para exibir e monitorar os principais trabalhos executados pelo cofre de Recuperação de Site do Azure, incluindo a configuração da proteção para uma nuvem; habilitação e desabilitação da proteção para uma máquina virtual; execução de um failover (planejado, não planejado ou teste) e confirmação de um failover não planejado.</p>

<p>A partir da guia <b>Trabalhos</b> você exibe os trabalhos, realiza busca detalhada nos detalhes do trabalho e erros, executa consultas de trabalho para recuperar trabalhos que combinam com os critérios específicos, exporta trabalhos para o Excel e reinicia os trabalhos falhos.</p>

<p>A partir do <b>Painel</b>, você pode baixar as últimas versões dos arquivos de instalação do Provedor e do Agente, obter informações de configuração para o cofre, consultar o número de máquinas virtuais que tem a proteção gerenciada pelo cofre, ver os trabalhos recentes, gerenciar o certificado do cofre e sincronizar novamente as máquinas virtuais.</p>

<p>Para obter mais informações sobre a interação com trabalhos e o painel, consulte o <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guia de Operações e Monitoramento</a>.</p>
	
<h2><a id="next"></a>Próximas etapas</h2>
<UL>
<LI>Para planejar e implantar a Recuperação de Site do Azure em um ambiente de produção completo, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guia de planejamento para a Recuperação do Hyper-V</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guia de implantação de Recuperação de Site do Azure</a>.</LI>
<LI>Para dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de serviços de recuperação do Windows Azure</a>.</LI> 
</UL>

<!--HONumber=35.2-->
