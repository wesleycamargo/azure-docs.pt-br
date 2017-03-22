---
title: "Solução de problemas de sincronização de senha com a sincronização do Azure AD Connect | Microsoft Docs"
description: "Fornece informações sobre como solucionar problemas de sincronização de senha"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Solução de problemas de sincronização de senha com a sincronização do Azure AD Connect
Este tópico fornece etapas para solucionar problemas com a sincronização de senha. Se as senhas não estiverem sincronizando conforme o esperado, pode ser para um subconjunto de usuários ou todos os usuários.

* Se você tiver um problema em que nenhuma senha é sincronizada, confira [Solucionar problemas em que nenhuma senha é sincronizada](#no-passwords-are-synchronized).
* Se você tiver um problema com objetos individuais, veja [Solucionar problemas de um objeto que não está sincronizando senhas](#one-object-is-not-synchronizing-passwords).

## <a name="no-passwords-are-synchronized"></a>Nenhuma senha é sincronizada
Siga estas etapas para descobrir por que nenhuma senha é sincronizada:

1. O servidor do Connect está no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode)? Um servidor no modo de preparo não sincroniza nenhuma senha.
2. Execute o script na seção [Obter o status das configurações de sincronização de senha](#get-the-status-of-password-sync-settings). Ele fornece uma visão geral da configuração da sincronização de senha.  
![Saída do script do PowerShell de configurações da sincronização de senha](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Se o recurso não estiver habilitado no Azure AD ou se o status do canal de sincronização não estiver habilitado, execute o assistente de instalação de conexão. Escolha **Personalizar opções de sincronização** e desmarque a sincronização de senha. Essa mudança desabilita o recurso temporariamente. Em seguida, execute o assistente novamente e reabilite a sincronização de senha. Execute o script novamente para verificar se a configuração está correta.
4. Procure erros no log de eventos. Procure os seguintes eventos, que poderão indicar um problema:
    1. Fonte: “Sincronização de diretório” ID: 0, 611, 652, 655 Caso receba uma delas, isso indica que há um problema de conectividade. A mensagem do log de eventos contém informações sobre a floresta em que há um problema. Para obter mais informações, consulte [Problema de conectividade](#connectivity problem)
5. Se você não vir nenhuma pulsação ou se nenhuma outra solução tiver funcionado, execute [Disparar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords). Você deve executar esse script apenas uma vez.
6. Leia a seção [Solucionar problemas de um objeto que não está sincronizando senhas](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problem"></a>Problema de conectividade

1. Você tem conectividade com o Azure AD?
2. A conta tem as permissões necessárias para ler os hashes de senha em todos os domínios? Se você instalou o Connect usando as configurações Expressas, as permissões já deverão estar corretas. Se você usou a instalação personalizada, é necessário definir as permissões manualmente.
    1. Para encontrar a conta usada pelo Active Directory Connector, inicie o **Synchronization Service Manager**. Acesse **Conectores** e encontre a floresta local do Active Directory que está sendo usada para a solução de problemas. Selecione o Conector e clique em **Propriedades**. Vá para **Conectar-se à floresta do Active Directory**.  
    ![Conta usada pelo Conector do AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Anote o nome de usuário e o domínio no qual a conta está localizada.
    2. Inicie **Computadores e Usuários do Active Directory**. Verifique se a conta encontrada nas etapas anteriores tem as seguintes permissões definidas na raiz de todos os domínios da floresta:
        * Replicar alterações de diretório
        * Replicar todas as alterações de diretório
3. Os controladores de domínio estão acessíveis pelo Azure AD Connect? Se o servidor do Connect não puder se conectar a todos os controladores de domínio, você deverá configurar **Usar somente controlador de domínio preferencial**.  
    ![Controlador de domínio usado pelo Conector do AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    Volte para **Synchronization Service Manager** e **Configurar Partição de Diretório**. Selecione o domínio em **Selecionar partições de diretório**, marque a caixa de seleção **Usar somente controladores de domínio preferenciais** e clique em **Configurar**. Na lista, insira os controladores de domínio que o Connect deverá usar para a sincronização de senha. A mesma lista também é usada para importação e exportação. Siga estas etapas para todos os domínios.
4. Se o script mostrar que não há nenhuma pulsação, execute o script em [Disparar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords"></a>Um objeto não está sincronizando senhas
Você pode solucionar problemas de sincronização de senha problemas facilmente analisando o status de um objeto.

1. Comece em **Usuários e Computadores do Active Directory**. Localize o usuário e verifique se **O usuário deve alterar a senha no próximo logon** está desmarcada.  
![Senhas produtivas do Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
Se essa opção estiver selecionada, peça ao usuário para entrar e alterar a senha. As senhas temporárias não são sincronizadas com o Azure AD.
2. Se elas parecerem corretas no Active Directory, a próxima etapa será seguir o usuário no mecanismo de sincronização. Ao seguir o usuário do Active Directory local para o Azure AD, você pode ver se há um erro descritivo no objeto.
    1. Abra o **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
    2. Clique em **Conectores**.
    3. Escolha o **Conector do Active Directory** em que o usuário está localizado.
    4. Selecione **Pesquisar Espaço do Conector**.
    5. Em **Escopo**, selecione **DN ou âncora**. Insira o DN completo do usuário que está sendo usado para a solução de problemas.
    ![Pesquisar usuário no cs com o DN](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. Localize o usuário que você está procurando e clique em **Propriedades** para ver todos os atributos. Se o usuário não estiver no resultado da pesquisa, verifique as [regras de filtragem](active-directory-aadconnectsync-configure-filtering.md) e lembre-se de executar [Aplicar e verificar alterações](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) para que o usuário seja exibido no Connect.
    7. Para ver os detalhes da sincronização de senha do objeto da última semana, clique em **Log...**.  
    ![Detalhes de log do objeto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    Se o log do objeto estiver vazio, o Azure AD Connect não conseguiu ler o hash da senha no Active Directory. Continue a solução de problemas com [Erros de conectividade](#connectivity-errors). Se você receber qualquer outro valor diferente de **êxito**, consulte a tabela em [Log de sincronização de senha](#password-sync-log).
    8. Escolha a guia **linhagem** e verifique se pelo menos uma **Regra de Sincronização** mostra Sincronização de Senha como **Verdadeiro**. Na configuração padrão, o nome da Regra de Sincronização é **Entrada do AD – Conta do Usuário Habilitada**.  
    ![Informações de linhagem sobre um usuário](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. Clique em **Propriedades do objeto do metaverso**. Você vê uma lista de atributos do usuário.  
    ![Informações do metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    Verifique se não há nenhum atributo **cloudFiltered** presente. Verifique se os atributos de domínio (domainFQDN e domainNetBios) têm os valores esperados.
    10. Clique na guia **Conectores**. Verifique se você consegue ver os conectores do AD local e do Azure AD.
    ![Informações do metaverso](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Selecione a linha que representa o Azure AD e clique em **Propriedades**. Clique na guia **Linhagem**. O objeto do espaço conector deve ter uma regra de saída com **Sincronização de Senha** definida como **Verdadeiro**. Na configuração padrão, o nome da regra de sincronização é **Saída para AAD – Ingresso do Usuário**.  
    ![Propriedades de espaço do conector de um usuário](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Log de sincronização de senha
A coluna de status pode ter os seguintes valores:

| Status | Descrição |
| --- | --- |
| Sucesso |A senha foi sincronizada com êxito. |
| FilteredByTarget |A senha está definida para **O usuário deve alterar a senha no próximo logon**. A senha não foi sincronizada. |
| NoTargetConnection |Nenhum objeto no metaverso ou no espaço conector do AD do Azure. |
| SourceConnectorNotPresent |Nenhum objeto encontrado no espaço conector do Active Directory local. |
| TargetNotExportedToDirectory |O objeto no espaço conector do AD do Azure ainda não foi exportado. |
| MigratedCheckDetailsForMoreInfo |A entrada de log foi criada antes da versão 1.0.9125.0 e é mostrada em seu estado herdado. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ajudar na solução de problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obter o status das configurações da sincronização de senha
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Disparar uma sincronização completa de todas as senhas
> [!NOTE]
> Você deve executar esse script apenas uma vez. Se precisar executá-lo mais de uma vez, isso indica que o problema é outro. Contate o suporte da Microsoft para ajudar a solucionar o problema.

Você pode disparar uma sincronização completa de todas as senhas usando o seguinte script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Próximas etapas
* [Implementação de sincronização de senha com a sincronização do Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

