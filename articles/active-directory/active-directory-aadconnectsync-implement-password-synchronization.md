<properties
	pageTitle="Azure AD Connect Sync - Implementar sincronização de senha | Microsoft Azure"
	description="Fornece a você as informações que você precisa para compreender como funciona a sincronização de senha e como habilitá-la em seu ambiente."
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
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: implementar a sincronização de senha

Com a sincronização de senha, você habilita seus usuários a usar, para fazer logon no Active Directory do Azure, a mesma senha que usada para entrar no seu Active Directory local.

O objetivo deste tópico é fornecer a você as informações que você precisa para compreender como funciona a sincronização de senha e como habilitá-la em seu ambiente.

## O que é sincronização de senha

A sincronização de senha é um recurso dos Azure Active Directory Connect Synchronization Services (Azure AD Connect Sync) que sincroniza as senhas de usuário do seu Active Directory local com as do Active Directory do Azure (AD do Azure). Esse recurso permite que os usuários façam logon em seus serviços do Active Directory do Azure (como Office 365, Microsoft Intune e CRM Online) usando a mesma senha que eles usam para fazer logon em sua rede local.

> [AZURE.NOTE]Para obter mais detalhes sobre os Serviços de Domínio do Active Directory, que são configurados para sincronização de senha e FIPS, consulte Falha na sincronização de senha em sistemas compatíveis com FIPS.

## Disponibilidade de sincronização de senha

Qualquer cliente do Active Directory do Azure é elegível para executar a sincronização de senha. Obtenha a seguir informações sobre a compatibilidade de sincronização de senha e outros recursos como Autenticação Federada.

## Como a sincronização de senha funciona

A sincronização de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Como consequência disso, esse recurso requer sincronização de diretórios entre seu Active Directory local e o Active Directory do Azure a ser configurado.

O serviço de domínio do Active Directory armazena as senhas na forma de uma representação do valor de hash da senha de usuário real. O hash de senha não pode ser usado para fazer logon na sua rede local. Ele também é projetado para que não possa ser revertido visando obter acesso à senha de usuário, em formato de texto sem formatação. Para sincronizar uma senha, o Azure AD Connect Sync extrai o hash da senha de usuário do Active Directory local. Processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Active Directory do Azure. O fluxo de dados real do processo de sincronização de senha é semelhante à sincronização de dados de usuário como DisplayName ou Endereços de Email.

As senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. As senhas são sincronizadas por usuário e geralmente em ordem cronológica. Quando a senha de usuário do AD local é sincronizada com a de nuvem, a senha de nuvem existente será substituída.

Quando você habilita o recurso de sincronização de senha pela primeira vez, ele executará uma sincronização inicial das senhas de todos os usuários no escopo, do seu Active Directory local ao Active Directory do Azure. Você não pode definir explicitamente o conjunto de usuários cujas senhas serão sincronizadas com a nuvem. Subsequentemente, após uma senha ter sido alterada por um usuário local, o recurso de sincronização de senha detecta e sincroniza a senha alterada, geralmente em questão de minutos. O recurso de sincronização de senha repete automaticamente sincronizações de senha de usuário com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, o erro é registrado no seu visualizador de eventos.

A sincronização de senha não tem impacto sobre usuários conectados atualmente. Se um usuário que está conectado a um serviço de nuvem também altera a senha local, a sessão de serviço de nuvem continuará sem interrupção. No entanto, assim que o serviço de nuvem exigir nova autenticação do usuário, a nova senha deverá ser fornecida. Nesse ponto, o usuário deve fornecer a nova senha - a senha do Active Directory local que foi sincronizada recentemente com a nuvem.

## Considerações de segurança

Ao sincronizar senhas, a versão da senha do usuário em texto sem formatação não é exposta ao recurso de sincronização de senha, nem ao AD do Azure ou qualquer um dos serviços associados.

Além disso, não há nenhum requisito no Active Directory local para armazenar a senha em um formato criptografado de modo reversível. Um resumo do hash da senha do Active Directory é usado para a transmissão entre o AD local e o Active Directory do Azure. O resumo do hash da senha não pode ser usado para acessar recursos no ambiente do local do cliente.

## Considerações sobre política de senha

Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de senha:

