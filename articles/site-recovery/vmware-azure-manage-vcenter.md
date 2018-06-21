---
title: " Gerenciar servidores VMware vCenter no Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter no Azure Site Recovery.
author: AnoopVasudavan
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: anoopkv
ms.openlocfilehash: 48b6cf9b90b429520df435aee00f57ea7b588748
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284991"
---
# <a name="manage-vmware-vcenter-servers"></a>Gerenciar servidores VMware vCenter 

Este artigo discute as várias operações do Site Recovery que podem ser executadas em um VMware vCenter. Verifique os [pré-requisitos](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

A Site Recovery precisa de acesso ao VMware para que o servidor de processo descubra automaticamente as máquinas virtuais e para failover e failback de máquinas virtuais. Crie uma conta de acesso da seguinte maneira:

1. Faça logon na máquina do servidor de configuração.
2. Abra e inicie o cspsconfigtool.exe usando o atalho da Área de Trabalho.
3. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Forneça os detalhes da conta e clique em **OK** para adicioná-la.  A conta deve ter os privilégios resumidos na tabela a seguir. 

Demora cerca de 15 minutos para que as informações de conta sejam sincronizadas com o serviço do Site Recovery.

### <a name="account-permissions"></a>Permissões da conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta automática/migrar (sem failback)** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|
|**Replicação/failover** | Você precisa de pelo menos um usuário somente leitura| Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Sem acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e atribua a função a um usuário ou grupo do VMware | Objeto de Data Center –> Propagar para o Objeto Filho, role=AzureSiteRecoveryRole<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adicionar um servidor VMware ao cofre

1. No portal do Azure, abra seu cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
2. Na página **Detalhes**, clique em **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificar as credenciais

Modifique as credenciais usadas para se conectar ao servidor vCenter ou ao host ESXi da seguinte maneira:

1. Faça logon no servidor de configuração e inicie o cspsconfigtool.exe por meio da área de trabalho.
2. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da nova conta e clique em **OK** para adicioná-la. A conta deve ter os privilégios listados [acima](#account-permissions).
4. No portal do Azure, abra o cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
5. Na página **Detalhes**, clique em **Atualizar Servidor**.
6. Após a conclusão do trabalho Atualizar Servidor, selecione o vCenter Server para abrir a página **Resumo** do vCenter.
7. Selecione a conta recém-adicionada no campo **Conta do host do servidor vCenter/vSphere** e clique em **Salvar**.

    ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Excluir um servidor vCenter

1. No portal do Azure, abra seu cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
2. Na página **Detalhes**, selecione o servidor vCenter.
3. Clique no botão **Excluir**.

  ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Caso precise modificar o endereço IP, o FQDN ou a porta do vCenter, precisará excluir o servidor vCenter e adicioná-lo novamente ao portal.
