<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Introdução à Recuperação de Site do Azure: No local para proteção no local


Use a Recuperação de Site do Azure para orquestrar proteção para máquinas virtuais nos servidores host Hyper-V no local nas nuvens VMM. Você pode configurar:

-   **No local para proteção no local** - Replique máquinas virtuais no local para outro site no local. Você configura e habilita configurações de proteção nos cofres de Recuperação de Site do Azure. As máquinas virtuais replicam de um servidor Hyper-V no local para outro. Saiba sobre este cenário em [Introdução à Recuperação de Site do Azure: No local para proteção no local][Introdução à Recuperação de Site do Azure: No local para proteção no local].
-   **No local para proteção no local** - Replique máquinas virtuais no local para o Azure. Você configura e habilita configurações de proteção nos cofres de Recuperação de Site do Azure. As máquinas virtuais replicam de um servidor Hyper-V no local para o armazenamento do Azure. Saiba sobre este cenário em [Introdução à Recuperação de Site do Azure: No local para proteção do Azure][Introdução à Recuperação de Site do Azure: No local para proteção do Azure]

## <span id="about"></span></a>Sobre este tutorial

Use este tutorial para configurar uma verificação de conceito rápida para Recuperação de Site do Azure no local para implantação do Azure. Ele usa o caminho rápido e configurações padrão onde possível. Você criará um cofre de Recuperação de Site do Azure, instalará o Provedor de Recuperação de Site do Azure no servidor VMM de origem, configurará as configurações de proteção de nuvem, habilitará a proteção para máquinas virtuais e testará sua implantação.

Se você quer informações sobre uma implantação completa, consulte:

-   [Planejar para a Implantação de Recuperação de Site do Azure][Planejar para a Implantação de Recuperação de Site do Azure]- Descreve as etapas de planejamento que devem ser concluídas antes de iniciar uma implantação completa.
-   [Implantação da Recuperação de Site do Azure: No local para proteção no local][Implantação da Recuperação de Site do Azure: No local para proteção no local] - Fornece instruções passo a passo para uma implantação completa.

Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de [Recuperação de Site do Azure: Cenários comuns de erro e resoluções][Recuperação de Site do Azure: Cenários comuns de erro e resoluções] ou poste suas perguntas no [Fórum dos Serviços de Recuperação do Azure][Fórum dos Serviços de Recuperação do Azure].

</div>

## <span id="before"></span></a>Antes de começar


Antes de iniciar este tutorial, verifique os pré-requisitos.

### <span id="HVRMPrereq"></span></a>Pré-requisitos

-   **Conta do Azure**- Você precisará de uma conta do Azure. Se você não tem, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure]. Obter informações de preço em [Detalhes de Preços do Gerenciador de Recuperação de Site do Azure][Detalhes de Preços do Gerenciador de Recuperação de Site do Azure].
-   **Servidor do VMM** - Você precisará de pelo menos um servidor VMM executando no System Center 2012 SP1 ou o System Center 2012 R2.
-   **Nuvem VMM** - Você precisará de pelo menos uma nuvem no servidor do VMM de origem que você deseja proteger e uma nuvem no servidor VMM de destino. Se você estiver executando um servidor VMM, ele precisará de duas nuvens. A nuvem principal que você deseja proteger deve conter o seguinte:
    -   Um ou mais grupos de hosts do VMM
    -   Um ou mais servidores de host do Hyper-V ou clusters em cada grupo de host.
    -   Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem.

## <span id="tutorial"></span></a>Etapas do tutoriais

Depois de verificar os pré-requisitos, faça o seguinte:

