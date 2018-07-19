---
title: Implantar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 240f5270d083fa5f4742f3ed2cd61feee2b635ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718950"
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você estiver replicando VMs do VMware no Azure. [Siga este artigo](physical-azure-set-up-source.md) se você precisa configurar um servidor de configuração para replicação de servidor físico.

>[!TIP]
Você pode saber mais sobre a função de Servidor de configuração como parte da arquitetura do Azure Site Recovery [aqui](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Implantação de servidor de configuração por meio do modelo OVA

O servidor de configuração deve ser configurado como uma VM VMware altamente disponível com determinados requisitos mínimos de hardware e dimensionamento. Para que a implantação seja mais fácil e conveniente, o Site Recovery fornece um modelo OVA (Aplicativo de Virtualização Aberto) para download para configurar o servidor de configuração que atende a todos os requisitos obrigatórios relacionados abaixo.

## <a name="prerequisites"></a>pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração estão resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>planejamento de capacidade

Os requisitos de dimensionamento para o servidor de configuração dependem da taxa potencial de alteração de dados. Use essa tabela como um guia.

| **CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar máquinas menos de 100. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replica 100-150 máquinas. |
| 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replica 150-200 máquinas. |

Se você estiver replicando mais de uma VM do VMware, leia as [considerações de planejamento de capacidade](/site-recovery-plan-capacity-vmware.md). Execute a [ferramenta Planejador de Implantação](site-recovery-deployment-planner.md) para a replicação do VMware.

## <a name="download-the-template"></a>Baixe o modelo

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo OVA (Aplicativo de Virtualização Aberta) para o servidor de configuração.

  > [!TIP]
>Também é possível baixar a versão mais recente do modelo de servidor de configuração diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).

>[!NOTE]
A licença fornecida com o modelo OVA é uma licença de avaliação válida por 180 dias. Após esse período, o cliente precisa ativar o Windows com uma licença adquirida.

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no VMware vCenter Server ou no host vSphere ESXi, usando o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF.

     ![Modelo de OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento**, para melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

    * Para adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.

## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede. 
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Em seguida, selecione **Avançar** > **Concluir** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registre o servidor de configuração com os serviços do Azure Site Recovery

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que você entrar, em alguns segundos, a Ferramenta de Configuração do Azure Site Recovery será iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração será iniciado **automaticamente** em alguns segundos.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade** e, em seguida, selecione a NIC que será usada pelo servidor de processo para receber o tráfego de replicação das VMs. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, entre no Microsoft Azure, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.

    > [!NOTE]
    > Uma vez registrado, não há flexibilidade para alterar o cofre de serviços de recuperação.
    
3. Em **Instalar software de terceiros**,

    |Cenário   |Etapas a serem executadas  |
    |---------|---------|
    |Posso baixar e instalar o MySQL manualmente?     |  Sim. Baixe o aplicativo MySQL e coloque-o na pasta **C:\Temp\ASRSetup** e instale-o manualmente. Agora, quando você aceitar os termos > clicar em **Baixar e instalar**, o portal dirá *Já instalado*. Você pode prosseguir para a próxima etapa.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque o aplicativo instalador do MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos e clique em  **Baixar e instalar**. O portal usará o instalador adicionado por você e instalará o aplicativo. Você pode prosseguir para a próxima etapa após a instalação.    |
    |Desejo baixar e instalar o MySQL por meio do Azure Site Recovery     |  Aceite o contrato de licença e clique em **Baixar e Instalar**. Em seguida, você pode prosseguir para a próxima etapa após a instalação.       |
4. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
5. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
6. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e **Continuar**. As credenciais digitadas aqui são salvas localmente.
7. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e a senha de máquinas virtuais para instalar automaticamente o Serviço de Mobilidade durante a replicação. Para computadores **Windows**, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o **Linux**, forneça detalhes para a conta raiz.
8. Selecione **Finalizar configuração** para concluir o registro.
9. Após o término do registro, abra o portal do Azure, verifique se o servidor de configuração e o servidor VMware estão listados em **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura de Recuperação do Site** > **Servidores de Configuração**.

## <a name="faq"></a>Perguntas frequentes

1. Posso usar a VM na qual o servidor de Configuração está instalado para outros fins?

    **Não**. Recomendamos que você use a VM para a única finalidade do servidor de configuração. Siga todas as especificações mencionadas na [seção anterior](vmware-azure-deploy-configuration-server.md#Prerequisites) para um gerenciamento eficiente da recuperação de desastre.
2. Posso trocar o cofre já registrado no servidor de configuração por um cofre recém-criado?

    **Não**, uma vez que um cofre é registrado no servidor de configuração, ele não pode ser alterado.
3. Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    **Sim**, o mesmo servidor de configuração pode ser usado para replicar máquinas físicas e virtuais. No entanto, o computador físico pode ter failback somente para uma VM do VMware.
4. Qual é a finalidade de um Servidor de configuração e em que local ele é usado?

    Confira nossa arquitetura do Azure Site Recovery [aqui](vmware-azure-architecture.md) para saber mais sobre o servidor de configuração e suas funcionalidades.
5. Onde obter a última versão do servidor de Configuração?

    Veja o artigo sobre as etapas usadas para atualizar o servidor de configuração [por meio do portal](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Baixe-o diretamente no [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).
6. Em que local posso baixar a frase secreta para o servidor de configuração?

    Veja [este artigo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) para baixar a frase secreta.
7. Em que local posso baixar as chaves de registro do cofre?

    No **Cofre dos Serviços de Recuperação**, **Gerenciar** > **Infraestrutura do Site Recovery** > **Servidores de Configuração**. Em Servidores, selecione **Baixar chave de registro** para baixar o arquivo de credenciais do cofre.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, leia as etapas fornecidas [aqui](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
