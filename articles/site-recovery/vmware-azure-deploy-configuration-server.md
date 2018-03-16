---
title: "Implantar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 99b368ca364bd7c5bebfc00c2df0f04333293388
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você estiver replicando VMs do VMware no Azure. [Siga este artigo](physical-azure-set-up-source.md) se você precisa configurar um servidor de configuração para replicação de servidor físico.

## <a name="prerequisites"></a>pré-requisitos

É recomendável que você implante o servidor de configuração como uma VM do VMware altamente disponível. Os requisitos mínimos de hardware são resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>planejamento de capacidade

Os requisitos de dimensionamento para o servidor de configuração dependem da taxa potencial de alteração de dados. Use essa tabela como um guia.

| **CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar máquinas menos de 100. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replica 100-150 máquinas. |
| 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replica 150-200 máquinas. |


Se você estiver replicando VMs do VMware, leia mais sobre [considerações de planejamento de capacidade](/site-recovery-plan-capacity-vmware.md). Execute a [ferramenta Planejador de Implantação](site-recovery-deployment-planner.md) para a replicação do VMware.



## <a name="download-the-template"></a>Baixe o modelo

O Site Recovery fornece um modelo que pode ser baixado para configurar o servidor de configuração como uma VM do VMware altamente disponível. 

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de formato OVF para o servidor de configuração.

  > [!TIP]
  É possível baixar a versão mais recente do modelo de servidor de configuração diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware


1. Entre no VMware vCenter Server ou no host vSphere ESXi, usando o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF.

     ![Modelo de OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento**, para melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
4. No restante das páginas do assistente, aceite as configurações padrão.
5. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

    * Para adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.


## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede. 
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Em seguida, selecione **Avançar** > **Concluir** > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrar o servidor de configuração 

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que entrar, a Ferramenta de Configuração do Azure Site Recovery é iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração é iniciado automaticamente.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade**. Selecione o NIC para receber tráfego de replicação e, em seguida, selecione **Salvar**. Você não pode alterar essa configuração após ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.
3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server.
4. Selecione **Instalar VMware PowerCLI**. Verifique se todas as janelas de navegador estão fechadas antes de executar essa etapa. Depois selecione **Continuar**.
5. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
6. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando e um nome amigável para o VMware Server no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e depois **Continuar**.
8. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e a senha a serem usados para instalar automaticamente o Serviço de Mobilidade do Azure Site Recovery em computadores, quando a replicação está habilitada. Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o Linux, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registro. 
10. Após a conclusão do registro, no portal do Azure, verifique se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Em seguida, selecione **OK** para definir as configurações de destino.


## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