-   [Etapa 1: Criar um cofre][Etapa 1: Criar um cofre] - Criar um cofre de Recuperação do Site do Azure.
-   [Etapa 2: Instalar o aplicativo do Provedor][Etapa 2: Instalar o aplicativo do Provedor] - Gerar uma chave de registro, e executa o aplicativo do Provedor de Recuperação do Site do Microsoft Azure no servidor VMM. Isso instala o Provedor e registra o servidor VMM no cofre.
-   [Etapa 3: Configurar a proteção de nuvem][Etapa 3: Configurar a proteção de nuvem] - Configurar as configurações de proteção para nuvem VMM.
-   <a href="#networkmapping">Etapa 5: Configurar mapeamento de rede - você pode configurar opcionalmente o mapeamento de rede para mapear as redes VM de origem para redes VM de destino.
-   [Etapa 6: Configurar o mapeamento de armazenamento][Etapa 6: Configurar o mapeamento de armazenamento] - Você pode configurar opcionalmente o mapeamento de armazenamento para mapear classificações de armazenamento no servidor VMM de origem para classificações de armazenamento no servidor de destino.
-   [Etapa 7: Ativar a proteção para máquinas virtuais][Etapa 7: Ativar a proteção para máquinas virtuais] - Ativa a proteção para máquinas virtuais do Hyper-V localizadas nas nuvens do VMM.
-   [Etapa 8: Configurar e executar planos de recuperação][Etapa 8: Configurar e executar planos de recuperação] - Criar um plano de recuperação e executar um failover de teste para o plano.

<a name="vault"></a>

## Etapa 1: Criar um cofre

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Expanda os **Serviços de dados**, expanda os **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.

3.  Clique em **Criar Novo** e, em seguida, clique em **Criação Rápida**.

4.  Em **Nome**, digite um nome amigável para identificar o cofre.

5.  Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure][Detalhes dos Preços de Recuperação de Site do Azure]

6.  Clique em **Criar cofre**.

    ![Novo cofre][Novo cofre]

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de Serviços de Recuperação.

<a name="upload"></a>

## Etapa 2: Configurar o cofre

1.  Na página **Serviços de Recuperação**, clique no cofre para abrir na página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

    ![Ícone de Inicialização Rápida][Ícone de Inicialização Rápida]

2.  Na lista suspensa, selecione **Entre dois sites Hyper-V no local**.
3.  Em **Preparar Servidores VMM**, clique no arquivo **Gerarchave de registro**. A chave é válida para 5 dias após ser gerada. Copie o arquivo para o servidor VMM. Você precisará dele quando for configurar o Provedor.

    ![Chave de Registro][Chave de Registro]

<a name="download"></a>

## Etapa 3: Instalar o Provedor de Recuperação do Site do Azure

</p>
1.  Na página **Início Rápido**, em **Preparar servidores VMM**, clique **Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM** para obter a versão mais recente do arquivo de instalação do Provedor.

2.  Execute esse arquivo nos servidores VMM de início e de destino.

3.  Na **Verificação dos pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza.

    ![Pré-requisitos][Pré-requisitos]

4.  No **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilidado, a atualização será instaada de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft][Atualizações da Microsoft]

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

1.  Na página Conexão com a Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione **Usar configurações do proxy do sistema padrão** para usar as configurações de conexão à Internet padrão configuradas no servidor.

    ![Configurações da Internet][Configurações da Internet]

2.  Em **Chave de registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
3.  Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.
4.  Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

    ![Registros do servidor][Registros do servidor]

5.  Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nivem no consolte VMM.

6.  Em **Criptografia de dados**, você gera um certificado que é usado para criptografar dados protegidos no Azure.
    Essa opção não é relevante, se você está replicando a partir de um site no local para outro.

    ![Registros do servidor][1]

7.  Clique em **Registrar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido no final da guia **Recursos** da página **Servidores** no cofre.

## <span id="clouds"></span></a>Etapa 4: Definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habiitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia **Itens Protegidos** no cofre.

![Publicar uma nuvem][Publicar uma nuvem]

1.  Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2.  Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. Observe que:
3.  Em **Destino**, selecione **VMM**.
4.  Em **Local de Destino**, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para a recuperação.
5.  Em **Nuvem de Destino**, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:

    -   É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para máquinas virtuais que você protegerá.
    -   Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.

