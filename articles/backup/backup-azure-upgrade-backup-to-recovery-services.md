---
title: "Atualizar um cofre de Backup para um cofre de Serviços de Recuperação (Versão Prévia) | Microsoft Docs"
description: "Comandos e informações de suporte para atualizar um cofre de Backup do Azure em um cofre de Serviços de Recuperação."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação

Este artigo explica como atualizar um cofre de Backup para um cofre de Serviços de Recuperação. O processo de atualização não afeta nenhum trabalho de backup em execução, e nenhum dado de backup é perdido. Os principais motivos para atualizar um cofre de Backup para um cofre de Serviços de Recuperação são:
- Todos os recursos de um cofre de Backup são mantidos em um cofre de Serviços de Recuperação.
- Cofres dos Serviços de Recuperação têm mais recursos do que os cofres de Backup, incluindo melhor segurança, monitoramento integrado, restaurações mais rápidas e restaurações no nível do item, com gerenciamento de itens de backup em um portal aprimorado e simplificado.
-  Novos recursos serão lançados apenas para cofres dos Serviços de Recuperação.

## <a name="impact-to-operations-during-upgrade"></a>Impacto às operações durante a atualização

Ao atualizar um cofre de Backup para um cofre de Serviços de Recuperação, não há nenhum impacto sobre suas operações de plano de dados. Todos os trabalhos de backup continuam como normal e quaisquer trabalhos de restauração ativos continuam sem interrupção. Durante a atualização, operações de gerenciamento incorrerem em um curto tempo de inatividade e você não pode proteger novos itens nem criar trabalhos de backups ad hoc. Trabalhos de restauração para VMs de IaaS não são executados durante a atualização. <MG>: Qual é a diferença entre os trabalhos de restauração ativa que serão acionados da maneira normal e os trabalhos de restauração da VM de IaaS?
A atualização do cofre leva menos de uma hora para ser concluída. Quando tiver concluído, um cofre de Serviços de Recuperação substitui o cofre de Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Alterações à automação e à ferramenta após a atualização

Ao preparar sua infraestrutura para a atualização do cofre, você deve atualizar sua automação ou ferramentas existentes para garantir que continue funcionando após a atualização.
Consulte as referências de cmdlets do PowerShell para o [Modelo de implantação do Service Manager](backup-client-automation-classic.md) e o [Modelo de implantação do Resource Manager](backup-client-automation.md)


## <a name="before-you-upgrade"></a>Antes de atualizar

Verifique os problemas a seguir antes de atualizar seus cofres de Backup para os cofres do serviço de recuperação.

- **Versão mínima do agente**: para atualizar seu cofre, verifique se o agente do MARS (Serviços de Recuperação do Microsoft Azure) é pelo menos a versão 2.0.9070.0. Se o agente do MARS for mais antigo que 2.0.9070.0, atualize o agente antes de iniciar o processo de atualização.
- **Modelo de cobrança com base em instância**: cofres do Serviço de Recuperação dão suporte apenas ao modelo de cobrança com base em instância. Se você tiver um cofre de backup que esteja usando o antigo modelo de cobrança com base em Armazenamento, converta o modelo de cobrança durante a atualização.
- **Nenhuma operação de configuração de backup em andamento**: durante a atualização, o acesso ao plano de gerenciamento é restrito. Conclua todas as ações do plano de gerenciamento e inicie a atualização.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Usando scripts do PowerShell para atualizar seus cofres

Você pode usar scripts do PowerShell para atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Verifique se você tem os componentes necessários do PowerShell para disparar a atualização do cofre. Scripts do PowerShell para os cofres de Backup não funcionam para cofres dos Serviços de Recuperação. Prepare seu ambiente para atualizar os cofres:

