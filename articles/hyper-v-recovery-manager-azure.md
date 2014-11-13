<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introdu&ccedil;&atilde;o &agrave; Recupera&ccedil;&atilde;o de Site do Azure: Prote&ccedil;&atilde;o no local para o Azure" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="A Recupera&ccedil;&atilde;o de Site do Azure coordena a replica&ccedil;&atilde;o, o failover e a recupera&ccedil;&atilde;o de m&aacute;quinas virtuais Hyper-V localizadas em nuvens VMM locais para o Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Introdu&ccedil;&atilde;o &agrave; Recupera&ccedil;&atilde;o de Site do Azure: Prote&ccedil;&atilde;o no local para o Azure" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introdução à Recuperação de Site do Azure: Proteção no local para o Azure

<div class="dev-callout">

Use a Recuperação de Site do Azure para orquestrar proteção para máquinas virtuais nos servidores host Hyper-V no local nas nuvens VMM. Você pode configurar:

-   **No local para proteção no local** - Replique máquinas virtuais no local para o Azure. Você configura e habilita as configurações de proteção nos cofres da Recuperação de Site do Azure. As máquinas virtuais replicam de um servidor Hyper-V no local para o armazenamento do Azure.
-   **No local para proteção no local** - Replique máquinas virtuais no local para outro site no local. Você configura e habilita as configurações de proteção nos cofres da Recuperação de Site do Azure. As máquinas virtuais replicam de um servidor Hyper-V no local para outro. Saiba sobre este cenário em [Introdução à Recuperação de Site do Azure: No local para proteção no local][Introdução à Recuperação de Site do Azure: No local para proteção no local].

## <span id="about"></span></a>Sobre este tutorial

Use este tutorial para configurar uma verificação de conceito rápida para Recuperação de Site do Azure no local para implantação do Azure. Ele usa o caminho rápido e configurações padrão onde possível. Você criará um cofre de Recuperação de Site do Azure, instalará o Provedor de Recuperação de Site do Azure no servidor VMM de origem, instalará o Agente de Serviços de Recuperação do Azure nos servidores host do Hyper-V nas nuvens VMM, definirá as configurações de proteção de nuvem, habilitará a proteção para máquinas virtuais e testará sua implantação.

Se você quer informações sobre uma implantação completa, consulte:

-   [Planejamento para o Azure Site Recovery Deployment][Planejamento para o Azure Site Recovery Deployment]—Descreve as etapas de planejamento que você precisará antes de uma partida a plena implantação.
-   [Implantação da Recuperação de Site do Azure: Proteção para o Azure no local][Implantação da Recuperação de Site do Azure: Proteção para o Azure no local]—Fornece instruções passo a passo para uma implantação completa.

Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de [Recuperação de Site do Azure: Cenários comuns de erro e resoluções][Recuperação de Site do Azure: Cenários comuns de erro e resoluções] ou poste suas perguntas no [Fórum dos Serviços de Recuperação do Azure][Fórum dos Serviços de Recuperação do Azure].

</div>

## <span id="before"></span></a>Antes de começar

<div class="dev-callout">

Antes de iniciar este tutorial, verifique os pré-requisitos.

### <span id="HVRMPrereq"></span></a>Pré-requisitos

-   **Conta do Azure**- Você precisará de uma conta do Azure. Se você não tem, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure]. Obter informações de preço em [Detalhes de Preços do Gerenciador de Recuperação de Site do Azure][Detalhes de Preços do Gerenciador de Recuperação de Site do Azure].
-   **Conta de armazenamento do Azure** - Você precisará de uma conta de armazenamento do Azure para armazenar dados replicados para o Azure. A conta precisa estar com a georreplicação habilitada. Ela deve estar localizada na mesma região que o serviço de Recuperação de Site do Azure e ser associada à mesma assinatura. Para aprender mais sobre configuração do armazenamento do Azure, consulte [Introdução ao Armazenamento do Microsoft Azure][Introdução ao Armazenamento do Microsoft Azure].
-   **Servidor VMM** - Um servidor VMM executando no System Center 2012 R2.
-   **Nuvens VMM** - Pelo menos uma nuvem no servidor VMM. A nuvem deve conter:
    -   Um ou mais grupos de hosts do VMM
    -   Um ou mais servidores de host do Hyper-V ou clusters em cada grupo de host.
    -   Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem. As máquinas virtuais devem ser de geração 1.
