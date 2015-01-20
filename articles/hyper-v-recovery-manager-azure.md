<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introdução à Recuperação de Site do Azure: Local para proteção do Azure com replicação Hyper-V" metaKeywords ="Recuperação de Site do Azure, o VMM, nuvens, recuperação de desastres" description="A Recuperação de Site do Azure coordena a replicação, o failover e a recuperação de máquinas virtuais Hyper-V localizadas em nuvens VMM locais para o Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises VMM Site to Azure protection with Hyper-V Replication
" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Introdução à Recuperação de Site do Azure: Site do VMM no local para proteção do Azure com replicação do Hyper-V



<div class="dev-callout"> 

<p>A Recuperação de Site do Azure colabora com sua estratégia de continuidade de negócios e carga de trabalho ao gerenciar replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação.<p>

<P>Este tutorial descreve como implantar a Recuperação de Site do Azure para orquestrar a proteção entre um site do VMM no local e o Azure usando a replicação do Hyper-V.  O tutorial usa o caminho de implantação e as configurações padrões mais rápidas, sempre que possível.</P>

<UL>
<LI>Se você quiser obter mais informações para uma implantação completa leia os guias de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planejamento</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=402679">Implantação</a>.</LI>
<LI>Você pode ler sobre cenários adicionais de implantação de recuperação de Site do Azure em <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Visão geral de recuperação do site do Azure</a>.</LI>
<LI>Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Recuperação de Site do Azure: Cenários comuns de erro e resoluções</a> ou publique suas perguntas no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum dos Serviços de Recuperação do Azure</a>.</LI>
</UL>
</div>


<h2><a id="before"></a>Pré-requisitos</h2> 
<div class="dev-callout"> 
<P>Verifique se tudo está certo antes de iniciar o tutorial.</P>

<UL>
<LI><b>Conta do Azure</b>-Você precisará de uma conta do Azure. Se você não tem uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obtenha informações de preço em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de Preços do Gerenciador de Recuperação de Site do Azure</a>.</LI>

<LI><b>Conta de armazenamento do Azure</b> - Você precisará de uma conta de armazenamento do Azure para armazenar dados replicados para o Azure. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o serviço de Recuperação de Site do Azure e ser associada à mesma assinatura. Para aprender mais sobre configuração do armazenamento do Azure, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introdução ao Armazenamento do Microsoft Azure</a>.</LI><LI><b>Servidor VMM</b> - Um servidor VMM executando no System Center 2012 R2.</LI>
<LI><b>Nuvens VMM</b> - Pelo menos uma nuvem no servidor VMM. A nuvem deve conter:
	<UL>
	<LI>Um ou mais grupos de hosts do VMM</LI>
	<LI>Um ou mais servidores de host do Hyper-V ou clusters em cada grupo de host.</LI>
	<li>Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem. As máquinas virtuais devem ser de geração 1.</li>
		</UL></LI>	
<LI><b>Máquina virtual</b>-Você precisará de máquinas virtuais que cumpram os requisitos do Azure. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Pré-requisitos e suporte</a> no guia Planejamento.</LI>
<LI>Para obter uma lista completa de requisitos de suporte da máquina virtual para failover para Azure, leia  </LI>
</UL>


<h2><a id="tutorial"></a>Etapas do tutorial</h2> 

Depois de verificar os pré-requisitos, faça o seguinte:
<UL>

<LI><a href="#vault">Etapa 1: Criar um cofre</a>-Crie um cofre de Recuperação de Site do Azure.</LI>
<LI><a href="#download">Etapa 2: Instalar o provedor em servidores do VMM</a>- Gere uma chave de registro no cofre e baixe o arquivo de configuração do provedor. Execute a instalação do servidor VMM para instalar o Provedor e registrar o servidor do VMM no cofre.</LI>
<LI><a href="#storage">Etapa 3: Adicionar uma conta de armazenamento Azure</a>-Se você não tiver uma conta, crie uma. </LI>
<LI><a href="#agent">Etapa 4: Instalar o aplicativo do agente</a>- Instale o agente de Serviços de Recuperação do Microsoft Azure em cada host Hyper-V localizado em nuvens do VMM que deseja proteger.</LI>
<LI><a href="#clouds">Etapa 5: Configurar a proteção da nuvem</a>-Defina as configurações de proteção para as nuvens VMM.</LI>
<LI><a href="#NetworkMapping">Etapa 6: Configurar o mapeamento de rede</a>-Você pode configurar opcionalmente o mapeamento de rede para mapear redes VM de origem para as redes Azure de destino.</LI>
<LI><a href="#virtualmachines">Etapa 7: Habilitar proteção para máquinas virtuais</a>-Habilitar proteção para máquinas virtuais localizadas em nuvens VMM protegidas.</LI>
<LI><a href="#test">Etapa 8: Teste a implantação</a>- Você pode executar um failover de teste para uma única máquina virtual, ou você pode criar um plano de recuperação e executar um teste de failover para o plano para verificar se ele está funcionando.</LI>
</UL>



