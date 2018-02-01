---
title: " Implantar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: anoopkv
ms.openlocfilehash: e257ede08ac46ad863b4883b10399058e6f59f1f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o serviço do [Azure Site Recovery](site-recovery-overview.md) para a recuperação após desastres de VMs do VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração.

## <a name="prerequisites"></a>pré-requisitos

É recomendável que você implante o servidor de configuração como uma VM do VMware altamente disponível. Para replicação de servidor físico, o servidor de configuração pode ser definido em um computador físico. Os requisitos mínimos de hardware são resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>planejamento de capacidade

Os requisitos de dimensionamento para o servidor de configuração dependem da taxa potencial de alteração de dados. Use essa tabela como um guia.

| **CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar máquinas menos de 100. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replique entre 100 e 150 computadores. |
| 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replique entre 150 e 200 computadores. |


Se você estiver replicando VMs do VMware, leia mais sobre [considerações de planejamento de capacidade](/site-recovery-plan-capacity-vmware.md) e execute a [ferramenta de planejamento de implantação](site-recovery-deployment-planner.md) para replicação de VMWare.



## <a name="download-the-template"></a>Baixe o modelo

O Site Recovery fornece um modelo que pode ser baixado para configurar o servidor de configuração como uma VM do VMware altamente disponível. 

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar a origem**, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de formato OVF para o servidor de configuração.

  > [!TIP]
  A versão mais recente do modelo de servidor de configuração pode ser baixada diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver)


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

### <a name="configure-settings"></a>Configurar definições

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


## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Próximas etapas

Analisar os tutoriais para configurar a recuperação após desastres de [VMs VMware](tutorial-vmware-to-azure.md) e [servidores físicos](tutorial-physical-to-azure.md) no Azure.
