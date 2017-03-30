---
title: "Implementação de sincronização de senha com a sincronização do Azure AD Connect | Microsoft Docs"
description: "Fornece informações sobre como funciona a sincronização de senha e como habilitá-la."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 946f135e832667ad6e32743be2b07b4f86cd1cae
ms.lasthandoff: 03/22/2017


---
# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementação de sincronização de senha com a sincronização do Azure AD Connect
Este tópico fornece as informações necessárias para sincronizar suas senhas de usuário do AD (Active Directory) local para um Azure AD (Azure Active Directory) baseado na nuvem.

## <a name="what-is-password-synchronization"></a>O que é sincronização de senha
A probabilidade de você ser impedido de concluir seu trabalho devido a uma senha esquecida está relacionada ao número de senhas diferentes que você precisa se lembrar. Quanto mais senhas você precisa lembrar, maior a probabilidade de se esquecer uma delas. Perguntas e chamadas sobre redefinições de senha e outros problemas relacionados à senha demandam a maior parte dos recursos de assistência técnica.

A sincronização de senha é um recurso para sincronizar senhas de usuário de um Active Directory local para um Azure Active Directory (AD do Azure) local.
Esse recurso permite que você entre nos serviços do Azure Active Directory (como Office 365, Microsoft Intune, CRM Online e Serviços de Domínio do Azure AD) usando a mesma senha usada para entrar no Active Directory local.

