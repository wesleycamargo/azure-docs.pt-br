<properties
	pageTitle="Implementação de sincronização de senha com a sincronização do Azure AD Connect | Microsoft Azure"
	description="Fornece informações sobre como funciona a sincronização de senha e como habilitá-la."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="markusvi;andkjell"/>


# Implementação de sincronização de senha com a sincronização do Azure AD Connect
Este tópico fornece as informações necessárias para sincronizar suas senhas de usuário do AD (Active Directory) local para um Azure AD (Azure Active Directory) baseado na nuvem.

## O que é sincronização de senha
A probabilidade de você ser impedido de concluir seu trabalho devido a uma senha esquecida está relacionada ao número de senhas diferentes que você precisa se lembrar. Quanto mais senhas você precisa lembrar, maior a probabilidade de se esquecer uma delas. Perguntas e chamadas sobre redefinições de senha e outros problemas relacionados à senha demandam a maior parte dos recursos de assistência técnica.

A sincronização de senha é um recurso para sincronizar senhas de usuário de um Active Directory local para um Azure Active Directory (AD do Azure) local. Esse recurso permite que você entre nos serviços do Azure Active Directory (como Office 365, Microsoft Intune, CRM Online e Serviços de Domínio do Azure AD) usando a mesma senha usada para entrar no Active Directory local.