1. Instale ou atualize o [WMF (Windows Management Framework) para a versão 5](https://www.microsoft.com/download/details.aspx?id=50395) ou superior.
2. Baixar e [instalar o Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi).
3. Atualize os módulos do PowerShell:
  - Para Windows Server:
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - Para Windows 10
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. Baixe o [Script do PowerShell](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1) para atualizar os cofres.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

Use o script a seguir para atualizar seus cofres. O exemplo de script a seguir tem explicações dos parâmetros.

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID – o número de ID da assinatura de cofre que está sendo atualizado.
VaultName – o nome do cofre de Backup que está sendo atualizado.
Local – local do cofre que está sendo atualizado.
ResourceType – usar BackupVault.
TargetResourceGroupName – uma vez que você está atualizando o cofre para uma implantação baseada no Resource Manager, especifique um Grupo de Recursos. Você pode usar um Grupo de Recursos existente ou criar um fornecendo um novo nome. Se você digitar incorretamente o nome de um Grupo de Recursos, poderá criar um novo Grupo de Recursos. Para saber mais sobre Grupos de Recursos, leia esta [visão geral sobre Grupos de Recursos](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Nomes de Grupo de Recursos têm restrições. Siga as diretrizes; não as seguir pode provocar falha das atualizações do cofre.
>
>

O trecho de código a seguir é um exemplo de como o comando do PowerShell deve ser:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Você também pode executar o script sem parâmetros e será solicitado a fornecer entradas para todos os parâmetros necessários.

O script do PowerShell solicita que você insira suas credenciais. Insira suas credenciais duas vezes: uma vez para a conta do Service Manager e outra para a conta do Resource Manager.

### <a name="pre-requisites-checking"></a>Verificação de pré-requisitos
Depois de inserir suas credenciais do Azure, o Azure verifica se o seu ambiente atende aos seguintes pré-requisitos:

- **Versão mínima do agente** – atualizar os cofres de Backup para cofres dos Serviços de Recuperação requer que o agente do MARS tenha pelo menos a versão 2.0.9070. Se você tiver itens registrados para um cofre de Backup com um agente anterior à versão 2.0.9070, a verificação de pré-requisitos falhará. Se a verificação de pré-requisitos falhar, atualize o agente e tente atualizar o cofre novamente. Você pode baixar a versão mais recente do agente de [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Tarefas de configuração em andamento**: se alguém estiver configurando o trabalho para um cofre de Backup definido para ser atualizado ou estiver registrando um item, a verificação de pré-requisitos falhará. Conclua a configuração ou conclua o registro do item e, em seguida, inicie o processo de atualização do cofre.
- **Modelo de cobrança com base em armazenamento**: cofres dos Serviços de Recuperação dão suporte ao modelo de cobrança baseado em instância. Se você executar a atualização do cofre em um cofre de Backup que use o modelo de cobrança baseado em armazenamento, deverá atualizar o modelo de cobrança junto com o cofre. Caso contrário, poderá atualizar o modelo de cobrança primeiro e então executar a atualização do cofre.
- Identificar um Grupo de Recursos para o cofre de Serviços de Recuperação. Para aproveitar os recursos de implantação do Resource Manager, coloque um cofre de Serviços de Recuperação em um Grupo de Recursos. Se você não souber qual Grupo de Recursos usar, forneça um nome e o processo de atualização criará o Grupo de Recursos para você. O processo de atualização também associa o cofre ao novo Grupo de Recursos.

Depois que o processo de atualização termina de verificar os pré-requisitos, o processo solicita que você inicie a atualização do cofre. Depois de confirmar, o processo de atualização normalmente leva cerca de 15 a 20 minutos para ser concluído, dependendo do tamanho de seu cofre. Se você tiver um grande cofre, a atualização poderá levar até 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Gerenciando seus cofres dos Serviços de Recuperação

As telas a seguir mostram um novo cofre de Serviços de Recuperação, atualizado do cofre de Backup, no portal do Azure. A primeira tela mostra o painel do cofre que exibe as entidades principais para o cofre.

![exemplo de cofre dos Serviços de Recuperação atualizado de um cofre de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A segunda tela mostra os links de ajuda disponíveis para ajudá-lo a começar a usar o cofre de Serviços de Recuperação.

![links de ajuda na folha Início Rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**O plano de atualização afeta meus backups em andamento?**</br>
Não. Os backups em andamento continuam sem interrupções durante e após a atualização.

**O que acontecerá com meus cofres se eu não planejar atualizar em breve?**</br>
Uma vez que todos os novos recursos planejados para o cofre de Serviços de Recuperação e o uso do portal clássico serão preteridos, a Microsoft substituirá os cofres de Backup e o modelo do gerenciador de serviço. No futuro, a Microsoft disparará uma atualização automática de todos os cofres para o Cofre de Serviços de Recuperação. Até lá, os clientes controlam quando atualizar seus cofres.

**O que este plano de atualização significa para minhas ferramentas existentes?**</br>  
Atualizar suas ferramentas para o modelo de implantação do Resource Manager em que os cofres dos Serviços de Recuperação são baseados é uma das alterações mais importantes que você precisa considerar em seus planos de atualização.

**De quanto tempo será a inatividade?**</br>
Isso depende do número de recursos que estão sendo atualizados. Para implantações menores (algumas dezenas de instâncias protegidas), a atualização inteira deve levar menos de 20 minutos. Para implantações maiores, deve levar um máximo de uma hora.

**Posso reverter após a atualização?**</br>
Não. Não há suporte para reversão após os recursos terem sido atualizados com êxito.

**Posso validar minha assinatura ou meus recursos para ver se eles podem ser atualizados?**</br>
Sim. A primeira etapa na atualização valida se os recursos podem ser atualizados. No caso de falha na validação dos pré-requisitos, você recebe mensagens para todos os motivos pelos quais a atualização não pode ser concluída.

**Quais permissões devo ter para disparar a atualização do cofre?**</br>
Para executar a atualização do cofre, você deve ser adicionado como coadministrador da assinatura no portal Clássico do Azure. Isso será necessário mesmo que você já tenha sido adicionado como proprietário no portal do Azure. Tente adicionar um coadministrador para a assinatura no portal clássico do Azure para descobrir se você é o coadministrador da assinatura. Se você não conseguir adicionar um coadministrador, contate um administrador de serviços ou o coadministrador da assinatura para ser adicionado.

**Posso atualizar meu cofre de Backup baseado em CSP?**</br>
Não. No momento, não é possível atualizar os cofres de backup baseados em CSP. Vamos adicionar suporte para isso nas próximas versões.

**Posso exibir meu cofre clássico após a atualização?**</br>
Não. Você não pode exibir nem gerenciar o cofre clássico pós-atualização. Você só poderá usar o novo portal do Azure para todas as ações de gerenciamento no cofre.

**Minha atualização falhou; preciso atualizar o agente em um computador que não existe mais. O que devo fazer nesse caso?**</br>
Se você precisar armazenar os backups desse computador para retenção de longo prazo, não poderá atualizar o cofre. Adicionaremos suporte para habilitar a atualização em um cofre assim em versões futuras.
Se você não precisar mais armazenar os backups nesse computador, cancele o registro desse computador do cofre e tente realizar a atualização novamente.

**Por que não consigo ver as informações de trabalhos para meus recursos locais após a atualização**</br>
O Monitoramento de backups locais (agente MARS, DPM e MABS) é um novo recurso que você obtém ao atualizar seu Cofre de Backup para o Cofre de Serviços de Recuperação. As informações de monitoramento levam até 12 horas para serem sincronizadas com o serviço.

**Como faço para relatar um problema?**</br>
Caso você enfrente falhas durante a atualização, tome nota do OperationId listado no erro. O Suporte da Microsoft estará trabalhando proativamente para resolver o problema. Você pode contatar o suporte ou enviar um email para nós no endereço rsvaultupgrade@service.microsoft.com com sua ID de Assinatura, nome do cofre e ID da operação. Trabalharemos para resolver o problema o mais rápido possível. Não repita a operação, a menos que seja explicitamente instruído a fazer isso pela Microsoft.


## <a name="next-steps"></a>Próximas etapas
Use o seguinte artigo para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md).