1. Política de complexidade de senha
2. Política de expiração de senha

### Política de complexidade de senha

Quando você habilita a sincronização de senha, as políticas de complexidade de senha configuradas no Active Directory local substituem qualquer política de complexidade que possa estar definida na nuvem para usuários sincronizados. Isso significa que qualquer senha válida no ambiente de Active Directory local do cliente pode ser usada para acessar os serviços do AD do Azure.


> [AZURE.NOTE]Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

### Política de expiração de senha

Se um usuário estiver no escopo de sincronização de senha, a senha da conta de nuvem é definida como "*Never Expire*". Isso significa que é possível que a senha do usuário expire no ambiente local, mas ele podem continuar a fazer logon nos serviços de nuvem usando essa senha expirada.

A senha de nuvem será atualizada na próxima vez que o usuário alterar a senha no ambiente local.


## Substituindo senhas sincronizadas

Um administrador pode redefinir manualmente a senha de usuário usando o Active Directory PowerShell do Azure.

Nesse caso, a nova senha substituirá a senha do usuário sincronizada e todas as políticas de senha definidas na nuvem se aplicarão à nova senha.

Se o usuário alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.


## Preparando para a sincronização de senha

Seu locatário do Active Directory do Azure deve ser habilitado para sincronização de diretório antes que o locatário possa ser habilitado para sincronização de senha.


## Habilitando a sincronização de senha

Você habilita a sincronização de senha ao executar o Assistente de configuração do Azure AD Connect.

Na página de diálogo **Recursos opcionais**, selecione “**Sincronização de senha**”.

![Recursos opcionais][1]


> [AZURE.NOTE]Este processo dispara uma sincronização completa. Ciclos de sincronização completa geralmente levam mais tempo para ser concluídos do que outros ciclos de sincronização.


## Gerenciando a sincronização de senha

Você pode monitorar o progresso da sincronização de senha por meio do log de eventos do computador que está executando o Azure AD Connect.


### Determinar o status de sincronização de senha

Você pode determinar quais usuários tiveram suas senhas sincronizadas com êxito, examinando os eventos que correspondem aos seguintes critérios:

| Fonte| ID do evento |
| --- | --- |
| Sincronização de diretório| 656|
| Sincronização de diretório| 657|

Os eventos com a ID de evento 656 fornecem um relatório de solicitações de alteração de senha processadas:

![ID do evento 656][2]

Os eventos correspondentes com a ID 657 fornecem o resultado para essas solicitações:

![ID do evento 657][3]

Nos eventos, os objetos afetados são identificados por sua âncora e pelo valor de DN. O valor da âncora corresponde ao valor de **ImmutableId**, que é retornado a um usuário pelo cmdlet Get-MsoUser.

Além dos identificadores de objeto, a **ID de evento 656** fornece a data em que a senha do usuário foi alterada no Active Directory local:

![Solicitação de alteração de senha][4]

A ID de evento 657 tem um campo Resultado, além os identificadores de objeto de origem, para indicar o status da sincronização para esse objeto de usuário.

Uma senha sincronizada com êxito está em um evento com a ID de evento 657, indicada por um valor Êxito para o atributo Resultado. Quando uma tentativa de sincronização de senha falha, o valor do atributo Resultado é Falha:

![Resultado de alteração de senha][5]

### Disparar uma sincronização completa de todas as senhas
Se você tiver alterado a configuração do filtro, será necessário disparar uma sincronização completa de todas as senhas para que os usuários, agora no escopo, tenham suas senhas sincronizadas.

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


## Desabilitando a sincronização de senha

Você desabilita a sincronização de senha ao executar novamente o Assistente de configuração do Azure AD Connect. Quando solicitado pelo Assistente, desmarque a caixa de seleção "Sincronização de senha".


> [AZURE.NOTE]Este processo dispara uma sincronização completa. Ciclos de sincronização completa geralmente levam mais tempo para ser concluídos do que outros ciclos de sincronização.

Após a execução do Assistente de configuração, seu locatário deixará de sincronizar senhas. Novas alterações de senha não serão sincronizadas com a nuvem. Os usuários que sincronizaram previamente suas senhas poderão continuar fazendo logon com elas até que alterem manualmente suas senhas na nuvem.



## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=Oct15_HO3-->