-   **Máquina virtual**—Você precisará de máquinas virtuais que cumpram os requisitos do Azure. Consulte [Pré-requisitos e suporte][Pré-requisitos e suporte] no guia Planejamento.
-   Para obter uma lista completa de requisitos de suporte da máquina virtual para failover para Azure, leia

## <span id="tutorial"></span></a>Etapas do tutoriais

Depois de verificar os pré-requisitos, faça o seguinte:

-   [Etapa 1: Criar um cofre][Etapa 1: Criar um cofre]—Criar um cofre do Azure Site Recovery.
-   [Etapa 2: Instalar o aplicativo Provider][Etapa 2: Instalar o aplicativo Provider]—Gerar uma chave de registro e executar o aplicativo Microsoft Azure Site Recovery Provider no servidor VMM. Isso instala o Provedor e registra o servidor VMM no cofre.
-   [Etapa 3: Adicionar uma conta de armazenamento Azure][Etapa 3: Adicionar uma conta de armazenamento Azure]—Se você não tiver uma conta, crie uma.
-   [Etapa 4: Instalar o aplicativo Agent][Etapa 4: Instalar o aplicativo Agent]—Instale o agente Microsoft Azure Recovery Services em cada host do Hyper-V
-   [Etapa 5: Configurar a proteção da nuvem][Etapa 5: Configurar a proteção da nuvem]—Defina as configurações de proteção para as nuvens VMM.
-   [Etapa 6: Configurar o mapeamento de rede][Etapa 6: Configurar o mapeamento de rede]—Você pode configurar opcionalmente o mapeamento de rede para mapear redes VM de origem para as redes Azure de destino.
-   [Etapa 7: Habilitar proteção para máquinas virtuais][Etapa 7: Habilitar proteção para máquinas virtuais]—Habilitar proteção para máquinas virtuais localizadas em nuvens VMM protegidas.
-   [Etapa 8: Testar a implantação][Etapa 8: Testar a implantação]—Você pode executar um failover de teste para uma única máquina virtual ou pode criar um plano de recuperação e executar um failover de teste para o plano.

<a name="vault"></a>

## Etapa 1: Criar um cofre

</p>
1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Expanda os **Serviços de dados**, expanda os **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.

3.  Clique em **Criar Novo** e, em seguida, clique em **Criação Rápida**.

4.  Em **Nome**, digite um nome amigável para identificar o cofre.

5.  Em **Região**, selecione a região geográfica para o cofre. As regiões geográficas disponíveis incluem Oeste da Europa, Sudeste da Ásia, Ásia Oriental, Norte da Europa, Oeste dos EUA, Leste dos EUA.
6.  Clique em **Criar cofre**.

    ![Novo cofre][Novo cofre]

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de Serviços de Recuperação.

<a name="download"></a>

## Etapa 2: Gerar uma chave de registro e instalar o Provedor de Recuperação de Site do Azure

</p>
1.  Na página **Serviços de Recuperação**, clique no cofre para abrir na página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

    ![Ícone de Inicialização Rápida][Ícone de Inicialização Rápida]

2.  Na lista suspensa, selecione **Entre um site do Hyper-V local e o Microsoft Azure**.
3.  Em **Preparar Servidores VMM**, clique no arquivo **Gerarchave de registro**. A chave é válida para 5 dias após ser gerada. Copie o arquivo para o servidor VMM. Você precisará dele quando for configurar o Provedor.

    ![Chave de Registro][Chave de Registro]

4.  Na página **Início Rápido**, em **Preparar servidores VMM**, clique **Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM** para obter a versão mais recente do arquivo de instalação do Provedor.

5.  Execute esse arquivo no servidor VMM de origem.

