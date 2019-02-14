---
title: Solução de problemas na versão prévia da proteção de senha do Azure AD
description: Entender a solução de problemas comuns da versão prévia da proteção de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5727965373752d40e3ce508c1bc79046c2b3b70b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177744"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Visualização: Solução de problemas de Proteção de Senha do Azure AD

|     |
| --- |
| A Proteção de Senha do Azure AD é uma versão prévia pública do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Após a implantação da Proteção de Senha do Azure AD, a solução de problemas pode ser necessária. Este artigo apresenta detalhes para ajudá-lo a entender algumas etapas de solução de problemas comuns.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Senhas fracas não estão sendo rejeitadas conforme o esperado

Isso pode ter várias causas possíveis:

1. Seus agentes do DC ainda não baixaram uma política. O sintoma disso é 30001 eventos no log de eventos do administrador do agente do DC.

    As possíveis para esse problema incluem:

    1. A floresta ainda não está registrada
    2. O proxy ainda não está registrado
    3. Problemas de conectividade de rede estão impedindo que o serviço de Proxy se comunique com o Azure (verificar os requisitos de Proxy HTTP)

2. O modo de Imposição de política de senha ainda está definido para Auditoria. Se esse for o caso, reconfigure-o para Imposição usando o portal de Proteção de Senha do Azure AD. Confira [Habilitar proteção de senha](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. A política de senha foi desabilitada. Se esse for o caso, reconfigure-a para Habilitada usando o portal de Proteção de Senha do Azure AD. Confira [Habilitar proteção de senha](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. O algoritmo de validação de senha pode estar funcionando conforme o esperado. Confira [Como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Modo de Reparo de Serviços de Diretório

Se o controlador de domínio for inicializado no Modo de Reparo de Serviços de Diretório, o serviço do agente DC detecta isso e fará com que todas as atividades de validação ou imposição de senha sejam desabilitadas, independentemente da configuração de política ativa no momento.

## <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação em que o serviço do agente DC esteja causando problemas, o serviço do agente DC poderá ser encerrado imediatamente. O dll do filtro de senha do agente DC ainda tentará chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada serão aceitas durante esse período. O serviço do agente DC poderá, então, também ser configurado através do Gerenciador de Controle de Serviço do Windows com um tipo de inicialização “Desabilitado”, conforme necessário.

Outra medida de correção seria definir para o modo Habilitar para Não no portal de Proteção de Senha do Azure AD. Depois que a política atualizada tiver sido baixada, cada serviço do agente do DC entrará no modo inativo em que todas as senhas são aceitas no estado em que se encontram. Para mais informações, consulte [Impor o modo](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Rebaixamento do controlador de domínio

Há suporte para rebaixar um controlador de domínio que ainda esteja executando o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente do DC continua aplicando a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de Administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que senhas seguras sejam escolhidas para contas de Administrador local como parte de um procedimento de rebaixamento DC, no entanto, a validação da nova senha da conta de Administrador Local pelo software do agente DC pode ser interrompido para procedimentos operacionais de rebaixamento pré-existentes.

Depois que o rebaixamento tiver ocorrido com êxito e o controlador de domínio for reiniciado e estiver em execução novamente como um servidor membro normal, o software do agente DC será revertido para execução em modo passivo. Então, poderá ser desinstalado a qualquer momento.

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de visualização pública e limpar todo o estado relacionado do(s) domínio(s) e floresta, essa tarefa poderá ser executada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço Proxy for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint. Se qualquer instância do serviço do agente DC for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint e o estado sysvol.

1. Desinstale o software do Proxy de todos os computadores. Esta etapa **não** exige um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Essa etapa **exige** um reinício.
3. Remova manualmente todos os pontos de conexão do serviço Proxy em cada contexto de nomenclatura do domínio. O local desses objetos pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente. 

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomeação de domínios. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de como o software de visualização pública foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomeação de configuração do Active Directory. É possível descobrir e excluir da seguinte forma:

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todo o estado relacionado ao sysvol excluindo manualmente a seguinte pasta e todo o conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, esse caminho também poderá ser acessado localmente em um determinado controlador de domínio e o local padrão seria semelhante ao caminho a seguir:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Esse caminho será diferente se o compartilhamento sysvol foi configurado em um local não padrão.

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre a Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
