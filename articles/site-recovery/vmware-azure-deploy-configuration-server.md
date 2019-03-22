---
title: Implantar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: ef0e29217e03b3c5d1b2880a6ce755c6cc02ceba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004445"
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você estiver replicando VMs do VMware no Azure. [Siga este artigo](physical-azure-set-up-source.md) se você precisa configurar um servidor de configuração para replicação de servidor físico.

> [!TIP]
> Você pode saber mais sobre a função de Servidor de configuração como parte da arquitetura do Azure Site Recovery [aqui](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Implantação de servidor de configuração por meio do modelo OVA

O servidor de configuração deve ser configurado como uma VM VMware altamente disponível com determinados requisitos mínimos de hardware e dimensionamento. Para que a implantação seja mais fácil e conveniente, o Site Recovery fornece um modelo OVA (Aplicativo de Virtualização Aberto) para download para configurar o servidor de configuração que atende a todos os requisitos obrigatórios relacionados abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração estão resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão do Active Directory do Azure

Você precisar de um usuário com **um dos seguintes** conjunto de permissões no AAD (Azure Active Directory) para registrar o servidor de configuração com os serviços do Azure Site Recovery.

1. Usuário deve ter a função de "Desenvolvedor do aplicativo" para criar o aplicativo.
   1. Para verificar, entrar no portal do Azure</br>
   1. Navegue para o Azure Active Directory > funções e os administradores</br>
   1. Verifique se a função de "Desenvolvedor do aplicativo" é atribuída ao usuário. Se não, use um usuário com essa permissão, ou vá para [administrador para habilitar a permissão](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Se não puder ser atribuída a função "Desenvolvedor de aplicativos", certifique-se de que o sinalizador de "Usuário pode registrar o aplicativo" é definido como true para o usuário criar a identidade. Para habilitar acima permissões,
   1. Entre no portal do Azure
   1. Navegue para o Azure Active Directory > configurações do usuário
   1. Em * * registros do aplicativo ","Os usuários podem registrar aplicativos"deve ser escolhido como"Sim".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> É Services(ADFS) de Federação do Active Directory **não tem suporte**. Use uma conta gerenciada por meio [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>planejamento de capacidade

Os requisitos de dimensionamento para o servidor de configuração dependem da taxa potencial de alteração de dados. Use essa tabela como um guia.

| **CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar máquinas menos de 100. |
| 12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replica 100-150 máquinas. |
| 16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replica 150-200 máquinas. |

Se você estiver replicando mais de uma VM do VMware, leia as [considerações de planejamento de capacidade](site-recovery-plan-capacity-vmware.md). Execute a [ferramenta Planejador de Implantação](site-recovery-deployment-planner.md) para a replicação do VMware.

## <a name="download-the-template"></a>Baixe o modelo

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo OVA (Aplicativo de Virtualização Aberta) para o servidor de configuração.

   > [!TIP]
   >Também é possível baixar a versão mais recente do modelo de servidor de configuração diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com o modelo OVA é uma licença de avaliação válida por 180 dias. Após esse período, cliente precisa ativar o windows com uma licença adquirida.

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no VMware vCenter Server ou no host vSphere ESXi, usando o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF.

     ![Modelo de OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **selecionar armazenamento**, para melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**. O uso da opção de provisionamento dinâmico pode afetar o desempenho do servidor de configuração.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

    * Para adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.

> [!IMPORTANT]
> Não altere as configurações de recursos (memória/núcleos/restrição de CPU), nem modifique/exclua serviços ou arquivos instalados no servidor de configuração depois da implantação. Isso afetará o registro no servidor de configuração com os serviços do Azure e o desempenho do servidor de configuração.

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
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure.</br>
     a. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.</br>
    b. Certifique-se de que a conta de usuário escolhido tem permissões para criar um aplicativo no Azure. Para habilitar as permissões necessárias, siga as diretrizes fornecidas [aqui](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração será iniciado **automaticamente** em alguns segundos.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade**. Nas listas suspensas, primeiro selecione a NIC usada pelo servidor de processo integrado para a instalação de descoberta e por push do serviço de mobilidade em máquinas de origem e, em seguida, selecione a NIC que usa o servidor de configuração para a conectividade com o Azure. Em seguida, selecione **Salvar**. Depois de configurado, você não pode alterar essa configuração. É altamente recomendável não alterar o endereço IP de um servidor de configuração. Certifique-se de que o IP atribuído ao servidor de configuração é o IP estático e não o IP DHCP.
2. Na **selecionar serviços de recuperação do cofre**, entrar no Microsoft Azure com as credenciais usadas na **etapa 6** de "[registrar o servidor de configuração com os serviços do Azure Site Recovery](#register-the-configuration-server-with-azure-site-recovery-services)" .
3. Depois de entrar, selecione sua assinatura do Azure e o grupo de recursos relevantes e o cofre.

    > [!NOTE]
    > Uma vez registrado, não há flexibilidade para alterar o cofre de serviços de recuperação.
    > Cofre dos serviços de recuperação alteração exigiria desassociação de servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração é interrompida. Saiba [mais](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Em **Instalar software de terceiros**,

    |Cenário   |Etapas a serem executadas  |
    |---------|---------|
    |Posso baixar e instalar o MySQL manualmente?     |  Sim. Baixe o aplicativo MySQL e coloque-o na pasta **C:\Temp\ASRSetup** e instale-o manualmente. Agora, quando você aceitar os termos > clicar em **Baixar e instalar**, o portal dirá *Já instalado*. Você pode prosseguir para a próxima etapa.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque o aplicativo instalador do MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos e clique em  **Baixar e instalar**. O portal usará o instalador adicionado por você e instalará o aplicativo. Você pode prosseguir para a próxima etapa após a instalação.    |
    |Desejo baixar e instalar o MySQL por meio do Azure Site Recovery     |  Aceite o contrato de licença e clique em **Baixar e Instalar**. Em seguida, você pode prosseguir para a próxima etapa após a instalação.       |

5. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
6. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e **Continuar**. As credenciais digitadas aqui são salvas localmente.
8. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e a senha de máquinas virtuais para instalar automaticamente o Serviço de Mobilidade durante a replicação. Para computadores **Windows**, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o **Linux**, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registro.
10. Após o término do registro, abra o portal do Azure, verifique se o servidor de configuração e o servidor VMware estão listados em **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura de Recuperação do Site** > **Servidores de Configuração**.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, siga estas [etapas](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções detalhadas sobre como atualizar todos os componentes do Site Recovery, clique [aqui](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gerenciar o servidor de configuração

Para evitar interrupções na replicação em andamento, assegure-se de que o endereço IP do servidor de configuração não seja alterado após o servidor de configuração ter sido registrado em uma área segura. Você pode aprender mais sobre as tarefas comuns de gerenciamento do servidor de configuração [aqui](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Perguntas frequentes

1. Por quanto tempo a licença fornecida no servidor de configuração implantada por meio do OVF é válida? O que acontece se eu não reativar a licença?

    A licença fornecida com o modelo OVA é uma licença de avaliação válida por 180 dias. Antes do vencimento, você precisa ativar a licença. Caso contrário, isso pode resultar no desligamento frequente do servidor de configuração e, assim, causar impedimentos nas atividades de replicação.

2. Posso usar a VM na qual o servidor de Configuração está instalado para outros fins?

    **Não**. Recomendamos que você use a VM para a única finalidade do servidor de configuração. Não deixe de seguir todas as especificações mencionadas em [Pré-requisitos](#prerequisites) para um gerenciamento eficiente da recuperação de desastre.
3. Posso trocar o cofre já registrado no servidor de configuração por um cofre recém-criado?

    **Não**, uma vez que um cofre é registrado no servidor de configuração, ele não pode ser alterado.
4. Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    **Sim**, o mesmo servidor de configuração pode ser usado para replicar computadores físicos e máquinas virtuais. No entanto, o computador físico pode ter failback somente para uma VM do VMware.
5. Qual é a finalidade de um servidor de configuração e em que local ele é usado?

    Veja [Arquitetura de replicação do VMware para o Azure](vmware-azure-architecture.md) para saber mais sobre o servidor de configuração e suas funcionalidades.
6. Onde obter a última versão do servidor de Configuração?

    Para obter as etapas para atualizar o servidor de configuração por meio do portal, veja [Atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções detalhadas sobre como atualizar todos os componentes do Site Recovery, confira [aqui](https://aka.ms/asr_how_to_upgrade).
7. Em que local posso baixar a frase secreta para o servidor de configuração?

    Veja [este artigo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) para baixar a frase secreta.
8. Posso alterar a frase secreta?

    **Não**, **recomendamos fortemente que você não altere a frase secreta** do servidor de configuração. Qualquer alteração na frase secreta interrompe a replicação de máquinas protegidas e leva a um estado de integridade crítico.
9. Em que local posso baixar as chaves de registro do cofre?

    No **Cofre dos Serviços de Recuperação**, **Gerenciar** > **Infraestrutura do Site Recovery** > **Servidores de Configuração**. Em Servidores, selecione **Baixar chave de registro** para baixar o arquivo de credenciais do cofre.
10. Posso clonar um servidor de configuração existente e usá-lo para orquestração de replicação?

    **Não**, não há suporte para o uso de um componente de servidor de configuração clonado.

11. Posso alterar o IP do servidor de configuração?

    **Não**, é altamente recomendável não alterar o endereço IP de um servidor de configuração. Certifique-se de que todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs DHCP.
12. Posso configurar o servidor de configuração no Azure?

    É recomendável configurar o servidor de configuração no ambiente local com uma linha de visão direta com o v-Center e para minimizar as latências de transferência de dados. É possível fazer backups agendados do servidor de configuração para [fins de failback](vmware-azure-manage-configuration-server.md#failback-requirements).

Para obter mais respostas a perguntas frequentes sobre o servidor de configuração, confira a [documentação sobre perguntas comuns a respeito do servidor de configuração](vmware-azure-common-questions.md#configuration-server).

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