6.  Em **Copiar frequência**, deixe a configuração padrão. Esse valor especifica a frequência com que dados devem ser sincronizados entre os locais de origem e de destino. Só é relevante quando o host do Hyper-V estiver executando o Windows Server 2012 R2. Para outros servidores, é usada uma configuração padrão de cinco minutos.
7.  Em **Pontos de recuperação adicionais**, deixe a configuração padrão. Este valor especifica se você quer criar pontos de recuperação adicionais. O valor padrão de zero especifica que apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
8.  Em **Frequência dos instantâneos consistentes de aplicativo**, mantenha a configuração padrão. Esse valor especifica a frequência de criação de snapshots. Os snapshots usam o Volume Shadow Copy Service (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é obtido. Se você deseja definir esse valor para o tutorial passo a passo, certifique-se de que ele está definido para menos do que o número de pontos de recuperação adicional que você configurar.
    ![Configurar configurações de proteção][Configurar configurações de proteção]
9.  Em **Compactação de Transferência de Dados**, especifique se os dados replicados transferidos devem ser compactados.
10. Em **Autenticação**, especifique como o tráfego é autenticado entre os servidores de host do Hyper-V primário e de recuperação. Para o propósito deste passo a passo, selecione HTTPS, a menos que você tenha um ambiente Kerberos configurado em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Nenhuma configuração manual é necessária. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
11. Em **Porta**, deixe a configuração padrão. Esse valor define o número da porta em que os computadores de origem e destino do host Hyper-V ouvem o tráfego de duplicação.
12. Em **Método de replicação**, especifique como a replicação inicial de dados de locais de origem para destino será tratada, antes de iniciar a replicação normal.

    -   **Pela rede**- Copiando dados pela rede pode ser demorado e consumir muitos recursos. Recomendamos usar essa opção se a nuvem contém máquinas virtuais com discos rígidos virtuais relativamente pequenos e se o servidor VMM primário está conectado ao servidor VMM secundário através de uma conexão com banda larga ampla. Você pode especificar que a cópia deve iniciar imediatamente ou selecionar uma hora. Se você usar a replicação de rede, recomendamos que você agende fora dos horários de pico.
    -   **Offline**- Esse método especifica que a replicação inicial será executada usando mídia externa. É útil se você deseja evitar a degradação no desempenho da rede ou para locais geograficamente remotos. Para usar esse método, você especifica o local de exportação na nuvem de origem e o local de importação na nuvem de destino. Ao ativar a proteção de uma máquina virtual, o disco rígido virtual é copiado para o local de exportação especificado. Você envia para o local de destino e copia para o local de importação. O sistema copia as informações importadas para as máquinas virtuais de réplica. Para uma lista completa dos pré-requisitos da replicação offline, consulte [Etapa 3: Definir configurações de proteção para nuvens VMM][Etapa 3: Definir configurações de proteção para nuvens VMM] no Guia de implantação.

13. Selecione **Excluir Máquina Virtual de Réplica** para especificar que a máquina virtual de réplica deve ser excluída se você parar de proteger a máquina virtual ao selecionar a opção **Excluir proteção para a máquina virtual** na guia Máquinas Virtuais das propriedades da nuvem. Com esta configuração habilitada, quando você desabilita a proteção, a máquina virtual é removida da Recuperação de Site do Azure, as configurações da Recuperação de Site para a máquina virtual são removidas no console VMM, e a réplica é excluída.
    ![Configurar as configurações de proteção][Configurar as configurações de proteção]

Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia **Trabalhos**. Todos os servidores do host do Hyper-V na nuvem de origem do VMM serão configurados para replicação. As configurações de nuvem podem ser modificadas na guia **Configurar**. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.

## <span id="networkmapping"></span></a>Etapa 5: Configurar o mapeamento de rede

Este tutorial descreve o caminho mais simples para implantar a Recuperação de Site do Azure em um ambiente de teste. Se você deseja configurar o mapeamento de rede como parte deste tutorial, leia [Preparar para mapeamento de rede][Preparar para mapeamento de rede] no Guia de Planejamento. Para configurar o mapeamento, siga estas etapas para [Configurar mapeamento de rede][Configurar mapeamento de rede] no guia de implantação.

## <span id="storagemapping"></span></a>Etapa 6: Configurar o mapeamento de armazenamento

Este tutorial descreve o caminho mais simples para implantar a Recuperação de Site do Azure em um ambiente de teste. Se você deseja configurar o mapeamento de armazenamento como parte deste tutorial, siga as etapas para [Configurar mapeamento de armazenamento][Configurar mapeamento de armazenamento] no guia de implantação.

## <span id="enablevirtual"></span></a>Etapa 7: Habilitar a proteção da máquina virtual

Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem.

1.  Na guia **Máquinas Virtuais** na nuvem em que a máquina virtual está localizada, clique em **Habilitar proteção** e, em seguida, selecione **Adicionar máquinas virtuais**.
2.  A partir da lista de máquinas virtuais na nuvem, selecione um que você quer proteger.

![Habilitar a proteção da máquina virtual][Habilitar a proteção da máquina virtual]

Acompanhar o progresso da ação de Habilitar Proteção na guia \*\*Trabalhos\*\*, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção executar a máquina virtual está pronta para failover. Após a proteção estar habilitada e as máquinas virtuais serem replicadas, você será capaz de visualizá-los no Azure.

![Trabalho de proteção da máquina virtual][Trabalho de proteção da máquina virtual]

## <span id="recoveryplans"></span></a>Etapa 8: Teste a implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

-   Para obter instruções sobre a criação de um plano de recuperação, consulte [Criar e personalizar planos de recuperação: No local para Azure][Criar e personalizar planos de recuperação: No local para Azure].
-   Para obter instruções sobre a execução de um failover de teste, consulte [Testar um local para implantação do local][Testar um local para implantação do local].

### <span id="runtest"></span></a>Monitorar atividade

Você pode usar a guia **Trabalhos** e **Painel** para exibir e monitorar os principais trabalhos executados pelo cofre de Recuperação de Site do Azure, incluindo a configuração da proteção para uma nuvem; habilitação e desabilitação da proteção para uma máquina virtual; execução de um failover (planejado, não planejado ou teste) e confirmação de um failover não planejado.

A partir da guia **Trabalhos** você exibe os trabalhos, realiza busca detalhada nos detalhes do trabalho e erros, executa consultas de trabalho para recuperar trabalhos que combinam com os critérios específicos, exporta trabalhos para o Excel e reinicia os trabalhos falhos.

A partir de **Painel**, você pode baixar as últimas versões dos arquivos de instalação do Provedor e do Agente, obtenha informações de configuração para o cofre, consulte o número de máquinas virtuais que tem a proteção gerenciada pelo cofre, veja os trabalhos recentes, grencie o certificado do cofre e sincronize novamente as máquinas virtuais.

Para obter mais informações sobre a interação com trabalhos e o painel, consulte o [Guia de Operações e Monitoramento][Guia de Operações e Monitoramento].

## <span id="next"></span></a>Próximas etapas

-   Para planejar e implantar a Recuperação de Site do Azure em um ambiente de produção completo, consulte [Guia de planejamento para a Recuperação do Hyper-V][Planejar para a Implantação de Recuperação de Site do Azure] e [Guia de implantação de Recuperação de Site do Azure][Implantação da Recuperação de Site do Azure: No local para proteção no local].
-   Para dúvidas, visite o [Fórum de serviços de recuperação do Azure][Fórum dos Serviços de Recuperação do Azure].

</div>

  [Introdução à Recuperação de Site do Azure: No local para proteção no local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introdução à Recuperação de Site do Azure: No local para proteção do Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Planejar para a Implantação de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implantação da Recuperação de Site do Azure: No local para proteção no local]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Recuperação de Site do Azure: Cenários comuns de erro e resoluções]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Fórum dos Serviços de Recuperação do Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Avaliação gratuita do Azure]: http://aka.ms/try-azure
  [Detalhes de Preços do Gerenciador de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Etapa 1: Criar um cofre]: #vault
  [Etapa 2: Instalar o aplicativo do Provedor]: #download
  [Etapa 3: Configurar a proteção de nuvem]: #clouds
  [Etapa 6: Configurar o mapeamento de armazenamento]: #storagemapping
  [Etapa 7: Ativar a proteção para máquinas virtuais]: #enablevirtual
  [Etapa 8: Configurar e executar planos de recuperação]: #recovery%20plans
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Detalhes dos Preços de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Novo cofre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Ícone de Inicialização Rápida]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Chave de Registro]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Pré-requisitos]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Atualizações da Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Configurações da Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registros do servidor]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Publicar uma nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configurar configurações de proteção]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Etapa 3: Definir configurações de proteção para nuvens VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [Configurar as configurações de proteção]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Preparar para mapeamento de rede]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurar mapeamento de rede]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Configurar mapeamento de armazenamento]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Habilitar a proteção da máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Trabalho de proteção da máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Criar e personalizar planos de recuperação: No local para Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testar um local para implantação do local]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Guia de Operações e Monitoramento]: http://go.microsoft.com/fwlink/?LinkId=398534
