<properties
   pageTitle="Azure AD Connect: atualização automática | Microsoft Azure"
   description="Este tópico descreve o recurso interno de atualização automática na sincronização do Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="andkjell"/>

# Azure AD Connect: atualização automática
Esse recurso foi introduzido com a compilação 1.1.105.0 (lançada em fevereiro de 2016).

## Visão geral
Verificar se a instalação do Azure AD Connect está sempre atualizada nunca foi tão fácil após o lançamento do recurso de **atualização automática**. Esse recurso é habilitado por padrão para instalações expressas e garante que, quando uma nova versão for lançada, a instalação será atualizada automaticamente.

A atualização automática é habilitada por padrão para o seguinte:

- Instalação das configurações expressas
- Usar o SQL Express LocalDB, que é o que as configurações Expressas sempre usarão
- A conta do AD é a conta MSOL\_ padrão criada pelas configurações Expressas
- Tem menos de 100 mil objetos no metaverso

O estado atual da atualização automática pode ser exibido com o cmdlet `Get-ADSyncAutoUpgrade` do PowerShell. Ele contém os seguintes estados:

| Estado | Comentário |
| ---- | ---- |
| Habilitado | A atualização automática está habilitada. |
| Suspenso | Somente definido pelo sistema. O sistema não está mais qualificado para receber atualizações automáticas. |
| Desabilitado | A atualização automática está desabilitada. |

Você pode alterar entre **Habilitado** e **Desabilitado** com o `Set-ADSyncAutoUpgrade`. Somente o sistema deve definir o estado como **Suspenso**.

A atualização automática está usando o Azure AD Connect Health como a infraestrutura de atualização. Para que a atualização automática funcione corretamente, não deixe de abrir as URLs no proxy do **Azure AD Connect Health** conforme documentado em [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se a interface do usuário **Gerenciador do Serviço de Sincronização** estiver em execução no servidor, a atualização será suspensa até que a interface do usuário seja fechada.

## Solucionar problemas
Se a sua instalação do Connect não for atualizada conforme o esperado, siga estas etapas para descobrir o que poderia estar errado.

Primeiro, você não deve esperar que a atualização automática seja tentada no primeiro dia em que uma nova versão for lançada. Há uma aleatoriedade intencional antes que ocorra uma tentativa de atualização. Sendo assim, não se assuste se a instalação não for atualizada imediatamente.

Se você achar que algo não está certo, primeiro execute `Get-ADSyncAutoUpgrade` para garantir que a atualização automática esteja habilitada.

Inicie o log de eventos e examine o log de eventos **Aplicativo**. Adicione um filtro de log de eventos para a fonte **Atualização do Azure AD Connect** e o intervalo de ID de evento **300-399**. ![Filtro de log de eventos para atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)

Isso lhe dará os logs de eventos associados ao status da atualização automática. ![Filtro de log de eventos para atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)

A primeira metade do resultado dará uma visão geral do estado.

| Prefixo do resultado | Descrição |
| --- | --- |
| Sucesso | A instalação foi atualizada com êxito. |
| UpgradeAborted | Uma condição temporária interrompeu a atualização. Ela será tentada novamente e a expectativa é de que tenha êxito. |
| UpgradeNotSupported | O sistema tem uma configuração que está impedindo que o sistema seja atualizado automaticamente. A atualização será tentada novamente para ver se o estado está mudando, mas a expectativa é de que o sistema precise ser atualizado manualmente. |

Esta é uma lista das mensagens mais comuns que você encontrará. Ela não lista todas as mensagens, mas a mensagem de resultado deve ser clara quanto a qual é o problema.

| Mensagem de resultado | Descrição |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedSyncExeInUse | A [interface do usuário do Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) está aberta no servidor.
| UpgradeAbortedInsufficientDiskSpace | Não há espaço em disco suficiente para dar suporte a uma atualização. |
| UpgradeAbortedSyncCycleDisabled | A opção SyncCycle no [agendador](active-directory-aadconnectsync-feature-scheduler.md) foi desabilitada. |
| UpgradeAbortedSyncOrConfigurationInProgress | O assistente de instalação está em execução ou uma sincronização foi agendada fora do agendador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules | Você adicionou suas próprias regras personalizadas à configuração. |
| UpgradeNotSupportedDeviceWritebackEnabled | Você habilitou o recurso [write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md). |
| UpgradeNotSupportedGroupWritebackEnabled | Você habilitou o recurso [write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback). |
| UpgradeNotSupportedMetaverseSizeExceeeded | Você tem mais de 100 mil objetos no metaverso. |
| UpgradeNotSupportedMultiForestSetup | Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta. |
| UpgradeNotSupportedNonMsolAccount | A [conta do Conector do AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) não é mais a conta MSOL\_ padrão.
| UpgradeNotSupportedStagingModeEnabled | O servidor está definido como em [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode). |

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0420_2016-->