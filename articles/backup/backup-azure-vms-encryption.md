---
title: "VMs criptografadas de backup e restauração usando o Backup do Azure"
description: "Este artigo fala sobre a experiência de backup e restauração para VMs criptografada usando a Azure Disk Encryption."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3c0c13aebbf325bc1f03a39a456c8eef1551c64a
ms.contentlocale: pt-br
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Como fazer backup e restaurar máquinas virtuais criptografadas com o Backup do Azure
Este artigo fala sobre as etapas para fazer backup e restauração de máquinas virtuais usando o Backup do Azure. Ele também oferece detalhes sobre os cenários com suporte, os pré-requisitos e as etapas de solução de problemas para casos de erro.

## <a name="supported-scenarios"></a>Cenários com suporte

 * O backup e a restauração de VMs criptografadas têm suporte somente para as máquinas virtuais implantadas pelo Resource Manager. Não há suporte para as máquinas virtuais Clássicas. <br>
 * Há suporte para máquinas virtuais com Windows e Linux usando a Azure Disk Encryption, que usa o recurso padrão do setor BitLocker do Windows e o DM-Crypt do Linux para fornecer criptografia de discos. <br>
 * A tabela a seguir captura os cenários com suporte para VMs criptografadas por somente Chave de Criptografia BitLocker (BEK) e BEK + Chave de Criptografia de Chave (KEK).
 
 
   |  | VMs BEK + KEK | VMs somente BEK |
   | --- | --- | --- |
   | **VMs não gerenciadas**  | Sim | Sim  |
   | **VMs gerenciadas**  | Sim | Não  |

