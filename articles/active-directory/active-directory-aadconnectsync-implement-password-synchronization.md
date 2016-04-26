<properties
	pageTitle="Implementação de sincronização de senha com a sincronização do Azure AD Connect | Microsoft Azure"
	description="Fornece informações sobre como funciona a sincronização de senha e como habilitá-la."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/15/2016"
	ms.author="markusvi;andkjell"/>


# Implementação de sincronização de senha com a sincronização do Azure AD Connect

Com a sincronização de senha, você pode usar sua senha do Active Directory local para entrar no Azure Active Directory também.

Este tópico fornece a você as informações de que precisa para habilitar e solucionar problemas de sincronização de senha em seu ambiente.


## O que é sincronização de senha

A sincronização de senha é um recurso para sincronizar senhas de usuário de um Active Directory local para um Azure Active Directory (AD do Azure) local. Esse recurso permite que você entre nos serviços do Azure Active Directory (como Office 365, Microsoft Intune e CRM Online) usando a mesma senha usada para fazer logon em sua rede local. Para usar esse recurso, você precisa instalar os serviços de sincronização do Azure Active Directory Connect (sincronização do Azure AD Connect).


> [AZURE.NOTE] Para obter mais detalhes sobre os Serviços de Domínio do Active Directory, que são configurados para sincronização de senha e FIPS, consulte[Sincronização de senha e FIPS](#password-synchronization-and-fips).

## Como a sincronização de senha funciona

A sincronização de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Esse recurso requer sincronização de diretórios entre seu AD local e o Azure Active Directory a ser configurado.

O serviço de domínio do Active Directory armazena as senhas na forma de uma representação do valor de hash da senha de usuário real. Você não pode usar um hash de senha para:

- Entrar sua rede local

- Revertê-la para a versão de texto sem formatação de uma senha

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. O fluxo de dados real do processo de sincronização de senha é semelhante à sincronização de dados de usuário como DisplayName ou Endereços de Email.

As senhas são sincronizadas:

- Por usuário

- Em ordem cronológica

- Com mais frequência do que a janela de sincronização de diretório padrão para outros atributos

Uma senha sincronizada substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar.

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos. O recurso de sincronização de senha repete automaticamente sincronizações de senha de usuário com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não afeta um usuário conectado atualmente. Se uma alteração de senha for sincronizada enquanto você estiver conectado em um serviço de nuvem, sua sessão de serviço de nuvem não será afetada imediatamente. No entanto, assim que o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

> [AZURE.NOTE] Somente há suporte para a sincronização de senha para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### Como a sincronização de senha funciona com os Serviços de Domínio do AD do Azure

Se você habilitar esse serviço no AD do Azure, a opção de sincronização de senha será necessária para obter uma experiência de logon único. Com esse serviço habilitado, o comportamento de sincronização de senha é alterado e os hashes de senha também são sincronizados como estão no seu Active Directory local com os Serviços de Domínio do AD do Azure. A funcionalidade é semelhante à da Ferramenta de Migração do Active Directory (ADMT) e permite que os Serviços de Domínio do AD do Azure sejam capazes de autenticar o usuário com todos os métodos disponíveis no AD local.

### Considerações de segurança

Ao sincronizar senhas, a versão da sua senha em texto sem formatação não é exposta ao recurso de sincronização de senha, nem ao AD do Azure ou qualquer um dos serviços associados.

Além disso, não há nenhum requisito no Active Directory local para armazenar a senha em um formato criptografado de modo reversível. Um resumo do hash da senha do Active Directory é usado para a transmissão entre o AD local e o Active Directory do Azure. O resumo do hash da senha não pode ser usado para acessar recursos no seu ambiente do local.

### Considerações sobre política de senha

Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de senha:

1. Política de complexidade de senha
2. Política de expiração de senha

**Política de complexidade de senha**

Quando você habilita a sincronização de senha, as políticas de complexidade de senha configuradas no Active Directory local substituem qualquer política de complexidade que possa estar definida na nuvem para usuários sincronizados. Isso significa que qualquer senha válida no ambiente de Active Directory local do cliente pode ser usada para acessar os serviços do AD do Azure.

> [AZURE.NOTE] Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

**Política de expiração de senha**

Se um usuário estiver no escopo de sincronização de senha, a senha da conta de nuvem será definida como "*Nunca Expirar*". Isso significa que você pode continuar a fazer logon nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local.

A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

### Substituindo senhas sincronizadas

Um administrador pode redefinir sua senha manualmente usando o PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem se aplicam à nova senha.

Se você alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.


## Habilitando a sincronização de senha

Para habilitar a sincronização de senha, você tem duas opções:

- Se você usar configurações expressas ao instalar o Azure AD Connect, a sincronização de senha será habilitada por padrão.

- Se você usar configurações personalizadas quando instalar o Azure AD Connect, poderá habilitar a sincronização de senha na página de entrada do usuário.

<br> ![Habilitando a sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png) <br>

Se você optar por usar **Federação com o AD FS**, é possível opcionalmente habilitar a sincronização de senha como um backup em caso de falha na infraestrutura do AD FS. Você também poderá habilitá-la se planejar usar os Serviços de Domínio do AD do Azure.

### Sincronização de senha e FIPS

Se o servidor foi bloqueado de acordo com o FIPS (Federal Information Processing Standard), o MD5 foi desabilitado. Para habilitá-lo para sincronização de senha, adicione a chave enforceFIPSPolicy em miiserver.exe.config em C:\\Program Files\\Azure AD Sync\\Bin.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

O nó configuração/tempo de execução pode ser encontrado no final do arquivo de configuração.

Para obter informações sobre segurança e FIPS, veja [Sincronização de senha do AAD, criptografia e conformidade FIPS](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)


## Solução de problemas de sincronização de senha

**Para solucionar problemas de sincronização de senha, execute as seguintes etapas:**

1. Abra o **Synchronization Service Manager**

2. Clique em **Conectores**

3. Selecione o Conector do Active Directory em que o usuário está localizado

4. Selecione **Pesquisar Espaço do Conector**

5. Localize o usuário que você está procurando.

6. Selecione a guia **linhagem** e verifique se pelo menos uma Regra de Sincronização mostra **Sincronização de Senha** como **True**. Na configuração padrão, o nome da Regra de Sincronização é **Entrada do AD - Conta do Usuário Habilitada**.

    ![Informações de linhagem sobre um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. Você também deve [seguir o usuário](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) por meio do metaverso até o espaço do Azure AD Connector. O objeto de espaço do conector deve ter uma regra de saída com a **Sincronização de Senha** definida como **True**. Na configuração padrão, o nome da regra de sincronização é **Saída do AAD - Ingresso do Usuário**.

    ![Propriedades de espaço do conector de um usuário](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. Para ver os detalhes da sincronização de senha do objeto, clique no botão **Log...**.<br> Isso cria uma página com uma exibição do histórico de status de sincronização de senha do usuário na última semana.

    ![Detalhes de log do objeto](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

A coluna de status pode ter os seguintes valores:

| Status | Descrição |
| ---- | ----- |
| Sucesso | A senha foi sincronizada com êxito. |
| FilteredByTarget | A senha está definida para **O usuário deve alterar a senha no próximo logon**. A senha não foi sincronizada. |
| NoTargetConnection | Nenhum objeto no metaverso ou no espaço conector do AD do Azure. |
| SourceConnectorNotPresent | Nenhum objeto encontrado no espaço conector do Active Directory local. |
| TargetNotExportedToDirectory | O objeto no espaço conector do AD do Azure ainda não foi exportado. |
| MigratedCheckDetailsForMoreInfo | A entrada de log foi criada antes da versão 1.0.9125.0 e é mostrada em seu estado herdado. |


## Disparar uma sincronização completa de todas as senhas

Normalmente, não é necessário forçar uma sincronização completa de todas as senhas.<br> No entanto, se necessário, você pode disparar uma sincronização completa de todas as senhas usando o seguinte script:

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Próximas etapas

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0420_2016-->