![O que é o Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Reduzindo o número de senhas que os usuários precisam manter para apenas uma, a sincronização de senha ajuda a:

- Aumentar a produtividade dos seus usuários
- Reduzir os custos de assistência técnica

Se você optar por usar a [**Federação com o AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), outra opção será habilitar a sincronização de senha como um backup em caso de falha na infraestrutura do AD FS.

A sincronização de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Para usar a sincronização de senha no seu ambiente, você precisa:

- Instalar o Azure AD Connect
- Configure a sincronização de diretórios entre seu AD local e o Azure Active Directory
- Habilitar a sincronização de senha

Para saber mais, consulte [Integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

> [AZURE.NOTE] Para obter mais detalhes sobre os Serviços de Domínio do Active Directory, que são configurados para sincronização de senha e FIPS, consulte[Sincronização de senha e FIPS](#password-synchronization-and-fips).

## Como a sincronização de senha funciona
O serviço de domínio do Active Directory armazena as senhas na forma de uma representação do valor de hash da senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o "*algoritmo de hash*"). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. Não é possível usar o hash de senha para entrar na sua rede local.

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. As senhas são sincronizadas por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de senha é semelhante à sincronização de dados de usuário como DisplayName ou Endereços de Email. Contudo, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma senha, ela substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar.

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos. O recurso de sincronização de senha repete automaticamente tentativas de sincronização com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não afeta um usuário conectado atualmente. Sua sessão de serviço de nuvem atual não será afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está logado em um serviço de nuvem. No entanto, quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

> [AZURE.NOTE] Somente há suporte para a sincronização de senha para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### Como a sincronização de senha funciona com os Serviços de Domínio do AD do Azure
Você também pode usar o recurso de sincronização de senha para sincronizar suas senhas locais para os [Serviços de Domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Esse cenário permite que os Serviços de Domínio do Azure AD autentiquem os usuários na nuvem com todos os métodos disponíveis no AD local. A experiência desse cenário é semelhante a usar a ADMT (Ferramenta de Migração do Active Directory) em um ambiente local.

### Considerações de segurança
Ao sincronizar senhas, a versão da sua senha em texto sem formatação não é exposta ao recurso de sincronização de senha, nem ao Azure AD ou qualquer um dos serviços associados.

Além disso, não há nenhum requisito no Active Directory local para armazenar a senha em um formato criptografado de modo reversível. Um resumo do hash da senha do Active Directory é usado para a transmissão entre o AD local e o Active Directory do Azure. O resumo do hash da senha não pode ser usado para acessar recursos no seu ambiente do local.

### Considerações sobre política de senha
Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de senha:

1. Política de complexidade de senha
2. Política de expiração de senha

**Política de complexidade da senha** Ao habilitar a sincronização de senha, as políticas de complexidade de senha no Active Directory local substituem as políticas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas do seu Active Directory local para acessar os serviços do Azure AD.

> [AZURE.NOTE] Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

**Política de expiração de senha** Se um usuário estiver no escopo de sincronização de senha, a senha da conta de nuvem será definida como "*Nunca Expirar*". Você pode continuar entrando nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

### Substituindo senhas sincronizadas
Um administrador pode redefinir sua senha manualmente usando o Windows PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem se aplicam à nova senha.

Se você alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.

## Habilitando a sincronização de senha
A sincronização de senha é habilitada automaticamente quando você instala o Azure AD Connect usando as **Configurações Expressas**. Para obter mais detalhes, confira [Introdução ao Azure AD Connect usando configurações expressas](active-directory-aadconnect-get-started-express.md).

Se você usar configurações personalizadas quando instalar o Azure AD Connect, poderá habilitar a sincronização de senha na página de entrada do usuário. Para obter mais detalhes, confira [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Habilitando a sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### Sincronização de senha e FIPS
Se o servidor foi bloqueado de acordo com o FIPS (Federal Information Processing Standard), o MD5 foi desabilitado.

**Para habilitar MD5 para a sincronização de senha, execute as seguintes etapas:**

1. Vá para **%programfiles%\\Azure AD Sync\\Bin**.
2. Abra **miiserver.exe.config**.
3. Acesse o nó **configuração/tempo de execução** (no fim do arquivo).
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Salve suas alterações.

Para referência, a captura mostra como deve ser a aparência:

```
	<configuration>
		<runtime>
			<enforceFIPSPolicy enabled="false"/>
		</runtime>
	</configuration>
```

Para obter informações sobre segurança e FIPS, veja [Sincronização de senha do AAD, criptografia e conformidade FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## Solução de problemas de sincronização de senha
Se as senhas não estiverem sincronizando conforme o esperado, pode ser para um subconjunto de usuários ou todos os usuários.

- Se você tiver um problema com objetos individuais, veja [Solucionar problemas de um objeto que não está sincronizando senhas](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Se você tiver um problema em que nenhuma senha é sincronizada, confira [Solucionar problemas em que nenhuma senha é sincronizada](#troubleshoot-issues-where-no-passwords-are-synchronized).

### Solucionar problemas de um objeto que não está sincronizando senhas
Você pode solucionar problemas de sincronização de senha problemas facilmente analisando o status de um objeto.

Comece em **Usuários e Computadores do Active Directory**. Localize o usuário e verifique se **O usuário deve alterar a senha no próximo logon** está desmarcada. ![Senhas produtivas do Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) Se essa opção estiver selecionada, peça ao usuário para entrar e alterar a senha. As senhas temporárias não são sincronizadas com o Azure AD.

Se elas parecerem corretas no Active Directory, a próxima etapa será seguir o usuário no mecanismo de sincronização. Ao seguir o usuário do Active Directory local para o Azure AD, você pode ver se há um erro descritivo no objeto.

1. Abra o **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Clique em **Conectores**.
3. Escolha o **Conector do Active Directory** em que o usuário está localizado.
4. Escolha **Pesquisar Espaço Conector**.
5. Localize o usuário que você está procurando.
6. Escolha a guia **linhagem** e verifique se pelo menos uma Regra de Sincronização mostra **Sincronização de Senha** como **Verdadeiro**. Na configuração padrão, o nome da Regra de Sincronização é **Entrada do AD – Conta do Usuário Habilitada**. ![Informações de linhagem sobre um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. Em seguida [siga o usuário](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) por meio do metaverso até o espaço Conector do Azure AD. O objeto do espaço conector deve ter uma regra de saída com **Sincronização de Senha** definida como **Verdadeiro**. Na configuração padrão, o nome da regra de sincronização é **Saída para AAD – Ingresso do Usuário**. ![Propriedades de espaço do conector de um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. Para ver os detalhes da sincronização de senha do objeto da última semana, clique em **Log...**. ![Detalhes de log do objeto](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

A coluna de status pode ter os seguintes valores:

Status | Descrição
---- | -----
Sucesso | A senha foi sincronizada com êxito.
FilteredByTarget | A senha está definida para **O usuário deve alterar a senha no próximo logon**. A senha não foi sincronizada.
NoTargetConnection | Nenhum objeto no metaverso ou no espaço conector do AD do Azure.
SourceConnectorNotPresent | Nenhum objeto encontrado no espaço conector do Active Directory local.
TargetNotExportedToDirectory | O objeto no espaço conector do AD do Azure ainda não foi exportado.
MigratedCheckDetailsForMoreInfo | A entrada de log foi criada antes da versão 1.0.9125.0 e é mostrada em seu estado herdado.

### Solucionar problemas em que nenhuma senha é sincronizada
Comece a executar o script na seção [Obter o status das configurações da sincronização de senha](#get-the-status-of-password-sync-settings). Ele fornece uma visão geral da configuração da sincronização de senha. ![Saída do script do PowerShell de configurações da sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) Se o recurso não estiver habilitado no Azure AD ou se o status do canal de sincronização não estiver habilitado, execute o assistente de instalação de conexão. Escolha **Personalizar opções de sincronização** e desmarque a sincronização de senha. Essa mudança desabilita o recurso temporariamente. Em seguida, execute o assistente novamente e reabilite a sincronização de senha. Execute o script novamente para verificar se a configuração está correta.

Se o script mostrar que não há nenhuma pulsação, execute o script em [Disparar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords). Esse script também pode ser usado para outros cenários em que a configuração está correta, mas as senhas não são sincronizadas.

#### Obter o status das configurações da sincronização de senha

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

#### Disparar uma sincronização completa de todas as senhas
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

## Próximas etapas

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->