6.  Na **Verificação dos pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza.

    ![Pré-requisitos][Pré-requisitos]

7.  No **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilidado, a atualização será instaada de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft][Atualizações da Microsoft]

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

1.  Em **Conexão de Internet**, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione **Usar configurações de proxy padrão do sistema** para usar as configurações de conexão com a Internet definidas no servidor.

    ![Configurações da Internet][Configurações da Internet]

2.  Em **Chave de registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
3.  Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado.
4.  Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

    ![Registros do servidor][Registros do servidor]

5.  Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nivem no consolte VMM.

6.  Em **Criptografia de dados**, você especifica um local para salvar um certificado SLL automaticamente gerado para criptografia de dados. Esse certificado é usado se você habilitar a criptografia de dados para uma nuvem protegida pelo Azure no portal de Recuperação de Site do Azure. Mantenha esse certificado protegido. Ao executar um failover para o Azure, você o selecionará para descriptografar dados criptografados.
    Essa opção não é relevante se você estiver replicando de um site local para outro.

    ![Registros do servidor][1]

7.  Clique em **Registrar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido no final da guia **Recursos** da página **Servidores** no cofre.

## <span id="storage"></span></a>Etapa 3: Criar uma conta de armazenamento do Azure

Se não tiver uma conta de armazenamento do Azure, clique em **Adicionar uma Conta de Armazenamento do Azure**. A conta deve ter georreplicação habilitada. Ela deve estar localizada na mesma região que o serviço de Recuperação de Site do Azure e ser associada à mesma assinatura.

Use este tutorial para configurar uma verificação de conceito rápida para Recuperação de Site do Azure no local para implantação do Azure. Ele usa o caminho rápido e configurações padrão onde possível. Você criará um cofre de Recuperação de Site do Azure, instalará o Provedor de Recuperação de Site do Azure no servidor VMM de origem, instalará o Agente de Serviços de Recuperação do Azure nos servidores host do Hyper-V nas nuvens VMM, definirá as configurações de proteção de nuvem, habilitará a proteção para máquinas virtuais e testará sua implantação.

![Conta de armazenamento][Conta de armazenamento]

## <span id="agent"></span></a>Etapa 4: Instalar o Agente de Serviços de Recuperação do Azure em hosts Hyper-V

Instale o Agente de Serviços de Recuperação do Azure em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

1.  Na página Início rápido, clique em **Baixar o Agente de Serviços de Recuperação de Site do Azure e instalar nos hosts** para obter a versão mais recente do arquivo de instalação do agente.

    ![Instalar o Agente de Serviços de Recuperação][Instalar o Agente de Serviços de Recuperação]

2.  Execute o arquivo de instalação em cada servidor host Hyper-V localizado nas nuvens VMM que você deseja proteger.
3.  Na página **Verificação de pré-requisitos**, clique em **Avançar**. Quaisquer pré-requisitos faltantes serão instalados automaticamente.

    ![Agente de Serviços de Recuperação de Pré-requisitos][Agente de Serviços de Recuperação de Pré-requisitos]

4.  Na página **Configurações de instalação**, especifique onde você deseja instalar o Agente e selecione a localização do cache no qual os metadados de backup serão instalados. Em seguida, clique em **Instalar**.

## <span id="clouds"></span></a>Etapa 5: Definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habiitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia **Itens Protegidos** no cofre.

![Publicar uma nuvem][Publicar uma nuvem]

1.  Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.