<a name="vault"></a> <h2>Etapa 1: Criar um cofre</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda os <b>Serviços de Dados</b>, expanda os <b>Serviços de Recuperação</b> e clique em <b>Cofre de Recuperação de Site</b>.

3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	

4. Em <b>Nome</b>, digite um nome amigável para identificar o cofre.

5. Em <b>Região</b>, selecione a região geográfica para o cofre. As regiões geográficas disponíveis incluem Oeste da Europa, Sudeste da Ásia, Ásia Oriental, Norte da Europa, Oeste dos EUA, Leste dos EUA.
6. Clique em <b>Criar cofre</b>.  

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página de Serviços de Recuperação.</P>





 <a name="download"></a> <h2>Etapa 2: Gerar uma chave de registro e instalar o Provedor de Recuperação de Site do Azure</h2>
 

1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre um site do Hyper-V local e o Microsoft Azure**.
3. Em **Preparar Servidores VMM**, clique no arquivo **Gerar chave de registro**. A chave é válida para 5 dias após ser gerada. Copie o arquivo para o servidor VMM. Você precisará dele quando for configurar o Provedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. Na página <b>Início Rápido</b>, em **Preparar servidores VMM**, clique <b>Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM</b> para obter a versão mais recente do arquivo de instalação do Provedor.

2. Execute esse arquivo no servidor VMM de origem.


3. Na **Verificação dos Pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Em **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

5. Em **Conexão de Internet**, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione <b>Usar configurações de proxy padrão do sistema</b> para usar as configurações de conexão com a Internet definidas no servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Em **Chave de Registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
7. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM.


9. Em **Criptografia de Dados**, você especifica um local para salvar um certificado SSL automaticamente gerado para criptografia de dados. Esse certificado é usado se você habilitar a criptografia de dados para uma nuvem protegida pelo Azure no portal de Recuperação de Site do Azure. Mantenha esse certificado protegido. Quando você executa um failover para o Azure, selecione-o para decodificar dados criptografados.  
Essa opção não é relevante se você estiver replicando de um site local para outro.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Clique em <b>Registrar</b> para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido no final da guia <b>Recursos</b> da página **Servidores** no cofre.

<h2><a id="storage"></a>Etapa 3: Criar uma conta de armazenamento do Azure</h2>
Se não tiver uma conta de armazenamento do Azure, clique em **Adicionar uma conta de armazenamento do Azure**. A conta deve ter replicação geográfica habilitada. Ela deve estar localizada na mesma região que o serviço de Recuperação de Site do Azure e ser associada à mesma assinatura.

<P>Use este tutorial para configurar uma verificação de conceito rápida para Recuperação de Site do Azure no local para implantação do Azure. Ele usa o caminho rápido e configurações padrão onde possível. Você criará um cofre de Recuperação de Site do Azure, instalará o Provedor de Recuperação de Site do Azure no servidor VMM de origem, instalará o Agente de Serviços de Recuperação do Azure nos servidores host do Hyper-V nas nuvens VMM, definirá as configurações de proteção de nuvem, habilitará a proteção para máquinas virtuais e testará sua implantação.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h2><a id="agent"></a>Etapa 4: Instalar o Agente de Serviços de Recuperação do Azure em hosts Hyper-V</h2>

Instale o Agente de Serviços de Recuperação do Azure em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

1. Na página Início rápido, clique em <b>Baixar o Agente de Serviços de Recuperação de Site do Azure e instalar nos hosts</b> para obter a versão mais recente do arquivo de instalação do agente.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. Execute o arquivo de instalação em cada servidor host Hyper-V localizado nas nuvens VMM que você deseja proteger.
3. Na página **Verificação de pré-requisitos**, clique em <b>Avançar</b>. Quaisquer pré-requisitos faltantes serão instalados automaticamente.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. Na página **Configurações de Instalação**, especifique onde você deseja instalar o Agente e selecione a localização do cachê no qual os metadados de backup serão instalados. Em seguida, clique em <b>Instalar</b>.