![O que é o Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Reduzindo o número de senhas que os usuários precisam manter para apenas uma, a sincronização de senha ajuda a:

* Aumentar a produtividade dos seus usuários
* Reduzir os custos de assistência técnica  

Se você optar por usar a [**Federação com o AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), outra opção será habilitar a sincronização de senha como um backup em caso de falha na infraestrutura do AD FS.

A sincronização de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Para usar a sincronização de senha no seu ambiente, você precisa:

* Instalar o Azure AD Connect  
* Configure a sincronização de diretórios entre seu AD local e o Azure Active Directory
* Habilitar a sincronização de senha

Para saber mais, consulte [Integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> Para obter mais detalhes sobre os Serviços de Domínio do Active Directory, que são configurados para sincronização de senha e FIPS, consulte [Sincronização de senha e FIPS](#password-synchronization-and-fips).
>
>

## <a name="how-password-synchronization-works"></a>Como a sincronização de senha funciona
O serviço de domínio do Active Directory armazena as senhas na forma de uma representação do valor de hash da senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o "*algoritmo de hash*"). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. Não é possível usar o hash de senha para entrar na sua rede local.

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. As senhas são sincronizadas por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de senha é semelhante à sincronização de dados de usuário como DisplayName ou Endereços de Email. Contudo, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma senha, ela substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar.

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos.
O recurso de sincronização de senha repete automaticamente tentativas de sincronização com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não afeta um usuário conectado atualmente.
Sua sessão de serviço de nuvem atual não será afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está logado em um serviço de nuvem. No entanto, quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

Uma limitação é que o usuário deve inserir suas credenciais corporativas uma segunda vez para autenticar no Azure AD, independentemente se ele está conectado à rede corporativa. Porém, esse padrão pode ser minimizado se o usuário marcar a caixa de seleção "Manter-me conectado" (KMSI) durante o logon. Isso define um cookie de sessão que ignora a autenticação por um curto período. O comportamento KMSI pode ser habilitado ou desabilitado pelo administrador do Azure Active Directory.

> [!NOTE]
> Somente há suporte para a sincronização de senha para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-synchronization-works"></a>Descrição detalhada de como funciona a sincronização de senha
Veja a seguir uma descrição detalhada de como funciona a sincronização de senha entre o Active Directory e o Azure Active Directory.

![Fluxo da senha detalhado](./media/active-directory-aadconnectsync-implement-password-synchronization/arch3.png)


1. A cada dois minutos, o agente de sincronização de senha no servidor do AD Connect solicita hashes de senha armazenados (o atributo unicodePwd) de um controlador de domínio por meio do protocolo de replicação padrão [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usado para sincronizar dados entre controladores de domínio. A conta do serviço deve ter as permissões do AD Replicar Alterações de Diretório e Replicar Todas as Alterações de Diretório (concedidas por padrão na instalação) para obter os hashes de senha.
2. Antes de enviar, o controlador de domínio criptografa o hash de senha MD4 usando uma chave que é um hash [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um valor de sal. Em seguida, ele envia o resultado para o agente de sincronização de senha por RPC. O controlador de domínio também passa o sal para o agente de sincronização usando o protocolo de replicação do controlador de domínio, para que o agente possa descriptografar o envelope.
3.    Quando o agente de sincronização de senha tiver o envelope criptografado, ele usará [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o sal para gerar uma chave para descriptografar os dados recebidos de volta para seu formato original de MD4. Em nenhum momento, o agente de sincronização de senha tem acesso à senha de texto não criptografado. Uso que o agente de sincronização de senha faz do MD5 é estritamente para compatibilidade de protocolo de replicação com o controlador de domínio, e é usado somente no local entre o controlador de domínio e o agente de sincronização de senha.
4.    O agente de sincronização de senha expande o hash de senha binária de 16 bits para 64 bytes convertendo primeiro o hash em uma cadeia hexadecimal de 32 bytes, depois convertendo essa cadeia de caracteres de volta para o binário com a codificação UTF-16.
5.    O agente de sincronização de senha adiciona um sal, composto por um sal de 10 bytes, para o binário de 64 bits a fim de proteger ainda mais o hash original.
6.    Depois, o agente de sincronização de senha combina o hash MD4 e o sal com entradas na função [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt), usando 1000 iterações do algoritmo de hash com chave [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx).
AD do Azure 
7.    O agente de sincronização de senha usa o hash resultante de 32 bytes, concatena o sal e o número de iterações SHA256 a ele (para ser usado pelo Azure AD) e transmite a cadeia de caracteres do AD Connect para o Azure AD por SSL.</br> 
8.    Quando um usuário tenta fazer logon no Azure AD e insere sua senha, a senha é executada por meio do mesmo processo de MD4 + sal + PBKDF2 + HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado no Azure AD, isso significa que o usuário digitou a senha correta e será autenticado. 

>[!Note] 
>O hash MD4 original não é transmitido ao Azure AD; em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado no Azure AD for obtido, ele não poderá ser usada em um ataque de passagem de hash no local.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Como a sincronização de senha funciona com os Serviços de Domínio do AD do Azure
Você também pode usar o recurso de sincronização de senha para sincronizar suas senhas locais para os [Serviços de Domínio do Azure AD](../../active-directory-domain-services/active-directory-ds-overview.md). Esse cenário permite que os Serviços de Domínio do Azure AD autentiquem os usuários na nuvem com todos os métodos disponíveis no AD local. A experiência desse cenário é semelhante a usar a ADMT (Ferramenta de Migração do Active Directory) em um ambiente local.

### <a name="security-considerations"></a>Considerações de segurança
Ao sincronizar senhas, a versão da sua senha em texto sem formatação não é exposta ao recurso de sincronização de senha, nem ao Azure AD ou qualquer um dos serviços associados.

A autenticação do usuário ocorre no Azure AD e não no próprio Active Directory da organização. Se sua organização se preocupar com a saída de dados de senha da empresa, considere o fato de que os dados de senha SHA256 armazenados no Azure AD - um hash do hash MD4 original - são consideravelmente mais seguros do que o que está armazenado no Active Directory. Além disso, como não é possível descriptografar esse hash SHA256, ele não pode ser levado de volta ao ambiente do Active Directory da organização e apresentado como uma senha de usuário válida em um ataque de passagem de hash.





### <a name="password-policy-considerations"></a>Considerações sobre política de senha
Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de senha:

1. Política de complexidade de senha
2. Política de expiração de senha

**Password complexity policy**  
Ao habilitar a sincronização de senha, as políticas de complexidade de senha no Active Directory local substituem as políticas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas do seu Active Directory local para acessar os serviços do Azure AD.

> [!NOTE]
> Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

**Password expiration policy**  
Se um usuário estiver no escopo de sincronização de senha, a senha da conta de nuvem será definida como "*Nunca Expirar*".

Você pode continuar entrando nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

**Expiração da conta** Se sua organização usar o atributo accountExpires como parte do gerenciamento de contas de usuário, lembre-se de que esse atributo não está sincronizado no Azure AD. Como resultado, uma conta expirada do AD em um ambiente configurado para sincronização de senha ainda estará ativa no Azure AD. Recomendamos que, se a conta expirar, uma ação de fluxo de trabalho deverá disparar um script do PowerShell que desabilita a conta de usuário do Azure AD. Por outro lado, quando a conta estiver habilitada, o Azure AD deverá estar habilitado.

### <a name="overwriting-synchronized-passwords"></a>Substituindo senhas sincronizadas
Um administrador pode redefinir sua senha manualmente usando o Windows PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem se aplicam à nova senha.

Se você alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.

A sincronização de uma senha não afeta um usuário conectado atualmente no Azure. Sua sessão de serviço de nuvem atual não será afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está logado em um serviço de nuvem. KMSI ampliará a duração dessa diferença. Quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

### <a name="additional-advantages"></a>Vantagens adicionais

- Em geral, a sincronização de senha é mais simples de implementar do que um serviço de federação. Ela não exige quaisquer servidores adicionais e elimina a dependência de um serviço de federação altamente disponível para autenticar usuários. 
- A sincronização de senha também pode ser habilitada, além da federação, para que seja usada como um fallback se o seu serviço de federação sofrer uma interrupção.












## <a name="enabling-password-synchronization"></a>Habilitando a sincronização de senha
A sincronização de senha é habilitada automaticamente quando você instala o Azure AD Connect usando as **Configurações Expressas**. Para obter mais detalhes, confira [Introdução ao Azure AD Connect usando configurações expressas](active-directory-aadconnect-get-started-express.md).

Se você usar configurações personalizadas quando instalar o Azure AD Connect, poderá habilitar a sincronização de senha na página de entrada do usuário. Para obter mais detalhes, confira [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Habilitando a sincronização de senha](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronização de senha e FIPS
Se o servidor foi bloqueado de acordo com o FIPS (Federal Information Processing Standard), o MD5 foi desabilitado.

**Para habilitar MD5 para a sincronização de senha, execute as seguintes etapas:**

1. Vá para **%programfiles%\Azure AD Sync\Bin**.
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

## <a name="troubleshooting-password-synchronization"></a>Solução de problemas de sincronização de senha
Caso tenha problemas com a sincronização de senha, consulte [Solucionar problemas de sincronização de senha](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="next-steps"></a>Próximas etapas
* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

