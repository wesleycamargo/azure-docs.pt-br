---
title: "Definir configurações de replicação para o Azure Site Recovery | Microsoft Docs"
description: "Descreve como implantar o Site Recovery para orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V em nuvens do VMM para o Azure."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Gerenciar a política de replicação para VMware no Azure


## <a name="create-a-new-replication-policy"></a>Criar uma nova política de replicação

1. Clique em 'Gerenciar-> Infraestrutura do Site Recovery' no menu à esquerda. 
2. Selecione 'Políticas de replicação' na seção 'Para VMware e Máquinas físicas'.
3. Clique em '+ Política de replicação' na parte superior.

    ![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Insira o nome da política.

5. Em Limite de RPO, especifique o limite de RPO. Alertas serão gerados quando a replicação contínua excede esse limite.
6. Em Retenção do ponto de recuperação, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela. 

    > [!NOTE] 
    > Até 24 horas, a retenção tem suporte para máquinas replicadas para o armazenamento premium e a retenção de 72 horas tem suporte para máquinas replicadas para o armazenamento padrão.
    
    > [!NOTE] 
    > Uma política de replicação para failback será criada automaticamente.

7. Em Frequência do instantâneo consistente com o aplicativo, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados.

8. Clique em 'OK'. A política deve ser criada em cerca de 30 segundos a um minuto.

![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Associar o servidor de configuração com a política de replicação
1. Clique na política de replicação à qual você deseja associar o servidor de configuração.
2. Clique em 'Associar' na parte superior.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Selecione o ‘Servidor de configuração' na lista de servidores.
4. Clique em OK. O servidor de configuração deve ser associado em cerca de um a dois minutos.

![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Editar política de replicação
1. Clique na política de replicação para a qual você deseja editar as configurações de replicação.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Clique em ‘Editar Configurações’ na parte superior.
![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Altere as configurações com base nas suas necessidades.
4. Clique em 'Salvar' na parte superior. A política deve ser salva em cerca de 2 a 5 minutos, dependendo de quantas VMs estejam usando essa política de replicação.

![Criar política de replicação](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Desassociar o servidor de configuração da política de replicação
1. Clique na política de replicação à qual você deseja associar o servidor de configuração.
2. Clique em 'Desassociar' na parte superior.
3. Selecione o ‘Servidor de configuração' na lista de servidores.
4. Clique em OK. O servidor de configuração deve ser desassociado em cerca de um a dois minutos.
    
    > [!NOTE] 
    > Não é possível desassociar um servidor de configuração se houver pelo menos um item duplicado usando a política. Verifique se não existem itens replicadas usando a política antes de desassociar o servidor de configuração.

## <a name="delete-replication-policy"></a>Excluir política de replicação 

1. Clique na política de replicação que você deseja excluir.
2. Clique em Excluir. A política deve ser excluída em cerca de 30 segundos a um minuto.

    > [!NOTE] 
    > Você não poderá excluir uma política de replicação se ela tiver pelo menos um servidor de configuração associado a ela. Verifique se não há itens replicados usando a política e exclua todos os servidores de configuração associados antes de excluir a política.


<!--HONumber=Jan17_HO4-->


