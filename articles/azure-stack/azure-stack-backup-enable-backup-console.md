---
title: Habilitar o backup do portal de administração para o Azure Stack | Microsoft Docs
description: Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que o Azure Stack pode ser restaurado se houver uma falha.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 98f793b7d94cd554d426a0eec30d8bb4553d3d81
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105396"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitar o backup do portal de administração para o Azure Stack
Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que o Azure Stack pode gerar backups de infraestrutura. O parceiro de hardware pode usar esses backups para restaurar seu ambiente usando a recuperação no caso de nuvem [uma falha catastrófica](./azure-stack-backup-recover-data.md). O objetivo de recuperação de nuvem é garantir que seus operadores e usuários podem fazer logon novamente no portal após a conclusão da recuperação. Os usuários terão suas assinaturas restauradas incluindo permissões de acesso baseado em função e funções, originais planos, ofertas e computação definida anteriormente, armazenamento, cotas de rede e segredos do Key Vault.

No entanto, o serviço de Backup de infraestrutura não fazer backup de VMs de IaaS, as configurações de rede e recursos de armazenamento, como contas de armazenamento, blobs, tabelas e assim por diante, para que os usuários que fazem logon depois de concluir a recuperação de nuvem não verão qualquer um dos seus previamente existente recursos. Plataforma como serviço (PaaS) recursos e dados também não passam por backup pelo serviço. 

Os administradores e usuários são responsáveis por fazer backup e restaurar os recursos de IaaS e PaaS separadamente dos processos de backup de infraestrutura. Para obter informações sobre como fazer backup de recursos de IaaS e PaaS, consulte os links a seguir:

