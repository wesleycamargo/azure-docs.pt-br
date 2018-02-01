---
title: "Configurar a recuperação de desastre de VMs VMware locais para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs VMware locais para o Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8acc8deff8b635c97e8722d65a728aebf0e49bb3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar a recuperação de desastre de VMs VMware locais para o Azure

Este tutorial mostra como configurar a recuperação de desastre para o Azure para VMs de VMware locais em execução no Windows. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Especificar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Criar uma política de replicação
> * Habilitar a replicação para uma VM

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](tutorial-prepare-on-premises-vmware.md)

Antes de começar, é aconselhável [examinar a arquitetura](concepts-vmware-to-azure-architecture.md) do cenário de recuperação de desastre.


## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. Em **cofres dos Serviços de Recuperação**, clique no nome do cofre, **ContosoVMVault**.
2. Em **Introdução**, clique em Site Recovery. A seguir, clique em **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores?, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hipervisor do VMware vSphere**. Em seguida, clique em **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, você precisa de um computador local único e altamente disponível para hospedar os componentes locais do Site Recovery. Os componentes incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre.

- O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- O servidor de processo atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure. O servidor de processo também instala o serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs em VMware locais.
- O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

Para configurar o servidor de configuração como uma VM da VMware altamente disponível do VMware, você baixa um modelo OVF preparado e importa o modelo para a VMware para criar a VM. Após configurar o servidor de configuração, registre-o no cofre. Após o registro, o Site Discovery descobre VMs locais da VMware.

### <a name="download-the-vm-template"></a>Baixe o modelo de VM

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar a origem**, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de formato OVF para o servidor de configuração.

  > [!TIP]
  A versão mais recente do modelo de servidor de configuração pode ser baixada diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Faça logon no VMware vCenter Server ou no host vSphere ESXi, usando o VMWare vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF**, para iniciar o assistente para Implantar o modelo de OVF.  

     ![Modelo de OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. Em **Selecione origem**, especifique o local do OVF baixado.
4. Em **Revisar detalhes**, clique em **Avançar**.
5. Em **Selecionar nome e pasta**, e **Selecionar configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento**, para melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
4. No restante das páginas do assistente, aceite as configurações padrão.
5. Em **Pronto para concluir**:
  - Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.
  - Se você deseja adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC, mas você pode adicionar mais NICs após a implantação.

  
## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

Se você deseja adicionar mais uma NIC ao servidor de configuração, faça-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, clique em **Adicionar** > **Adaptador Ethernet**. Em seguida, clique em **Próximo**.
3. Selecione e o tipo de adaptador e uma rede. 
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Clique em **Avançar** > **Concluir**e, em seguida, clique em **OK**.


## <a name="register-the-configuration-server"></a>Registrar o servidor de configuração 

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e especifique uma senha de administrador.
3. Após a conclusão da instalação, faça logon para na VM como administrador.
4. Na primeira vez que fizer logon, a Ferramenta de Configuração do Azure Site Recovery inicia.
5. Especifique um nome que é usado para registrar o servidor de configuração com Site Recovery. Em seguida, clique em **Próximo**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, clique em **Entrar**, para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Faça logon no computador novamente. O assistente de gerenciamento do servidor de configuração será iniciado automaticamente.

### <a name="configure-settings-and-connect-to-vmware"></a>Definir configurações e conectar-se à VMware

1. No assistente de gerenciamento do servidor de configuração > **Configurar conectividade**, selecione a NIC que receberá o tráfego de replicação. Em seguida, clique em **Salvar**. Você não pode alterar essa configuração após ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, selecione sua assinatura do Azure, o grupo de recursos relevantes e o cofre.
3. Em **Instalar software de terceiros**, aceite o contrato de licença e clique em **Baixar e instalar**, para instalar o MySQL Server.
4. Clique em **Instalar VMware PowerLCI**. Verifique se todas as janelas de navegador estão fechadas antes de fazer isso. Clique em **Continuar**
5. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de você poder continuar.
6. Em **Configurar vCenter Server/vSphere ESXi Server**, especifique o FQDN ou endereço IP do vCenter Server ou host vSphere, nos quais as máquinas virtuais que você deseja replicar estão localizadas. Especifique a porta na qual o servidor está escutando e um nome amigável a ser usado para o VMware Server no cofre.
7. Especifique as credenciais que serão usadas pelo servidor de configuração para se conectar ao VMware Server. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Clique em **Adicionar** e depois em **Continuar**.
8. Em **Configurar credenciais de máquina virtual**, especifique o nome de usuário e a senha que será usada para instalar automaticamente o serviço de Mobilidade em computadores, quando a replicação está habilitada. Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o Linux, forneça detalhes para a conta raiz.
9. Clique em **Finalizar configuração** para concluir o registro. 
10. Após a conclusão do registro, no portal do Azure, verifique se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Em seguida, clique em **OK** para definir as configurações de destino.


A Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

> [!NOTE]
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique se o seu modelo de implantação de destino é baseada no Gerenciador de Recursos ou clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [Portal do Azure](https://portal.azure.com) e clique em **Todos os recursos**.
2. Clique no cofre de Recuperação de Serviço chamado **ContosoVMVault**.
3. Para criar uma política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+ Política de Replicação**.
4. Em **Criar política de replicação**, especifique um nome de política **VMwareRepPolicy**.
5. Em **Limite de RPO**, use o padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
6. Em **Retenção de ponto de recuperação**, use o padrão de 24 horas para a duração do período de retenção de cada ponto de recuperação. Para este tutorial, podemos selecionar 72 horas. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
7. Em **Frequência do instantâneo consistente com aplicativo**, use o padrão de 60 minutos para a frequência em que os instantâneos consistentes com o aplicativo são criados. Clique em **OK** para criar a política.

   ![Política](./media/tutorial-vmware-to-azure/replication-policy.png)

A política é associada automaticamente ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.

## <a name="enable-replication"></a>Habilitar a replicação

O Site Recovery instala o serviço de Mobilidade quando a replicação é habilitada para uma VM. Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

Habilite a replicação da seguinte maneira:

1. Clique em **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo (servidor de configuração). Em seguida, clique **OK**.
6. Em **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs do failover.
7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados.
8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.
9. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
10. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.
11. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
12. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
13. Clique em **Habilitar a Replicação**.

Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

Para monitorar as VMs adicionadas, você pode verificar o horário da última descoberta de VMs em **Servidores de Configuração**
> **Último Contato Em**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](site-recovery-test-failover-to-azure.md)