## <a name="prerequisites"></a>Pré-requisitos
1. A máquina virtual foi criptografada usando a [Azure Disk Encryption](../security/azure-security-disk-encryption.md). 
2. O cofre de serviços de recuperação foi criado e a replicação de armazenamento definida usando as etapas mencionadas no artigo [Preparar seu ambiente para backup](backup-azure-arm-vms-prepare.md).
3. O Backup do Azure recebeu [permissões para acessar o cofre de chaves](#provide-permissions-to-azure-backup) que contém chaves, segredos para VMs criptografadas.

## <a name="backup-encrypted-vm"></a>Fazer backup da VM criptografada
Use as etapas a seguir para definir o objetivo do backup, definir a política, configurar itens e disparar o backup.

### <a name="configure-backup"></a>Configurar o backup
1. Se você já tiver um cofre dos Serviços de Recuperação aberto, vá para a próxima etapa. Se você não tiver um cofre dos Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu Hub, clique em **Procurar**.

   * Na lista de recursos, digite **Serviços de Recuperação**.
   * Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando vir a opção **Cofres de Serviços de Recuperação**, clique nela.

      ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     A lista de cofres de Serviços de Recuperação aparecerá. Na lista de cofres de Serviços de Recuperação, selecione um cofre.

     O painel de cofres selecionados será aberto.
2. Na lista de itens que aparece no cofre, clique em **Backup** para começar a fazer o backup da VM criptografada.

      ![Abrir a folha Backup](./media/backup-azure-vms-encryption/select-backup.png)
3. Clique em **Meta de backup** primeiro para selecionar a meta de backup.

      ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Na primeira etapa de seleção da Meta de Backup, defina **Onde está a carga de trabalho em execução** no Azure, **Do que você deseja fazer backup** na máquina Virtual e clique **OK**.

   Isso leva à segunda etapa de seleção da política de Backup.

   ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Na segunda etapa de seleção da política, selecione a política de backup que você deseja aplicar no cofre e clique em **OK**.

      ![Selecionar a política de backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Os detalhes da política padrão estão listados nos detalhes. Se você quiser criar uma política, selecione **Criar Nova** no menu suspenso. Quando você clicar em **OK**, a política de backup será associada ao cofre.

    Em seguida, escolha as VMs para associar ao cofre.
6. Escolha as máquinas virtuais para associar à política especificada e clique em **OK**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Essa página mostra uma mensagem sobre o cofre de chaves associado às VMs criptografadas selecionadas. O serviço de backup requer acesso somente leitura às chaves e segredos no cofre de chaves. Ele usa essas permissões para fazer backup da chave e do segredo, junto com as VMs associadas. **Você deve fornecer permissões para que o serviço de backup acesse o cofre de chaves para que os backups funcionem**. Você pode fornecer essas permissões usando as [etapas mencionadas na seção abaixo](#provide-permissions-to-azure-backup).

      ![Mensagem de VMs criptografada](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      Agora que você definiu todas as configurações para o cofre, clique em Habilitar Backup na parte inferior da página. Habilitar o Backup implanta a política para o cofre e as VMs.
8. A próxima fase de preparação será instalar o Agente de VM ou verificar se ele está instalado. Para fazer o mesmo, use as etapas mencionadas no artigo [Preparar seu ambiente para backup](backup-azure-arm-vms-prepare.md).

### <a name="triggering-backup-job"></a>Disparar o trabalho de backup
Use as etapas mencionadas no artigo [Fazer backup das VMs do Azure no cofre de serviços de recuperação](backup-azure-arm-vms.md) para disparar o trabalho de backup.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuar os backups de VMs cujo backup já foi realizado com a criptografia habilitada  
Se você tiver VMs cujo backup já estiver sendo realizado no cofre de serviços de recuperação e tiverem sido habilitadas para criptografia em um momento posterior, você deverá conceder permissões para o serviço de backup acessar o cofre de chaves para que os backups continuem. Você pode fornecer essas permissões usando as [etapas na seção abaixo](#provide-permissions-to-azure-backup) ou usando as etapas do PowerShell mencionadas na seção **Habilitar Backup** da [documentação do PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-azure-backup"></a>Fornecer as permissões para o Backup do Azure
Use as etapas a seguir para fornecer as permissões adequadas para que o Backup do Azure acesse o cofre de chaves e realize o backup de VMs criptografadas:
1. Selecione **Mais serviços** e pesquise **Cofres de chaves**.

    ![Pesquisar cofre de chaves](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Na lista de cofres de chaves, selecione o cofre de chaves associado à VM criptografada da qual precisa ser feito o backup.

     ![Selecionar o cofre de chaves](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Clique em **Políticas de acesso** e, em seguida, **Adicionar nova**.

    ![Adicionar política de acesso](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Clique em **Selecionar entidade** e digite **Serviço de gerenciamento de backup** na barra de pesquisa. 

    ![Pesquisar serviço de backup](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Selecione o **Serviço de Gerenciamento de Backup** e clique no botão Selecionar.

    ![Selecionar o serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Selecione **Backup do Azure** na lista suspensa Configurar usando o modelo. Ele preenche previamente as permissões necessárias na lista suspensa Permissões de chave e Permissões de segredo. Se a VM é criptografada usando **somente BEK**, as permissões apenas para segredos são necessárias, portanto você deve remover a seleção de permissões de chave.

    ![Selecionar backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Clique em **OK**. Observe que o Serviço de Gerenciamento de Backup é adicionado em Políticas de Acesso. 

    ![Política de acesso do serviço de backup](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Clique em **Salvar**. Isso concederá as permissões necessárias ao Backup do Azure.

    ![Política de acesso do serviço de backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Quando as permissões tiverem sido concedidas com êxito, você poderá continuar com a habilitação do backup para VMs criptografadas.

## <a name="restore-encrypted-vm"></a>Restauração a VM criptografada
Para restaurar a VM criptografada, primeiro restaure discos, usando as etapas mencionadas na seção **restauração do backup discos** na [restaurar configuração da VM escolhendo](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Depois disso, você pode usar uma das seguintes opções:
* Use as etapas do PowerShell mencionadas em [Criar uma VM de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar uma VM completa de discos restaurados.
* OU [Usar modelo gerado como parte de discos de restauração](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm) para criar VMs de discos restaurados. Modelos podem ser usados somente para pontos de recuperação criados após 26 de abril de 2017.

## <a name="troubleshooting-errors"></a>Solucionar erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
|Backup | O Serviço de Backup do Azure não tem permissões suficientes para o Key Vault para Backup de máquinas virtuais criptografadas | A máquina virtual deve ser criptografado usando a Chave de Criptografia BitLocker e a Chave de Criptografia. Depois disso, o backup deve ser habilitado.  O serviço de backup deve ter essas permissões concedidas usando as [etapas mencionadas na seção acima](#provide-permissions-to-azure-backup) ou usando as etapas do PowerShell, mencionadas na seção **Habilitar Proteção** da documentação do PowerShell em [Usar cmdlets AzureRM.RecoveryServices.Backup para fazer backup de máquinas virtuais](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Backup |Falha na validação pois a máquina virtual é criptografada somente com BEK. Os backups podem ser habilitados somente para máquinas virtuais criptografadas com BEK e KEK. |A máquina virtual deve ser criptografada usando BEK e KEK. Primeiro descriptografe a VM e criptografe-a usando BEK e KEK. Habilite o backup após a VM ser criptografada usando BEK e KEK. Saiba mais sobre como você pode [descriptografar e criptografar a VM](../security/azure-security-disk-encryption.md)  |
| Restaurar |Você não poderá restaurar essa VM criptografada pois o cofre de chaves associado a essa VM não existe. |Crie o cofre de chaves usando [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md). Veja o artigo [Restaurar chave do Cofre de Chaves e o segredo usando o Backup do Azure Backup](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se não estiverem presentes. |
| Restaurar |Você não poderá restaurar essa VM criptografada pois a chave e o segredo associados a essa VM não existem. |Veja o artigo [Restaurar chave do Cofre de Chaves e o segredo usando o Backup do Azure Backup](backup-azure-restore-key-secret.md) para restaurar a chave e o segredo, se não estiverem presentes. |
| Restaurar |O Serviço de Backup não tem autorização para acessar recursos em sua assinatura. |Conforme mencionado acima, primeiro restaure os discos, usando as etapas mencionadas na seção **restauração do backup discos** na [restaurar configuração da VM escolhendo](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Depois, use o PowerShell para [Criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |


