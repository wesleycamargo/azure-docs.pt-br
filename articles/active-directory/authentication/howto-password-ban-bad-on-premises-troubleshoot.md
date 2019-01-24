---
title: Solução de problemas e registro em log na versão prévia da proteção por senha do Azure AD
description: Reconhecer o registro em log da proteção por senha do Azure AD e solução de problemas comuns
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 008fba2fd6052ac9043de6ec217cb71d17f3ecce
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427093"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Visualização: Solução de problemas, relatório e monitoramento de proteção por senha do Azure AD

|     |
| --- |
| A proteção de senha do Azure Active Directory é uma versão prévia do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Após a implantação da proteção de senha do Microsoft Azure Active Directory, o monitoramento e o relatório são tarefas essenciais. Este artigo apresenta detalhes para ajudá-lo a reconhecer onde cada serviço registra as informações e como relatar sobre o uso da proteção por senha do Azure AD.

## <a name="on-premises-logs-and-events"></a>Eventos e logs locais

### <a name="dc-agent-admin-log"></a>Log do administrador do agente DC

Em cada controlador de domínio, o software de serviço do agente DC grava os resultados de suas validações de senha (e outro status) em um log de eventos local:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Os eventos são registrados pelos vários componentes do agente DC usando os intervalos a seguir:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Dll do filtro de senha do Agente DC| 10000-19999|
|Processo de hospedagem de serviço do agente DC| 20000-29999|
|Lógica de validação da política de serviço do agente DC| 30000-39999|

Para uma operação de validação de senha com êxito, geralmente há um evento registrado da dll do filtro de senha do agente DC. Para uma operação de validação de senha com falha, geralmente há dois eventos registrados, um do serviço do agente DC e um da dll do filtro de senha do Agente DC.

Eventos discretos para capturar essas situações são registrados com base nos fatores a seguir:

* Se uma determinada senha está sendo definida ou alterada.
* Se a validação de uma determinada senha foi aprovada ou falhou.
* Se a validação falhou devido à política global da Microsoft versus a política organizacional.
* Se o modo somente auditoria está atualmente ativado ou desativado para a política de senha atual.

Os principais eventos relacionados à validação de senha são os seguintes:

