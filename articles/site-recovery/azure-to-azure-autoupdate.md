---
title: Atualização automática do Serviço de Mobilidade no Azure para a recuperação de desastres do Azure | Microsoft Docs
description: Fornece uma visão geral da atualização automática do Serviço de Mobilidade, durante a replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: f31fccd2bf6d0daae03b025b53a41a0fad4ce2ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210124"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Atualização automática do Serviço de Mobilidade no Azure para a replicação do Azure

O Azure Site Recovery tem um ritmo de lançamentos mensais nos quais são adicionados recursos novos ou melhorias a recursos existentes, assim como são corrigidos eventuais problemas conhecidos. Isso significa que, para permanecer atualizado com o serviço, você precisa planejar a implantação desses patches mensalmente. Para evitar a sobrecarga associada à atualização, os usuários podem escolher permitir que o Site Recovery gerencie as atualizações dos componentes. Conforme detalhado na [referência de arquitetura](azure-to-azure-architecture.md) do Azure para a recuperação de desastres do Azure, o Serviço de Mobilidade é instalado em todas as máquinas virtuais do Azure para que a replicação seja habilitada durante a replicação das máquinas virtuais de uma região do Azure para outra. Depois que você habilita a atualização automática, a extensão Serviço de mobilidade é atualizada a cada nova versão. Este documento detalha o seguinte:

- Como funciona o trabalho de atualização automática?
- Habilitar as atualizações automáticas
- Problemas comuns e solução de problemas
 
## <a name="how-does-automatic-update-work"></a>Como funciona o trabalho de atualização automática

Quando você permite que o Site Recovery gerencie atualizações, um runbook global (que é usado pelos serviços do Azure) é implantado por meio de uma conta de automação, que é criada na mesma assinatura que o cofre. Uma conta de automação é usada para um cofre específico. O runbook verifica cada VM em um cofre para a qual são ativadas atualizações automáticas e inicia uma atualização da extensão do Serviço de Mobilidade se uma versão mais recente estiver disponível. O agendamento padrão do runbook ocorre diariamente às 12h de acordo com o fuso horário da localização geográfica da máquina virtual replicada. O agendamento do runbook também pode ser modificado pelo usuário por meio da conta de automação, se necessário. 

> [!NOTE]
> A habilitação de atualizações automáticas não exige a reinicialização das VMs do Azure nem afeta a replicação em andamento.

> [!NOTE]
> A cobrança pelos trabalhos usados pela conta de automação baseia-se no número de minutos de tempo de execução do trabalho usados no mês e, por padrão, 500 minutos são incluídos como unidades gratuitas em uma conta de automação. A execução do trabalho diariamente equivale a **alguns segundos até cerca de um minuto** e será **coberta pelos créditos gratuitos**.

UNIDADES GRATUITAS INCLUÍDAS (POR MÊS)**   PREÇO Tempo de execução do trabalho    500 minutos ₹0,14/minuto

## <a name="enable-automatic-updates"></a>Habilitar as atualizações automáticas

Você pode optar por permitir que o Site Recovery gerencie as atualizações das seguintes maneiras:

- [Como parte da etapa habilitar replicação](#as-part-of-the-enable-replication-step)
- [Ativar/desativar as configurações de atualização de dentro do cofre](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Como parte da etapa habilitar replicação:

Quando você habilita a replicação para uma máquina virtual da iniciando [no modo de exibição da máquina virtual](azure-to-azure-quickstart.md), ou [no cofre de serviços de recuperação](azure-to-azure-how-to-enable-replication.md), você obterá uma opção para escolher permitir que o Site Recovery gerencie as atualizações para a extensão do Site Recovery ou gerencie manualmente o mesmo.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Ativar/desativar as configurações de atualização de dentro do cofre

1. Dentro do cofre, navegue até **Gerenciar**-> **Infraestrutura do Site Recovery**
2. Em **Para as máquinas virtuais do Azure**-> **Configurações de atualização da extensão**, clique na alternância para escolher se deseja permitir *ASR para gerenciar atualizações* ou *gerenciar manualmente*. Clique em **Salvar**.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Quando você escolhe *Permitir ASR para gerenciar*, a configuração é aplicada a todas as máquinas virtuais no cofre correspondente.


> [!Note] 
> As duas opções enviarão uma notificação sobre qual conta de automação está sendo usada para gerenciar as atualizações. Se você estiver habilitando esse recurso pela primeira vez em um cofre, uma nova conta de automação será criada. Todas as replicações de habilitação subsequentes no mesmo cofre usarão aquela criada anteriormente.

### <a name="manage-manually"></a>Gerenciar manualmente

1. Se há novas atualizações disponíveis para o serviço de mobilidade instalado em suas VMs do Azure, você verá uma notificação que lê a "atualização do agente de replicação da recuperação do novo Site está disponível. Clique para instalar."

     ![Janela Itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Clique na notificação para abrir a página de seleção de máquina virtual.
4. Selecione as máquinas virtuais nas quais deseja atualizar o serviço de mobilidade e selecione **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada uma das máquinas virtuais selecionadas.


## <a name="common-issues--troubleshooting"></a>Problemas comuns e solução de problemas

Se houver um problema com as atualizações automáticas, você será notificado em “Problemas de configuração” no painel do cofre. 

Caso você tenha tentado habilitar as atualizações automáticas, mas não teve êxito, consulte a solução de problemas abaixo.

**Erro**: Você não tem permissões para criar uma conta Executar como do Azure (entidade de serviço) e conceder a função Colaborador à entidade de serviço. 
- Ação recomendada: Certifique-se de que a conta registrada está atribuída ao 'Colaborador' e repita a operação. Veja [este](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) documento para obter mais informações sobre como atribuir as permissões corretas.
 
Depois que as atualizações automáticas forem ativadas, a maioria dos problemas pode ser reparada pelo serviço do Site Recovery e exige que você clique no botão “**Reparar**”.

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Caso do botão de reparo não esteja disponível, consulte a mensagem de erro exibida no painel de configurações de extensão.

 - **Erro**: A conta Executar como não tem permissão para acessar o recurso de serviços de recuperação.

    **Ação recomendada**: Exclua e, em seguida, [recrie a conta Executar como](https://docs.microsoft.com/azure/automation/automation-create-runas-account) ou certifique-se de que o aplicativo do Azure Active Directory da conta Executar como de Automação tenha acesso ao recurso dos serviços de recuperação.

- **Erro**: A conta Executar como não foi localizada. Um deles foi excluído ou não criado - o aplicativo do Azure Active Directory, a entidade de serviço, a função, o ativo do certificado de automação, o ativo de conexão de automação - ou a impressão digital não é idêntica entre o certificado e a conexão. 

    **Ação recomendada**: Exclua e, em seguida, [recrie a conta Executar como](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
