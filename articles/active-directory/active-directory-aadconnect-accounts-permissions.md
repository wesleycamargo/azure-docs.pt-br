<properties
   pageTitle="Azure AD Connect: contas e permissões | Microsoft Azure"
   description="Este tópico descreve as contas usadas e criadas e as permissões necessárias."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="02/16/2016"
   ms.author="andkjell;billmath"/>


# Azure AD Connect: contas e permissões

O assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

- Em Configurações Expressas, exigimos mais privilégios para que possamos definir sua configuração facilmente, sem que você precise criar usuários ou configurar permissões separadamente.

- Em Configurações Personalizadas, oferecemos mais opções, mas há algumas situações em que você precisará ter as permissões corretas por conta própria.

## Documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados.

| Tópico | |
| --------- | --------- |
| Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## Instalação das configurações expressas
As configurações expressas do assistente de instalação solicitará credenciais de administrador corporativo do AD DS para que o Active Directory local possa ser configurado com as permissões necessárias para o Azure AD Connect. Se você estiver atualizando o DirSync, as credenciais de administrador corporativo do AD DS serão usadas para redefinir a senha da conta usada pelo DirSync. Você também precisará de credenciais de Administrador Global do AD do Azure.

Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |------------- |
N/D|Usuário que está executando o assistente de instalação| Administrador do servidor local| <li>Cria a conta local que será usada como a [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account).
Conecte-se ao AD do Azure| Credenciais de diretório do AD do Azure | Função de administrador global no AD do Azure | <li>Habilitar a sincronização no diretório do AD do Azure.</li> <li>Criação da [conta do AD do Azure](#azure-ad-service-account) que será usada para operações de sincronização em andamento no AD do Azure.</li>
Conectar-se ao AD DS | Credenciais do Active Directory local | Membro do grupo EA (Administradores de Empresa) no Active Directory| <li>Cria uma [conta](#active-directory-account) no Active Directory e concede permissões a ela. Essa conta criada é usada para ler e gravar informações de diretório durante a sincronização.</li>

### Credenciais de administrador corporativo
Essas credenciais são usadas apenas durante a instalação e não serão usadas após a conclusão da instalação. São de administrador corporativo e não administrador de domínio, para garantir que as permissões no Active Directory possam ser definidas em todos os domínios.

### Credenciais de administrador global
Essas credenciais são usadas apenas durante a instalação e não serão usadas após a conclusão da instalação. São usadas para criar a [conta do AD do Azure](#azure-ad-service-account) usada para sincronizar alterações com o AD do Azure. A conta também habilitará a sincronização como um recurso no AD do Azure.

### Permissões de configurações expressas para a conta do AD DS criada
A [conta](#active-directory-account) criada para leitura e gravação no AD DS terá as seguintes permissões se for criada pelas configurações expressas:

| Permissão | Usado para |
| ---- | ---- |
| <li>Replicar alterações de diretório</li> <li>Replicar todas as alterações de diretório | Sincronização de senha |
| Ler/Gravar todas as propriedades Usuário | Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades iNetOrgPerson | Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Grupo | Importação e Exchange híbrido |
| Ler/Gravar todas as propriedades Contato | Importação e Exchange híbrido |
| Redefinir senha | Preparação para habilitar write-back de senha |

## Instalação de configurações personalizadas
Ao usar configurações personalizadas, a conta usada para conectar-se ao Active Directory deve ser criada antes da instalação. As permissões que você deve conceder a essa conta podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account).

Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |-------------
N/D|Usuário que está executando o assistente de instalação|<li>Administrador do servidor local</li><li>Se usar um SQL Server completo, o usuário deve ser o administrador do sistema (SA) no SQL</li>| Por padrão, cria a conta local que será usada como [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account). A conta é criada somente se o administrador não definir uma conta específica.
Instalar serviços de sincronização, opção de conta de serviço | Credenciais de conta de usuário local ou AD | Usuário, permissões serão concedidas pelo assistente de instalação|Se o administrador especificar uma conta, essa conta será usada como a conta de serviço para o serviço de sincronização.
Conecte-se ao AD do Azure|Credenciais de diretório do AD do Azure| Função de administrador global no AD do Azure| <li>Habilitar a sincronização no diretório do AD do Azure.</li> <li>Criação da [conta do AD do Azure](#azure-ad-service-account) que será usada para operações de sincronização em andamento no AD do Azure.</li>
Conectar seus diretórios|Credenciais do Active Directory para cada floresta que será conectada ao AD do Azure local | As permissões dependerão de quais recursos você habilita e podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account) |Essa conta é usada para ler e gravar informações de diretório durante a sincronização.
Servidores do AD FS|Para cada servidor na lista, o assistente coletará credenciais se as credenciais de logon do usuário que executa o assistente não forem suficientes para conectar-se|Administrador de domínio|Instalação e configuração da função de servidor do AD FS.
Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coletará credenciais se as credenciais de logon do usuário que executa o assistente não forem suficientes para conectar-se|Administrador local no computador de destino|Instalação e configuração da função de servidor WAP.
Credenciais de confiança de proxy |As credenciais de confiança do serviço de federação (as credenciais que o proxy usará para se registrar para um certificado de confiança do FS |Conta de domínio que é administrador local do servidor do AD FS|Registro inicial de certificado de confiança FS-WAP.
Página da conta de serviço do AD FS, "Usar uma opção de conta de usuário de domínio"|Credenciais de conta de usuário do AD|Usuário de domínio|A conta de usuário do AD cujas credenciais fore fornecidas será usada como a conta de logon do serviço do AD FS.

### Criar a conta do AD DS
Quando você instala o Azure AD Connect, a conta que especifica na página **Conectar seus diretórios** deve estar presente no Active Directory e ter as permissões necessárias concedidas. O assistente de instalação não verificará as permissões e qualquer problema só será identificado durante a sincronização.

As permissões de que você precisa dependem dos recursos opcionais que habilitar. Se você tiver vários domínios, as permissões devem ser concedidas para todos os domínios na floresta. Se você não habilitar esses recursos, as permissões padrão **Usuário de domínio** serão suficientes.

| Recurso | Permissões |
| ------ | ------ |
| Sincronização de senha | <li>Replicar alterações de diretório</li> <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange | Permissões de gravação para os atributos documentados em [Write-back híbrido do Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Write-back de senha | Permissões de gravação para os atributos documentados em [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) para usuários. |
| Write-back de dispositivo | Permissões concedidas com um script do PowerShell, conforme descrito em [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md).|
| Write-back de grupo | Ler, criar, atualizar e excluir objetos de grupo na UO em que os grupos de distribuição devem estar localizados.|

## Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, você precisará das seguintes permissões:

| Principal | Permissões necessárias | Usado para |
| ---- | ---- | ---- |
| Usuário que está executando o assistente de instalação | Administrador do servidor local | Binários de atualização. |
| Usuário que está executando o assistente de instalação | Membro do ADSyncAdmins | Fazer alterações em Regras de Sincronização e outras configurações. |
| Usuário que está executando o assistente de instalação | Se usar um SQL Server completo: DBO (ou semelhante) do banco de dados de mecanismo de sincronização | Fazer alterações no nível de banco de dados, como a atualização de tabelas com novas colunas. |

## Mais informações sobre as contas criadas

### Conta do Active Directory

Se você usar configurações expressas, será criada uma conta no Active Directory que será usada para sincronização. A conta criada estará localizada no domínio raiz da floresta no contêiner Usuários e terá seu nome prefixado com **MSOL\_**. A conta é criada com uma senha longa complexa que não expira. Se você tiver uma política de senha em seu domínio, verifique se senhas longas e complexas são permitidas para esta conta.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Contas do serviço de sincronização do Azure AD Connect
Uma conta de serviço local é criada pelo assistente de instalação (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta prefixada com **AAD\_** é usada com o serviço de sincronização real para ser executada como. Se você instalar o Azure AD Connect em um controlador de domínio, a conta é criada no domínio. Se você usar um SQL Server em um servidor remoto, a conta de serviço **AAD\_** deverá estar localizada no domínio.

![Conta de serviço de sincronização](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha longa complexa que não expira.

Essa conta será usada pelo Windows para armazenar as chaves de criptografia, então a senha dessa conta não deve ser redefinida ou alterada.

Se você usar um SQL Server completo, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme esperado com qualquer outra permissão. Um logon SQL também será criado.

A conta também recebe permissões para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização.

### Conta de serviço do AD do Azure
Uma conta no AD do Azure será criada para uso do serviço de sincronização. Essa conta pode ser identificada com seu nome de exibição.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

O nome do servidor em que a conta é usada em pode ser identificado na segunda parte do nome do usuário. Na figura acima, nome do servidor é FABRIKAMCON. Se você tiver servidores de teste, cada servidor terá sua própria conta. Há um limite de 10 contas de serviço de sincronização no AD do Azure.

A conta de serviço é criada com uma senha longa e complexa que não expira. Ela recebe uma função especial **Contas de sincronização de diretório** que tem somente permissões para executar tarefas de sincronização de diretório. Essa função interna especial não pode ser concedida fora do assistente do Azure AD Connect e o portal do Azure apenas mostrará essa conta com a função **Usuário**.

![Função de conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Próximas etapas

Saiba mais sobre [Como integrar suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->