- [Máquinas virtuais](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Habilitar ou reconfigurar o backup

1. Abra o [portal de administração do Azure Stack](azure-stack-manage-portals.md).
2. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.
3. Digite o caminho para o **local de armazenamento de Backup**. Use uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos. Para o serviço, você pode usar um endereço IP. Para garantir a disponibilidade dos dados de backup após um desastre, o dispositivo deve estar em um local separado.

    > [!Note]  
    > Se seu ambiente oferece suporte a resolução de nome de rede de infraestrutura do Azure Stack para seu ambiente corporativo, você pode usar um FQDN em vez do IP.

4. Tipo de **nome de usuário** usando o domínio e o nome de usuário com acesso suficiente para ler e gravar arquivos. Por exemplo, `Contoso\backupshareuser`.
5. Tipo de **senha** para o usuário.
6. Digite a senha novamente até **Confirmar senha**.
7. O **frequência nas horas** determina a frequência com que os backups são criados. O valor padrão é 12. O Agendador dá suporte a um máximo de 12 e um mínimo de 4. 
8. O **período de retenção em dias** determina quantos dias de backups são preservados em local externo. O valor padrão é 7. O Agendador dá suporte a um máximo de 14 e um mínimo de 2. Backups mais antigos que o período de retenção são automaticamente excluídos do local externo.

    > [!Note]  
    > Se você quiser arquivar backups mais antigos que o período de retenção, certifique-se os arquivos de backup antes que o Agendador exclui os backups. Se você reduzir o período de retenção de backup (por exemplo, de 7 dias para 5 dias), o Agendador excluirá todos os backups mais antigos que o novo período de retenção. Verifique se que você está okey com os backups excluídos antes de atualizar esse valor. 

9. Nas configurações de criptografia de fornecer um certificado na caixa de arquivo. cer de certificado. Arquivos de backup são criptografados usando essa chave pública no certificado. Você deve fornecer um certificado que contém apenas a parte da chave pública quando você configura as configurações de backup. Depois que você configurar este certificado pela primeira vez ou girar o certificado no futuro, você pode exibir somente a impressão digital do certificado. Você não pode baixar ou exibir o arquivo de certificado carregado. Para criar o arquivo de certificado, execute o seguinte comando do PowerShell para criar um certificado autoassinado com as chaves públicas e privadas e exportar um certificado com apenas a parte de chave pública.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 e acima**: O Azure Stack aceita um certificado para criptografar os dados de backup de infraestrutura. Certifique-se de armazenar o certificado com a chave pública e privada em um local seguro. Por motivos de segurança, não é recomendável que você usa o certificado com as chaves públicas e privadas para definir configurações de backup. Para obter mais informações sobre como gerenciar o ciclo de vida desse certificado, consulte [práticas recomendadas do serviço de Backup de infraestrutura](azure-stack-backup-best-practices.md).
   > 
   > **1811 ou anterior**: O Azure Stack aceita uma chave simétrica para criptografar os dados de backup de infraestrutura. Use o [AzsEncryptionKey64 novo cmdlet para criar uma chave](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Depois de atualizar do 1811 1901, as configurações de backup manterá a chave de criptografia. Recomendação é atualizar as configurações de backup para usar um certificado. Suporte à criptografia de chave é preterida. Você terá pelo menos 3 versões ao atualizar as configurações para usar um certificado. 

10. Selecione **Okey** para salvar suas configurações de backup do controlador.

![O Azure Stack - configurações de controlador de Backup](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Iniciar o backup
Para iniciar um backup, clique em **fazer Backup agora** para iniciar um backup sob demanda. Um backup sob demanda não modificará o tempo para o próximo backup agendado. Depois que a tarefa for concluída, você pode confirmar as configurações no **Essentials**:

![O Azure Stack - backup sob demanda](media/azure-stack-backup/scheduled-backup.png)

Você também pode executar o cmdlet do PowerShell **AzsBackup início** em seu computador de administração do Azure Stack. Para obter mais informações, consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Habilitar ou desabilitar backups automáticos
Os backups são agendados automaticamente quando você habilita o backup. Você pode verificar o próximo horário de backup de agendamento **Essentials**. 

![O Azure Stack - backup sob demanda](media/azure-stack-backup/on-demand-backup.png)

Se você precisar desabilitar futuros backups agendados, clique em **desabilitar Backups automáticos**. Desabilitantes backups automáticos manterão as configurações de backup configuradas e manterão o agendamento de backup. Essa ação simplesmente informa ao Agendador para ignorar os backups futuros. 

![O Azure Stack - desabilitar agendado backups](media/azure-stack-backup/disable-auto-backup.png)

Confirme que os backups futuros agendados foram desabilitados no **Essentials**:

![O Azure Stack - confirmar backups foram desabilitados](media/azure-stack-backup/confirm-disable.png)

Clique em **habilitar Backups automáticos** para informar o Agendador para iniciar backups futuros no horário agendado. 

![O Azure Stack - ativar programada backups](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Se você tiver configurado o backup de infraestrutura antes de atualizar para 1807, backups automáticos serão desabilitados. Dessa forma os backups iniciados pelo Azure Stack não entrem em conflito com iniciado por uma tarefa externa do mecanismo de agendamento de backups. Depois de desabilitar qualquer Agendador de tarefas externos, clique em **habilitar Backups automáticos**.

## <a name="update-backup-settings"></a>Atualizar as configurações de backup
A partir de 1901, suporte para chave de criptografia é preterido. Se você estiver configurando o backup pela primeira vez no 1901, você deve usar um certificado. O Azure Stack dá suporte a chave de criptografia somente se a chave estiver configurada antes de atualizar para 1901. Modo de compatibilidade com versões anteriores continuarão para três versões. Depois disso, as chaves de criptografia não terá suporte. 

### <a name="default-mode"></a>Modo padrão
Nas configurações de criptografia, se você estiver configurando o backup da infra-estrutura pela primeira vez após instalar ou atualizar para 1901, você deve configurar o backup com um certificado. Não há suporte para o uso de uma chave de criptografia. 

Para atualizar o certificado usado para criptografar dados de backup, você pode carregar um novo. CER com a parte pública da chave de arquivo e selecione Okey para salvar as configurações. 

Novos backups serão começar a usar a chave pública no certificado novo. Não há nenhum impacto para todos os backups existentes criados com o certificado anterior. Certifique-se de manter o certificado mais antigo em torno de em um local seguro no caso de você precisar dela para recuperação na nuvem.

![O Azure Stack - impressão digital do certificado de modo de exibição](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Com versões anteriores modo de compatibilidade
Se você tiver configurado o backup antes de atualizar para 1901, as configurações são transmitidas com nenhuma alteração no comportamento. Nesse caso, chave de criptografia tem suporte para versões anteriores compatibilidade. Você tem a opção atualizar a chave de criptografia ou alternar para usar um certificado. Você terá pelo menos três versões continuar atualizando a chave de criptografia. Use esse tempo para fazer a transição para um certificado. Para criar um novo uso de chave de criptografia a [cmdlet New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![O Azure Stack - usando a chave de criptografia no modo de compatibilidade com versões anteriores](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Atualização de chave de criptografia para o certificado é uma operação unidirecional. Depois de fazer essa alteração, você não pode alternar novamente para chave de criptografia. Todos os backups existentes permanecerão criptografados com a chave de criptografia anterior. 

![O Azure Stack - usar o certificado de criptografia no modo de compatibilidade com versões anteriores](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Próximas etapas

Aprenda a executar um backup. Consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Saiba como verificar se o backup foi executado. Consulte [backup confirmar concluído no portal de administração](azure-stack-backup-back-up-azure-stack.md)
