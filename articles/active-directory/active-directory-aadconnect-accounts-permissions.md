<properties
   pageTitle="Azure AD Connect: contas e permissões | Microsoft Azure"
   description="Este tópico descreve as contas usadas e criadas e as permissões necessárias."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/13/2016"
   ms.author="andkjell;billmath"/>


# Azure AD Connect: contas e permissões
O assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

- Em Configurações Expressas, o assistente requer mais privilégios para que possa definir sua configuração facilmente, sem que você precise criar usuários ou configurar permissões separadamente.

- Em Configurações Personalizadas, o assistente oferece mais opções, mas há algumas situações em que você precisa ter as permissões corretas por conta própria.

## Documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados.

Tópico |  
--------- | ---------
Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## Instalação das configurações expressas
As configurações expressas do assistente de instalação solicita credenciais de administrador corporativo do AD DS para que o Active Directory local possa ser configurado com as permissões necessárias para o Azure AD Connect. Se você estiver atualizando o DirSync, as credenciais de administrador corporativo do AD DS serão usadas para redefinir a senha da conta usada pelo DirSync. Você também precisa de credenciais de Administrador Global do Azure AD.

Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |-------------
N/D|Usuário que está executando o assistente de instalação| Administrador do servidor local| <li>Cria a conta local que é usada como a [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account).
Conecte-se ao AD do Azure| Credenciais de diretório do AD do Azure | Função de administrador global no AD do Azure | <li>Habilitar a sincronização no diretório do AD do Azure.</li> <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é usada para operações de sincronização em andamento no Azure AD.</li>
Conectar-se ao AD DS | Credenciais do Active Directory local | Membro do grupo EA (Administradores de Empresa) no Active Directory| <li>Cria uma [conta](#active-directory-account) no Active Directory e concede permissões a ela. Essa conta criada é usada para ler e gravar informações de diretório durante a sincronização.</li>

### Credenciais de administrador corporativo
Essas credenciais são usadas apenas durante a instalação e são usadas após a conclusão da instalação. São de administrador corporativo e não administrador de domínio, para garantir que as permissões no Active Directory possam ser definidas em todos os domínios.

### Credenciais de administrador global
Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. São usadas para criar a [conta do AD do Azure](#azure-ad-service-account) usada para sincronizar alterações com o AD do Azure. A conta também habilita a sincronização como um recurso no Azure AD.

### Permissões de configurações expressas para a conta do AD DS criada
A [conta](#active-directory-account) criada para leitura e gravação no AD DS tem as seguintes permissões se é criada pelas configurações expressas:

Permissão | Usado para
---- | ----
<li>Replicar alterações de diretório</li> <li>Replicar todas as alterações de diretório | Sincronização de senha
Ler/Gravar todas as propriedades Usuário | Importação e Exchange híbrido
Ler/Gravar todas as propriedades iNetOrgPerson | Importação e Exchange híbrido
Ler/Gravar todas as propriedades Grupo | Importação e Exchange híbrido
Ler/Gravar todas as propriedades Contato | Importação e Exchange híbrido
Redefinir senha | Preparação para habilitar write-back de senha

## Instalação de configurações personalizadas
Ao usar configurações personalizadas, a conta usada para conectar-se ao Active Directory deve ser criada antes da instalação. As permissões que você deve conceder a essa conta podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account).

Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |-------------
N/D | Usuário que está executando o assistente de instalação|<li>Administrador do servidor local</li><li>Se usar um SQL Server completo, o usuário deve ser o administrador do sistema (SA) no SQL</li>| Por padrão, cria a conta local que é usada como [conta de serviço do mecanismo de sincronização](#azure-ad-connect-sync-service-account). A conta é criada somente quando o administrador não define uma conta específica.
Instalar serviços de sincronização, opção de conta de serviço | Credenciais de conta de usuário local ou AD | Usuário, permissões são concedidas pelo assistente de instalação | Se o administrador especificar uma conta, essa conta será usada como a conta de serviço para o serviço de sincronização.
Conecte-se ao AD do Azure | Credenciais de diretório do AD do Azure| Função de administrador global no AD do Azure| <li>Habilitar a sincronização no diretório do AD do Azure.</li> <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é usada para operações de sincronização em andamento no Azure AD.</li>
Conectar seus diretórios | Credenciais do Active Directory para cada floresta que é conectada ao Azure AD local | As permissões dependem de quais recursos você habilita e podem ser encontradas em [Criar a conta do AD DS](#create-the-ad-ds-account) |Essa conta é usada para ler e gravar informações de diretório durante a sincronização.
Servidores do AD FS | Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de logon do usuário que executa o assistente não são suficientes para conectar-se | Administrador de domínio | Instalação e configuração da função de servidor do AD FS.
Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de logon do usuário que executa o assistente não são suficientes para conectar-se | Administrador local no computador de destino | Instalação e configuração da função de servidor WAP.
Credenciais de confiança de proxy |As credenciais de confiança do serviço de federação (as credenciais que o proxy usa para se registrar para um certificado de confiança do FS |Conta de domínio que é administrador local do servidor do AD FS | Registro inicial de certificado de confiança FS-WAP.
Página da conta de serviço do AD FS, "Usar uma opção de conta de usuário de domínio" | Credenciais de conta de usuário do AD | Usuário de domínio | A conta de usuário do AD cujas credenciais são fornecidas é usada como a conta de logon do serviço do AD FS.

### Criar a conta do AD DS
Quando você instala o Azure AD Connect, a conta que especifica na página **Conectar seus diretórios** deve estar presente no Active Directory e ter as permissões necessárias concedidas. O Assistente de instalação não verifica as permissões, e os problemas são encontrados somente durante a sincronização.

As permissões de que você precisa dependem dos recursos opcionais que habilitar. Se você tiver vários domínios, as permissões devem ser concedidas para todos os domínios na floresta. Se você não habilitar esses recursos, as permissões padrão **Usuário de domínio** serão suficientes.

Recurso | Permissões
------ | ------
Sincronização de senha | <li>Replicar alterações de diretório</li> <li>Replicar todas as alterações de diretório
Implantação híbrida do Exchange | Permissões de gravação para os atributos documentados em [Write-back híbrido do Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos.
Write-back de senha | Permissões de gravação para os atributos documentados em [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) para usuários.
Write-back de dispositivo | Permissões concedidas com um script do PowerShell, conforme descrito em [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md).
Write-back de grupo | Ler, criar, atualizar e excluir objetos de grupo na UO em que os grupos de distribuição devem estar localizados.

## Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, você precisa das seguintes permissões:

Principal | Permissões necessárias | Usado para
---- | ---- | ----
Usuário que está executando o assistente de instalação | Administrador do servidor local | Binários de atualização.
Usuário que está executando o assistente de instalação | Membro do ADSyncAdmins | Fazer alterações em Regras de Sincronização e outras configurações.
Usuário que está executando o assistente de instalação | Se você usa um SQL Server completo: DBO (ou semelhante) do banco de dados de mecanismo de sincronização | Fazer alterações no nível de banco de dados, como a atualização de tabelas com novas colunas.

## Mais informações sobre as contas criadas

### Conta do Active Directory
Se você usar configurações expressas, uma conta será criada no Active Directory e será usada para sincronização. A conta criada está localizada no domínio raiz da floresta no contêiner Usuários e tem seu nome prefixado com **MSOL\_**. A conta é criada com uma senha longa complexa que não expira. Se você tiver uma política de senha em seu domínio, verifique se senhas longas e complexas são permitidas para esta conta.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Contas do serviço de sincronização do Azure AD Connect
Uma conta de serviço local é criada pelo assistente de instalação (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta prefixada com **AAD_** é usada com o serviço de sincronização real para ser executada como. Se você instalar o Azure AD Connect em um controlador de domínio, a conta é criada no domínio. Se você usar um servidor remoto que execute um servidor SQL ou se usar um proxy que exija autenticação, a conta do serviço **AAD_** deverá estar localizada no domínio.

![Conta de serviço de sincronização](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha longa complexa que não expira.

Essa conta é usada para armazenar as senhas das outras contas de maneira segura. As senhas dessas outras contas são armazenadas criptografadas no banco de dados. As chaves privadas das chaves de criptografia são protegidas com a criptografia de chave secreta dos serviços de criptografia usando a DPAPI (API de Proteção de Dados) do Windows. A senha da conta de serviço não deverá ser redefinida, pois o Windows destruirá, em seguida, as chaves de criptografia por motivos de segurança.

Se você usar um SQL Server completo, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme esperado com qualquer outra permissão. Um logon do SQL também é criado.

A conta também recebe permissões para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização.

### Conta de serviço do AD do Azure
Uma conta no Azure AD é criada para uso do serviço de sincronização. Essa conta pode ser identificada com seu nome de exibição.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

O nome do servidor em que a conta é usada em pode ser identificado na segunda parte do nome do usuário. Na figura, nome do servidor é FABRIKAMCON. Se você tiver servidores de teste, cada servidor terá sua própria conta. Há um limite de 10 contas de serviço de sincronização no AD do Azure.

A conta de serviço é criada com uma senha longa e complexa que não expira. Ela recebe uma função especial **Contas de sincronização de diretório** que tem somente permissões para executar tarefas de sincronização de diretório. Essa função interna especial não pode ser concedida fora do assistente do Azure AD Connect e o portal do Azure apenas mostra essa conta com a função **Usuário**.

![Função de conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Próximas etapas

Saiba mais sobre [Como integrar suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0914_2016-->