<h2><a id="clouds"></a>Etapa 5: Definir as configurações da proteção de nuvem</h2>

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia <b>Itens Protegidos</b> no cofre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, clique na nuvem que você deseja configurar e vá até a guia **Configuração**.
3. Em <b>Destino</b>, selecione <b>Microsoft Azure</b>.
4. Em <b>Conta de Armazenamento</b>, selecione o armazenamento do Azure que você deseja utilizar para armazenar máquinas virtuais do Azure.
5. Defina <b>Criptografar dados armazenados</b> como <b>Desligado</b>. Essa configuração especifica que os dados podem ser criptografados e replicados entre o site local e o Azure.
6. Em <b>Copiar frequência</b>, deixe a configuração padrão. Esse valor especifica a frequência com que dados devem ser sincronizados entre os locais de origem e de destino.
7. Em <b>Manter pontos de recuperação para</b>, mantenha a configuração padrão. Com um valor padrão de zero apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
8. Em <b>Frequência dos instantâneos consistentes de aplicativo</b>, mantenha a configuração padrão. Esse valor especifica a frequência de criação de instantâneos. Os instantâneos usam o Serviço de cópias de sombra de volume (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido.  Se você definir um valor, certifique-se de que ele seja menor que o número dos pontos de recuperação adicionais que você configurar.
9. Em <b>Hora de início para replicação</b>, especifique quando a replicação inicial dos dados para o Azure deve começar. O fuso horário do servidor de host Hyper-V será usado. Recomendamos que você agende a replicação inicial fora dos horários de pico.  

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia <b>Trabalhos</b>. Todos os servidores do host do Hyper-V na nuvem de origem do VMM serão configurados para replicação.

Após o salvamento, as configurações de nuvem podem ser modificadas a partir da guia <b>Configurar</b>. Para modificar o local de destino ou armazenamento de destino, você deve remover a configuração de nuvem e reconfigurar a nuvem. Observe que, se você mudar a conta de armazenamento, a mudança só será aplicada para máquinas virtuais habilitadas para proteção, após a conta de armazenamento ter sido modificada. Máquinas virtuais existentes não são migradas para a nova conta de armazenamento.</p>

<h2><a id="networkmapping"></a>Etapa 6: Configurar o mapeamento de rede</h2>

<p>Este tutorial descreve o caminho mais simples para implantar a Recuperação de Site do Azure em um ambiente de teste. Se você deseja configurar o mapeamento de rede como parte deste tutorial, leia <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Preparar para mapeamento de rede</a> no Guia de Planejamento. Para configurar o mapeamento, siga estas etapas para <a href="http://go.microsoft.com/fwlink/?LinkId=402533">Configurar mapeamento de rede</a> no guia de implantação.</p>




<h2><a id="virtualmachines"></a>Etapa 7: Habilitar a proteção para máquinas virtuais</h2>

Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem. Observe o seguinte:

- As máquinas virtuais devem cumprir os requisitos do Azure. Consulte-os em <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Pré-requisitos e suporte</a> na guia Planejamento.
- Para habilitar a proteção, o sistema operacional e as propriedades do disco do sistema operacional devem estar definidos para as máquinas virtuais. Ao criar uma máquina virtual no VMM usando um modelo de máquina virtual, é possível definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** nas propriedades da máquina virtual. Se você não definir essas propriedades no VMM, poderá configurá-las no portal de Recuperação de Site do Azure.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. Para habilitar a proteção, na guia <b>Máquinas Virtuais</b>, na nuvem na qual a máquina virtual está localizada, clique em <b>Habilitar proteção</b> e, então, selecione <b>Adicionar máquinas virtuais</b>
2. A partir da lista de máquinas virtuais na nuvem, selecione uma que você quer proteger.  

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. Verifique as propriedades da máquina virtual e modifique-as conforme o necessário.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

Acompanhe o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover. Após a proteção estar habilitada e as máquinas virtuais serem replicadas, você será capaz de visualizá-los no Azure.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="test"></a>Etapa 8: Testar a implantação</h2>
Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano.  O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe o seguinte:
<UL>
<li>Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover.</li>
<li>Após o failover, você usará um endereço IP público para conectar-se à máquina virtual no Azure usando a Área de trabalho remota. Se você quiser fazer isso, certifique-se de não ter nenhuma política de domínio que impeça que você se conecte a uma máquina virtual usando um endereço público.</li>
</UL>

1. Na guia **Planos de Recuperação**, adicione um novo plano. Especifique um nome, **VMM** em **Tipo de fonte de**e o servidor do VMM de origem em **Fonte**, o destino será Azure.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que um failover de teste com essa configuração irá verificar se a máquina virtual replicou corretamente no Azure, porém não verifica a configuração da rede de replicação. Se você quiser executar o teste com uma rede especificada do Azure, consulte href="http://go.microsoft.com/fwlink/?LinkId=522292">Testar um local para a implantação do Azure</a>.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. Quando o failover atinge a fase **Teste completo** fase, clique em **Teste completo** para concluir o failover de teste. Você pode fazer uma busca detalhada na guia **Trabalho** para acompanhar o progresso e o status do failover e para realizar as ações necessárias.
4. Após o failover estiver concluído, faça o seguinte:
	- Verifique se as máquinas virtuais foram iniciadas com êxito
	- Clique em **Observações** para gravar e salvar observações associadas com o teste de failover.
	- Clique em **Failover de teste concluído**. Limpe o ambiente de teste para desligar automaticamente a máquina virtual de teste e exclua o teste de rede do Azure.
5. Após o failover você poderá ver a réplica de teste da máquina virtual no portal do Azure. Se estiver configurado para máquinas virtuais de acesso da rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.


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