|   |Alteração de senha |Definição de senha|
| --- | :---: | :---: |
|Aprovado |10014 |10015|
|Falha (não aprovou a política de senha do cliente)| 10016, 30002| 10017, 30003|
|Falha (não aprovou a política de senha da Microsoft)| 10016, 30004| 10017, 30005|
|Aprovado somente auditoria (teria falhado a política de senha do cliente)| 10024, 30008| 10025, 30007|
|Aprovado somente auditoria (teria falhado a política de senha da Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Senhas de entrada são validadas primeiro na lista de senhas globais da Microsoft e, se isso falhar, nenhum processamento adicional será executado. Esse é o mesmo comportamento executado em alterações de senha no Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Mensagem de registro de eventos de amostra para a ID de Evento 10014 (conjunto de senhas bem-sucedido)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de registro de eventos de amostra para ID de Evento 10017 e 30003 (conjunto de senha com falha)

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mensagem de registro de eventos de amostra para o ID do Evento 30001 (senha aceita devido a nenhuma política disponível)

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Mensagem de registro de eventos de amostra para a ID de Evento 30006 (nova política sendo aplicada)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Log operacional de agente do controlador de domínio

O serviço do agente DC também registrará eventos relacionados à operação no log a seguir:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>Log de rastreamento do agente de controlador de domínio

O serviço do agente DC também pode registrar eventos de rastreamento detalhados no nível de depuração no seguinte log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Log de rastreamento está desabilitado por padrão.

> [!WARNING]
>  Quando ativado, o log de rastreamento recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

#### <a name="dc-agent-text-logging"></a>Registro de texto do agente do DC

O serviço do agente DC pode ser configurado para gravar em um log de texto, definindo o seguinte valor do Registro:

HKLM \ System \ CurrentControlSet \ Services \ AzureADPasswordProtectionDCAgent \ Parâmetros! EnableTextLogging = 1 (valor REG_DWORD)

O log de texto é desabilitado por padrão. Uma reinicialização do serviço do agente DC é necessária para que as alterações nesse valor entrem em vigor. Quando ativado, o serviço do agente DC gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente tem um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção por senha do Azure AD

#### <a name="proxy-service-event-logs"></a>Logs de Eventos do Serviço de Proxy

O serviço Proxy emite um conjunto mínimo de eventos para os seguintes logs de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

O serviço Proxy também pode registrar eventos de rastreamento detalhados no nível de depuração no seguinte log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Log de rastreamento está desabilitado por padrão.

> [!WARNING]
> Quando ativado, o log de rastreamento recebe um grande volume de eventos e isso pode afetar o desempenho do host proxy. Portanto, esse log deverá ser habilitado apenas quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

#### <a name="proxy-service-text-logging"></a>Log de texto do serviço de proxy

O serviço de proxy pode ser configurado para gravar em um log de texto, definindo o seguinte valor do Registro:

HKLM \ System \ CurrentControlSet \ Services \ AzureADPasswordProtectionProxy \ Parâmetros! EnableTextLogging = 1 (valor REG_DWORD)

O log de texto é desabilitado por padrão. Uma reinicialização do serviço Proxy é necessária para que as alterações nesse valor entrem em vigor. Quando ativado, o serviço Proxy gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente tem um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log aprimorado somente deverá ser habilitado quando um problema exigir uma investigação mais profunda e apenas por um período mínimo.

#### <a name="powershell-cmdlet-logging"></a>Registro de cmdlet Powershell

A maioria dos cmdlets Powershell de proteção por senha do Microsoft Azure Active Directory grava em um log de texto localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a causa e \ ou a solução não forem aparentes, esses logs de texto também poderão ser consultados.

### <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação em que o serviço do agente DC esteja causando problemas, o serviço do agente DC poderá ser encerrado imediatamente. O dll do filtro de senha do agente DC ainda tentará chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada serão aceitas durante esse período. O serviço do agente DC poderá, então, também ser configurado através do Gerenciador de Controle de Serviço do Windows com um tipo de inicialização “Desabilitado”, conforme necessário.

### <a name="performance-monitoring"></a>Monitoramento de desempenho

O software de serviço do agente DC instala um objeto de contador de desempenho denominado **Proteção de senha do Microsoft Azure Active Directory**. Atualmente, estão disponíveis os contadores de desempenho a seguir:

|Nome do contador de desempenho | DESCRIÇÃO|
| --- | --- |
|Senhas processadas |Esse contador exibe o número total de senhas processadas (aceitas ou rejeitadas) desde o último reinício.|
|Senhas aceitas |Esse contador exibe o número total de senhas aceitas desde o último reinício.|
|Senhas rejeitadas |Esse contador exibe o número total de senhas que foram rejeitadas desde o último reinício.|
|Solicitações de filtro de senha em andamento |Esse contador exibe o número de solicitações de filtro de senha atualmente em andamento.|
|Solicitações de filtro de senha de pico |Esse contador exibe o número de pico das solicitações de filtro de senha simultâneas desde o último reinício.|
|Erros de solicitação de filtro de senha |Este contador exibe o número total de solicitações de filtro de senha que falharam devido a um erro desde o último reinício. Erros podem ocorrer quando o serviço do agente DC de proteção por senha do Azure AD não estiver em execução.|
|Solicitações de filtro de senha/s |Esse contador exibe a taxa na qual as senhas estão sendo processadas.|
|Tempo de processamento de solicitação de filtro de senha |Esse contador exibe o tempo médio necessário para processar uma solicitação de filtro de senha.|
|Tempo de processamento da solicitação de filtro de senha de pico |Esse contador exibe o tempo de processamento da solicitação do filtro de senha de pico desde o último reinício.|
|Senhas aceitas devido ao modo de auditoria |Esse contador exibe o número total de senhas que normalmente teriam sido rejeitadas, mas foram aceitas porque a política de senha foi configurada para estar no modo de auditoria (desde o último reinício).|

## <a name="directory-services-repair-mode"></a>Modo de Reparo de Serviços de Diretório

Se o controlador de domínio for inicializado no Modo de Reparo de Serviços de Diretório, o serviço do agente DC detectará isso, fazendo com que todas as atividades de validação ou imposição de senha sejam desabilitadas, independentemente da configuração de política ativa no momento.

## <a name="domain-controller-demotion"></a>Rebaixamento do controlador de domínio

Há suporte para rebaixar um controlador de domínio que ainda esteja executando o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente DC mantém em execução e continua aplicando a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de Administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que senhas seguras sejam escolhidas para contas de Administrador local como parte de um procedimento de rebaixamento DC, no entanto, a validação da nova senha da conta de Administrador Local pelo software do agente DC pode ser interrompido para procedimentos operacionais de rebaixamento pré-existentes.

Depois que o rebaixamento tiver ocorrido com êxito e o controlador de domínio for reiniciado e estiver em execução novamente como um servidor membro normal, o software do agente DC será revertido para execução em modo passivo. Então, poderá ser desinstalado a qualquer momento.

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de visualização pública e limpar todo o estado relacionado do(s) domínio(s) e floresta, essa tarefa poderá ser executada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço Proxy for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint. Se qualquer instância do serviço do agente DC for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint e o estado sysvol.

1. Desinstale o software do Proxy de proteção por senha de todos os computadores. Esta etapa **não** exige um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Essa etapa **exige** um reinício.
3. Remova manualmente todos os pontos de conexão do serviço Proxy em cada contexto de nomenclatura do domínio. O local desses objetos pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente. 

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomeação de domínios. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de como o software de visualização pública foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomeação de configuração do Active Directory. É possível descobrir e excluir da seguinte forma:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectonConfigContainer
   ```

6. Remova manualmente todo o estado relacionado ao sysvol excluindo manualmente a seguinte pasta e todo o conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Se necessário, esse caminho também poderá ser acessado localmente em um determinado controlador de domínio e o local padrão seria semelhante ao caminho a seguir:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Esse caminho será diferente se o compartilhamento sysvol foi configurado em um local não padrão.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