2.  Na guia **Itens Protegidos**, clique na nuvem que você deseja configurar e vá até a guia **Configuração**.
3.  Em **Destino**, selecione **Microsoft Azure**.
4.  Em **Conta de Armazenamento**, selecione o armazenamento do Azure que você deseja utilizar para armazenar máquinas virtuais do Azure.
5.  Defina **Criptografar dados armazenados** como **Desligado**. Essa configuração especifica que os dados podem ser criptografados e replicados entre o site local e o Azure.
6.  Em **Copiar frequência**, deixe a configuração padrão. Esse valor especifica com que frequência os dados devem ser sincronizados entre os locais de origem e destino.
7.  Em **Manter pontos de recuperação para**, mantenha a configuração padrão. Com um valor padrão de zero apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
8.  Em **Frequência dos instantâneos consistentes de aplicativo**, mantenha a configuração padrão. Esse valor especifica a frequência de criação de snapshots. Os snapshots usam o Volume Shadow Copy Service (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é obtido. Se você definir um valor, certifique-se de que ele seja menor que o número dos pontos de recuperação adicionais que você configurar.
9.  Em **Hora de início para replicação**, especifique quando a replicação inicial dos dados para o Azure deve começar. O fuso horário do servidor de host Hyper-V será usado. Recomendamos que você agende a replicação inicial fora dos horários de pico.

    ![Configurações de replicação de nuvem][Configurações de replicação de nuvem]

Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia **Trabalhos**. Todos os servidores do host do Hyper-V na nuvem de origem do VMM serão configurados para replicação. Após o salvamento, as configurações de nuvem podem ser modificadas a partir da guia **Configurar**. Para modificar o local de destino ou armazenamento de destino, você deve remover a configuração de nuvem e reconfigurar a nuvem. Observe que, se você mudar a conta de armazenamento, a mudança só será aplicada para máquinas virtuais habilitadas para proteção, após a conta de armazenamento ter sido modificada. Máquinas virtuais existentes não são migradas para a nova conta de armazenamento.

## <span id="networkmapping"></span></a>Etapa 6: Configurar o mapeamento de rede

Este tutorial descreve o caminho mais simples para implantar a Recuperação de Site do Azure em um ambiente de teste. Se você deseja configurar o mapeamento de rede como parte deste tutorial, leia [Preparar para mapeamento de rede][Preparar para mapeamento de rede] no Guia de Planejamento. Para configurar o mapeamento, siga estas etapas para [Configurar mapeamento de rede][Configurar mapeamento de rede] no guia de implantação.

## <span id="virtualmachines"></span></a>Etapa 7: Habilitar a proteção para máquinas virtuais

Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem. Observe o seguinte:

-   As máquinas virtuais devem cumprir os requisitos do Azure. Consulte-os em [Pré-requisitos e suporte][Pré-requisitos e suporte] no guia Planejamento.
-   Para habilitar a proteção, o sistema operacional e as propriedades do disco do sistema operacional devem estar definidos para as máquinas virtuais. Ao criar uma máquina virtual no VMM usando um modelo de máquina virtual, é possível definir a propriedade. Também é possível definir essas propriedades para máquinas virtuais existentes nas guias \*\*Geral\*\* e \*\*Configuração de Hardware\*\* das propriedades da máquina virtual. Se você não definir essas propriedades no VMM, poderá configurá-las no portal de Recuperação de Site do Azure.

![Criar máquina virtual][Criar máquina virtual]

![Modificar propriedades de máquina virtual][Modificar propriedades de máquina virtual]

1.  Para habilitar a proteção, na guia **Máquinas Virtuais**, na nuvem na qual a máquina virtual está localizada, clique em **Habilitar proteção** e, então, selecione **Adicionar máquinas virtuais**
2.  A partir da lista de máquinas virtuais na nuvem, selecione um que você quer proteger.

    ![Habilitar a proteção da máquina virtual][Habilitar a proteção da máquina virtual]

3.  Verifique as propriedades da máquina virtual e modifique-as conforme o necessário.

    ![Verificar as máquinas virtuais][Verificar as máquinas virtuais]

Acompanhar o progresso da ação de Habilitar Proteção na guia \*\*Trabalhos\*\*, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção executar a máquina virtual está pronta para failover. Após a proteção estar habilitada e as máquinas virtuais serem replicadas, você será capaz de visualizá-los no Azure.

![Trabalho de proteção da máquina virtual][Trabalho de proteção da máquina virtual]

## <span id="test"></span></a>Etapa 8: Teste a implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe o seguinte:

-   Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover.
-   Após o failover, você usará um endereço IP público para conectar-se à máquina virtual no Azure usando a Área de trabalho remota. Se você quiser fazer isso, certifique-se de não ter nenhuma política de domínio que impeça que você se conecte a uma máquina virtual usando um endereço público.

-   Para obter instruções sobre a criação de um plano de recuperação, consulte [Criar e personalizar planos de recuperação: No local para Azure][Criar e personalizar planos de recuperação: No local para Azure].
-   Para obter instruções sobre a execução de um failover de teste, consulte [Testar uma implantação local para o Azure][Testar uma implantação local para o Azure].

</p>
### <span id="runtest"></span></a>Monitorar atividade

Você pode usar a guia **Trabalhos** e **Painel** para exibir e monitorar os principais trabalhos executados pelo cofre de Recuperação de Site do Azure, incluindo a configuração da proteção para uma nuvem; habilitação e desabilitação da proteção para uma máquina virtual; execução de um failover (planejado, não planejado ou teste) e confirmação de um failover não planejado.

A partir da guia **Trabalhos** você exibe os trabalhos, realiza busca detalhada nos detalhes do trabalho e erros, executa consultas de trabalho para recuperar trabalhos que combinam com os critérios específicos, exporta trabalhos para o Excel e reinicia os trabalhos falhos.

A partir de **Painel**, você pode baixar as últimas versões dos arquivos de instalação do Provedor e do Agente, obtenha informações de configuração para o cofre, consulte o número de máquinas virtuais que tem a proteção gerenciada pelo cofre, veja os trabalhos recentes, grencie o certificado do cofre e sincronize novamente as máquinas virtuais.

Para obter mais informações sobre a interação com trabalhos e o painel, consulte o [Guia de Operações e Monitoramento][Guia de Operações e Monitoramento].

## <span id="next"></span></a>Próximas etapas

-   Para planejar e implantar a Recuperação de Site do Azure em um ambiente de produção completo, consulte [Guia de planejamento para a Recuperação do Hyper-V][Planejamento para o Azure Site Recovery Deployment] e [Guia de implantação de Recuperação de Site do Azure][Guia de implantação de Recuperação de Site do Azure].
-   Para dúvidas, visite o [Fórum de serviços de recuperação do Windows Azure][Fórum dos Serviços de Recuperação do Azure].

</div>

  [Introdução à Recuperação de Site do Azure: No local para proteção no local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Planejamento para o Azure Site Recovery Deployment]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implantação da Recuperação de Site do Azure: Proteção para o Azure no local]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Recuperação de Site do Azure: Cenários comuns de erro e resoluções]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Fórum dos Serviços de Recuperação do Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Avaliação gratuita do Azure]: http://aka.ms/try-azure
  [Detalhes de Preços do Gerenciador de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introdução ao Armazenamento do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Pré-requisitos e suporte]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Etapa 1: Criar um cofre]: #vault
  [Etapa 2: Instalar o aplicativo Provider]: #download
  [Etapa 3: Adicionar uma conta de armazenamento Azure]: #storage
  [Etapa 4: Instalar o aplicativo Agent]: #agent
  [Etapa 5: Configurar a proteção da nuvem]: #clouds
  [Etapa 6: Configurar o mapeamento de rede]: #NetworkMapping
  [Etapa 7: Habilitar proteção para máquinas virtuais]: #virtualmachines
  [Etapa 8: Testar a implantação]: #test
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Novo cofre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Ícone de Inicialização Rápida]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Chave de Registro]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Pré-requisitos]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Atualizações da Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Configurações da Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registros do servidor]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Conta de armazenamento]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Instalar o Agente de Serviços de Recuperação]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Agente de Serviços de Recuperação de Pré-requisitos]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Publicar uma nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configurações de replicação de nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Preparar para mapeamento de rede]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurar mapeamento de rede]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Criar máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modificar propriedades de máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Habilitar a proteção da máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verificar as máquinas virtuais]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Trabalho de proteção da máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Criar e personalizar planos de recuperação: No local para Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testar uma implantação local para o Azure]: http://go.microsoft.com/fwlink/?LinkId=511494
  [Guia de Operações e Monitoramento]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guia de implantação de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=321295
