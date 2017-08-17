---
title: "Introdução - Fazer backup de VMs do Azure com um cofre de backup | Microsoft Docs"
description: "Use o portal clássico para fazer backup de máquinas virtuais do Azure em um cofre de Backup. Este tutorial explica todas as fases, incluindo a criação do cofre de Backup, o registro das máquinas virtuais, a criação da política de backup e a execução do trabalho de backup inicial."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/2/2017
ms.author: markgal;
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 1a0e738cfac01d90e91959412b1ad5591705f617
ms.contentlocale: pt-br
ms.lasthandoff: 08/03/2017

---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Introdução: Fazendo backup de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Proteger VMs em um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md)
> * [Proteger as VMs do Azure com um cofre de backup](backup-azure-vms-first-look.md)
>
>

Este tutorial explica as etapas para fazer backup de uma VM (máquina virtual) do Azure em um cofre de backup do Azure. Este artigo descreve o modelo clássico ou o modelo de implantação do Service Manager para fazer backup de máquinas virtuais. As etapas a seguir se aplicam somente a cofres de Backup criados no portal clássico. A Microsoft recomenda usar o modelo do Resource Manager para novas implantações.

Se você estiver interessado em fazer backup de uma VM em um cofre de Serviços de Recuperação que pertence a um grupo de recursos, confira [Introdução: proteger VMs em um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md).

Para concluir o tutorial a seguir com êxito, estes pré-requisitos devem existir:

