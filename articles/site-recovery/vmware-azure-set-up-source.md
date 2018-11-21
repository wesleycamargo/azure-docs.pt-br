---
title: Configurar o ambiente de origem e o servidor de configuração para recuperação de desastre de VMs do VMware no Azure Site Recovery com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar seu ambiente local e o servidor de configuração para recuperação de desastre de VMs do VMware no Azure com o Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566305"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Configurar o ambiente de origem e o servidor de configuração

Este artigo descreve como configurar seu ambiente local de origem para recuperação de desastre de VMs do VMware no Azure com o [Azure Site Recovery](site-recovery-overview.md). Ele inclui etapas para configurar um computador local como o servidor de configuração do Site Recovery e descobrir automaticamente as VMs locais. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter feito o seguinte

- Planejar a implantação da recuperação de desastre usando o [Planejador de Implantações do Azure Site Recovery](site-recovery-deployment-planner.md). O Planejador ajuda a garantir que você tenha recursos e largura de banda suficientes para a recuperação de desastres e o RPO (objetivo de ponto de recuperação) em sua organização.
- [Configurar recursos do Azure](tutorial-prepare-azure.md) no [portal do Azure](http://portal.azure.com).
- [Configurar recursos do VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluindo uma conta dedicada para a descoberta automática de VMs do VMware local.
- Você pode [examinar perguntas comuns](vmware-azure-common-questions.md#configuration-server) sobre a implantação e o gerenciamento do servidor de configuração.


## <a name="choose-protection-goals"></a>Escolher objetivos de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. 
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.

## <a name="about-the-configuration-server"></a>Sobre o servidor de configuração

Você implanta um servidor de configuração local quando configura a recuperação de desastre de VMs do VMware no Azure.

- O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados.
- Você implanta o servidor de configuração como uma VM do VMware.
- O Site Recovery fornece um modelo OVA que você baixa do portal do Azure e importa para o vCenter Server para configurar a VM do servidor de configuração.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.


>[!NOTE]
Não use estas instruções se você estiver implantando o servidor de configuração para recuperação de desastre de computadores físicos locais no Azure. Para esse cenário, siga [este artigo](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Antes de implantar o servidor de configuração

Se você instalar o servidor de configuração como uma VM do VMware usando o modelo OVA, a VM será instalada com todos os pré-requisitos. Se quiser examinar os pré-requisitos, use os artigos a seguir.

- [Saiba mais sobre](vmware-azure-configuration-server-requirements.md) os requisitos de hardware, software e capacidade do servidor de configuração.
- Se você estiver replicando várias VMs do VMware, será necessário examinar as [considerações de planejamento de capacidade](site-recovery-plan-capacity-vmware.md) e executar a ferramenta [Planejador de Implantações do Azure Site Recovery](site-recovery-deployment-planner.md) para replicação do VMWare.
- A licença do Windows para o modelo OVA é uma licença de avaliação válida por 180 dias. Após esse período, você precisa ativar o Windows com uma licença válida. 
- O modelo OVA fornece uma maneira simples de definir o servidor de configuração como uma VM do VMware. Se por algum motivo você precisar configurar a VM do VMware sem o modelo, examine os pré-requisitos e siga as instruções do manual [neste artigo](physical-azure-set-up-source.md).
- Sua conta do Azure precisa de permissões para criar aplicativos do Azure AD.


>[!IMPORTANT]
O servidor de configuração precisa ser implantado conforme descrito neste artigo. Não há suporte para copiar ou clonar um servidor de configuração existente.

### <a name="set-up-azure-account-permissions"></a>Configurar permissões de conta do Azure

Um administrador de locatário/global pode atribuir permissões para criar aplicativos do Azure AD para a conta ou atribuir a função de Desenvolvedor do Aplicativo (que tem as permissões) para a conta.


O administrador de locatário/global pode conceder permissões para a conta da seguinte maneira:

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

 > [!NOTE]
 > Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Como alternativa, o administrador de locatário/global tem permissões para atribuir a função para a conta. [Saiba mais](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo OVA (Aplicativo de Virtualização Aberta) para o servidor de configuração.

  > [!TIP]
>Também é possível baixar a versão mais recente do modelo de servidor de configuração no [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Importar o modelo OVA para o VMware

1. Entre no servidor VMware vCenter ou no host vSphere ESXi com o VMWare vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF.

     ![Modelo de OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento**, para melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**, para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.
9. Por padrão, a VM é implantada com uma única NIC. Se você quiser adicionar outro NIC, limpe **Ligar após a implantação** e clique em **Concluir**. Depois, siga o procedimento a seguir.

## <a name="add-an-adapter-to-the-configuration-server"></a>Adicionar um adaptador ao servidor de configuração

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede.
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Em seguida, selecione **Avançar** > **Concluir** > **OK**.

## <a name="register-the-configuration-server"></a>Registrar o servidor de configuração 
Ligue a VM e registre o servidor de configuração no cofre do Site Recovery.

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM é inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.



## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Como parte da implantação, você precisa instalar o MySQL na VM do servidor de configuração. Isso pode ser feito de duas maneiras:

- Permitir que o Site Recovery baixe e instale o MySQL. Se quiser usar essa opção, você não precisará fazer nada antes de iniciar a instalação do servidor de configuração.
- Baixe e instale manualmente o MySQL: antes de configurar o servidor de configuração, baixe o instalador do MySQL e coloque-o na pasta **C:\Temp\ASRSetup**. Agora, instale o MySQL. 
- Baixar manualmente e permitir que o Site Recovery seja instalado. Antes de configurar o servidor de configuração, baixe o instalador do MySQL e coloque-o na pasta **C:\Temp\ASRSetup**.


1. Na primeira vez em que você entrar na VM, a Ferramenta de Configuração do Azure Site Recovery será iniciada.
2. Especifique um nome usado para registrar o servidor de configuração no cofre do Site Recovery. Em seguida, selecione **Avançar**.
3. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. Observe que a conta deve ter acesso ao cofre no qual você deseja registrar o servidor de configuração.
4. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
5. Entre novamente no computador. Em alguns segundos, o Assistente de Gerenciamento do Servidor de Configuração será iniciado automaticamente.
6. No assistente, selecione **Configurar conectividade**.
7. Selecione a NIC que o servidor de processo (em execução por padrão no servidor de rede) usa para receber o tráfego de replicação das VMs.
8. Em seguida, selecione **Salvar**. Observe que você não pode alterar as configurações do cofre após o servidor de configuração ser registrado. 
9. Em **Selecionar cofre de Serviços de Recuperação**, entre no Microsoft Azure, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes. 
10. Em **Instalar software de terceiros**, instale o MySQL:
     - Se o Site Recovery estiver tratando o download e a instalação do MySQL, clique em **Baixar e instalar**. Aguarde até que a instalação seja concluída e, em seguida, continue com o assistente.
     - Se você baixou o MySQL e o Site Recovery for instalá-lo, clique em **Baixar e instalar**. Aguarde até que a instalação seja concluída e continue com o assistente.
     - Se você baixou e instalou o MySQL manualmente, clique em **Baixar e instalar**. O aplicativo é exibido como **Já instalado**. Continue com o assistente.
11. Em **Validar configuração de dispositivo**, a instalação verifica os pré-requisitos antes de você continuar. 
12. Em **Configurar vCenter Server/vSphere ESXi Server**, insira o FQDN ou o endereço IP do vCenter Server ou host vSphere em que as máquinas virtuais que você deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando e especifique um nome amigável para o servidor do VMware no cofre.
13. Insira as credenciais que serão usadas pelo servidor de configuração para se conectar ao servidor do VMware e descobrir automaticamente VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** >  **Continuar**. As credenciais são salvas localmente.
14. Em **Configurar credenciais de máquina virtual**, especifique as credenciais que o Site Recovery usará para instalar automaticamente o Serviço de Mobilidade quando a replicação estiver habilitada para uma VM.
    - Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
15. Selecione **Finalizar configuração** para concluir o registro.
16. Após o término do registro, abra o portal do Azure e verifique se o servidor de configuração e o servidor do VMware aparecem em **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura de Recuperação do Site** > **Servidores de Configuração**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Excluir antivírus no servidor de configuração

Se algum software antivírus estiver em execução na VM do servidor de configuração, verifique se as seguintes pastas estão excluídas das operações de antivírus. Isso garante que a replicação e a conectividade funcionem conforme o esperado: 

- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\Arquivos de Programas\Provedor do Microsoft Azure Site Recovery
- C:\Arquivos de Programas\Gerente de Configuração do Microsoft Azure Site Recovery
- C:\Arquivos de Programas\Ferramenta de Coleta de Erro do Microsoft Azure Site Recovery
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Arquivos de Programas (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Diretório de instalação do Site Recovery. Por exemplo: E:\Arquivos de Programas (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Próximas etapas
- Se você tiver dificuldades, examine as páginas de [perguntas frequentes](vmware-azure-common-questions.md#configuration server) e [solução de problemas](vmware-azure-troubleshoot-configuration-server.md) do servidor de configuração.
- Agora, [configure o ambiente de destino](./vmware-azure-set-up-target.md) no Azure.