* Você criou uma VM em sua assinatura do Azure.
* A VM tem conectividade com os endereços IP públicos do Azure. Para obter informações adicionais, veja [Conectividade de rede](backup-azure-vms-prepare.md#network-connectivity).


> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este tutorial deve ser usado com máquinas virtuais criadas no portal clássico.
>
>

## <a name="create-a-backup-vault"></a>Criar um cofre de backup
O cofre de backup é uma entidade que armazena todos os pontos de backups e recuperação que foram criados ao longo do tempo. O cofre de backup também contém as políticas de backup que serão aplicadas às máquinas virtuais incluídas no backup.

> [!IMPORTANT]
> A partir de março de 2017, você não poderá mais usar o portal clássico para criar os cofres de Backup.
> Você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, não será possível usar o PowerShell para criar os cofres de Backup. **1º de novembro de 2017**:
>- Todos os cofres de Backup restantes serão atualizados automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.
>

## <a name="discover-and-register-azure-virtual-machines"></a>Descobrir e registrar máquinas virtuais do Azure
Antes de registrar a VM em um cofre, execute o processo de descoberta para identificar novas VMs. Isso retorna uma lista de máquinas virtuais na assinatura, juntamente com informações adicionais, como o nome do serviço de nuvem e a região.

1. Entrar no [portal Clássico do Azure](http://manage.windowsazure.com/)
2. No portal clássico do Azure, clique em **Serviços de Recuperação** para abrir a lista de cofres dos Serviços de Recuperação.
    ![Selecionar carga de trabalho](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. Na lista de cofres, escolha o cofre para fazer backup de uma VM.

    Quando você seleciona o cofre, ele é aberto na página **Início Rápido**
4. No menu do cofre, clique em **Itens Registrados**.

    ![Selecionar carga de trabalho](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. No menu **Tipo**, selecione **Máquina Virtual do Azure**.

    ![Selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
6. Clique em **DESCOBRIR** na parte inferior da página.
    ![Botão Descobrir](./media/backup-azure-vms/discover-button-only.png)

    O processo de descoberta pode ser executado por alguns minutos, enquanto as máquinas virtuais estão sendo tabuladas. Há uma notificação na parte inferior da tela que informa você de que o processo está sendo executado.

    ![Descobrir VMs](./media/backup-azure-vms/discovering-vms.png)

    As alterações de notificação quando o processo é concluído.

    ![Descoberta concluída](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Clique em **REGISTRAR** na parte inferior da página.
    ![Botão Registrar](./media/backup-azure-vms-first-look/register-icon.png)
8. No menu de atalho **Registrar Itens** , selecione as máquinas virtuais que você deseja registrar.

   > [!TIP]
   > Várias máquinas virtuais podem ser registradas ao mesmo tempo.
   >
   >

    Um trabalho é criado para cada máquina virtual selecionada.
9. Clique em **Exibir Trabalho** na notificação para ir para a página **Trabalhos**.

    ![Registrar trabalho](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registrados junto com o status da operação de registro.

    ![Status de registro 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o status será alterado para refletir o estado *registrado* .

    ![Status de registro 2](./media/backup-azure-vms/register-status02.png)

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalar o Agente de VM na máquina virtual
O Agente de VM do Azure deve ser instalado na máquina virtual do Azure para a extensão de Backup funcionar. Se sua VM tiver sido criada da galeria do Azure, o agente de VM já estará presente na VM; você pode pular para [proteção de suas VMs](backup-azure-vms-first-look.md#create-the-backup-policy).

Se sua VM tiver migrado de um datacenter local, a VM provavelmente não terá o agente instalado. Você deve instalar o Agente de VM na máquina virtual antes de continuar a proteger a VM. Para obter etapas detalhadas sobre como instalar o Agente de VM, confira a [seção Agente de VM do artigo Backup de VMs](backup-azure-vms-prepare.md#vm-agent).

## <a name="create-the-backup-policy"></a>Criar a política de backup
Antes de disparar o trabalho de backup inicial, defina a agenda de quando os instantâneos de backup serão feitos. A agenda de quando os instantâneos de backup são criados e por quanto tempo esses instantâneos serão mantido compõem a política de backup. As informações de retenção se baseiam no esquema de rotação de backup Avô-pai-filho.

1. Navegue até o cofre de backup, em **Serviços de Recuperação** no Portal Clássico do Azure e clique em **Itens Registrados**.
2. Selecione **Máquina Virtual do Azure** no menu suspenso.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)
3. Na parte inferior da página, clique em **PROTEGER** .
    ![Clique em Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    O **assistente Proteger Itens** é mostrado e lista *apenas* as máquinas virtuais registradas e não protegidas.

    ![Configurar proteção em escala](./media/backup-azure-vms/protect-at-scale.png)
4. Selecione as máquinas virtuais que deseja proteger.

    Se houver duas ou mais máquinas virtuais com o mesmo nome, use o Serviço de Nuvem para distinguir entre elas.
5. No menu **Configurar proteção** , escolha uma política existente ou crie uma nova política para proteger as máquinas virtuais que você identificou.

    Os novos cofres de Backup têm uma política padrão associada ao cofre. Essa política usa um instantâneo diário a cada noite, e o instantâneo diário é mantido por 30 dias. Cada política de backup pode ter várias máquinas virtuais associadas a ela. No entanto, a máquina virtual só pode estar associada a apenas uma política de cada vez.

    ![Proteger com nova política](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > Uma política de backup também inclui um esquema de retenção para os backups agendados. Se você selecionar uma política de backup, não será possível modificar as opções de retenção na próxima etapa.
   >
   >
6. Em **Intervalo de Retenção** , defina o escopo diário, semanal, mensal e anual para os pontos de backup específicos.

    ![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    A política de retenção especifica o período de armazenamento de um backup. Você pode especificar políticas de retenção diferentes com base em quando o backup é feito.
7. Clique em **Trabalhos** para exibir a lista de trabalhos de **Configurar Proteção**.

    ![Configurar o trabalho de proteção](./media/backup-azure-vms/protect-configureprotection.png)

    Agora que a política foi estabelecida, vá para a próxima etapa e execute o backup inicial.

## <a name="initial-backup"></a>Backup inicial
Após uma máquina virtual ser protegida com uma política, você poderá exibir essa relação na guia **Itens Protegidos** . Até que o backup inicial ocorra, o **Status de Proteção** será mostrado como **Protegido – (backup inicial pendente)**. Por padrão, o primeiro backup agendado é o *backup inicial*.

![Backup pendente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para começar o backup inicial agora:

1. Na página **Itens Protegidos**, clique em **Fazer Backup Agora** na parte inferior da página.
    ![Ícone de Fazer Backup Agora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    O serviço de Backup do Azure cria um trabalho de backup para a operação de backup inicial.
2. Clique na guia **Trabalhos** para exibir a lista de trabalhos.

    ![Backup em andamento](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Após a conclusão do backup inicial, o status da máquina virtual na guia **Itens Protegidos** é *Protegida*.

    ![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > O backup de máquinas virtuais é um processo local. Você não pode fazer backup de máquinas virtuais de uma região em um cofre de backup em outra região. Assim, para todas as regiões do Azure que tenham VMs que precisem de backup, pelo menos um cofre de backup deverá ser criado nessa região.
   >
   >

## <a name="next-steps"></a>Próximas etapas
Agora que você já fez um backup de uma VM, há várias etapas subsequentes pode poderiam ser interessantes. A etapa mais lógica é se familiarizar com a restauração de dados para uma máquina virtual. No entanto, há tarefas de gerenciamento que o ajudarão a entender como manter os dados seguros e minimizar os custos.

* [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
* [Diretrizes de solução de problemas](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

