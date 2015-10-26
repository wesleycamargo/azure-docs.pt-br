<properties
	pageTitle="Mais informações sobre permissões e as credenciais do Azure AD Connect | Microsoft Azure"
	description="Descrição de configurações personalizadas de credenciais e permissões do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Mais informações sobre permissões e as credenciais de Conexão do AD do Azure


O Assistente de Conexão do AD do Azure oferece dois caminhos diferentes com requisitos distintos de permissões:

* Em Configurações Expressas, exigimos mais privilégios para que possamos definir sua configuração facilmente, sem que você precise criar usuários ou configurar permissões separadamente.

* Em Configurações Personalizadas, oferecemos mais opções, mas há algumas situações em que você precisará ter as permissões corretas por conta própria.


## As credenciais que são coletadas e para que são usadas em uma instalação expressa

Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |------------- |
Conectar-se ao AD do Azure| Credenciais de diretório do AD do Azure | Função de administrador global no AD do Azure | <li>Habilitar a sincronização no diretório do AD do Azure.</li> <li>Criação da conta do AD do Azure que será usada para operações de sincronização em andamento no AD do Azure.</li>
Conectar-se ao AD DS | Credenciais do Active Directory local | Membro do grupo EA (Administradores de Empresa) no Active Directory| Usadas como a conta do Conector do AD local, ou seja, é a conta que lê e grava as informações do diretório para sincronização.
ND|Credenciais de logon do usuário que executa o assistente| Administrador do servidor local|O assistente cria a conta do AD que será usada como a conta de logon do serviço de sincronização no computador local.

<br> <br>


## As credenciais que são coletadas e para que são usadas em uma instalação personalizada


Página do assistente | Credenciais coletadas | Permissões necessárias| Usadas para
------------- | ------------- |------------- |-------------
ND|Credenciais de logon do usuário que executa o assistente|Administrador do servidor local| <li>Por padrão, o assistente cria a conta do AD que será usada como a conta de logon do serviço de sincronização no computador local</li><li>Só poderemos criar a conta de logon do serviço de sincronização se o administrador não especificar uma determinada conta</li> <li>A conta é um usuário local, a menos que se trate de um controlador de domínio. Nesse caso, a conta é um usuário de domínio</li>
Instalar a página de serviços de sincronização, opção de conta de Serviço | Credenciais de conta de usuário local ou AD | Usuário local|Se o administrador especificar uma conta, essa conta será usada como a conta de logon para o serviço de sincronização.
Conectar-se ao AD do Azure|Credenciais de diretório do AD do Azure| Função de administrador global no AD do Azure|O assistente cria a conta do AD que será usada como a conta de logon do serviço de sincronização no computador local.
Conectar seus diretórios|Credenciais locais do Active Directory para cada floresta que será conectada ao AD do Azure |<li>O nível mínimo de permissões exigidas pelo assistente é um usuário de Domínio.</li> <li>No entanto, a conta especificada deve ter as permissões necessárias para seu cenário pretendido.</li><li>Se você pretende configurar a sincronização de senha para o AD do Azure, verifique se essa conta tem as seguintes permissões atribuídas:-Replicar Alterações de Diretório - Replicação de Todas as Alterações de Diretório</li> <li>Se você pretende configurar a sincronização para fazer 'write-back' de informações do AD do Azure para seu AD local, verifique se a conta tem permissões de gravação para os objetos de diretório e atributos nos quais você pretende fazer write-back.</li> <li>Se você pretende configurar o AD FS para Entrada, verifique as credenciais que você fornecer ao AD para a floresta em que seus servidores do AD FS residem têm privilégios de Administrador de Domínio.</li><li>Consulte a tabela abaixo, com uma lista de requisitos adicionais para seu cenário.</li>|<li>Esta é a conta que será usada para a conta local do agente de gerenciamento do AD (MA). Ela será usada para ler e gravar objetivos e atributos no AD local para operação contínua de sincronização.</li>
Servidores do AD FS|Para cada servidor na lista, o assistente coletará credenciais se as credenciais de logon do usuário que executa o assistente não forem suficientes para conectar-se|Administrador de domínio|Instalação e configuração da função de servidor do AD FS.|
Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coletará credenciais se as credenciais de logon do usuário que executa o assistente não forem suficientes para conectar-se|Administrador local no computador de destino.|Instalação e configuração da função de servidor WAP.
Credenciais de confiança de proxy |As credenciais de confiança do serviço de federação (as credenciais que o proxy usará para se registrar para um certificado de confiança do FS |Conta de domínio que é administrador local do servidor do AD FS|Registro inicial do certificado de confiança FS WAP
Página da conta de serviço do AD FS, "Usar uma opção de conta de usuário de domínio"|Credenciais de conta de usuário do AD|Usuário de domínio|A conta de usuário do AD cujas credenciais fore fornecidas será usada como a conta de logon do serviço do AD FS.



<br> <br>
## Permissões necessárias para o cenário específico

Cenário |Permissão
------------- | ------------- |
Sincronização de senha| <li>Replicar alterações de diretório.</li> <li>Replicar todas as alterações de diretório.</li>
Implantação híbrida do Exchange|Consulte [Permissões e atributos de write-back de sincronização do Office 365 Exchange Hybrid AAD](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Write-back de senha | <li>Alterar senha</li><li>Redefinir senha</li>
Write-back de usuário, grupo e dispositivo|Permissões de gravação para os objetos de diretório e os atributos nos quais você deseja fazer "write-back"
Logon único e AD FS| Permissões de administrador de domínio no domínio em que os servidores federados estão localizados.
Permissões do SQL Server|A conta de serviço Azure AD Connect Sync exige permissões dbo no SQL. Essas permissões são definidas durante a instalação do Azure AD Connect e não há suporte às alterações.

<br> <br>
## Resumo das contas que são criadas pelo Azure AD Connect



Conta criada |Permissões atribuídas | Usado para
------------- | ------------- |------------- |
Conta do AD do Azure para sincronização|Função de sincronização de diretório dedicada|Operações de sincronização em andamento (conta de MA do AD do Azure)
Configurações expressas: conta do AD usada para sincronização|Permissões de leitura/gravação no diretório conforme necessário para sincronização e sincronização de senha|Operações de sincronização em andamento (conta de MA do AD do Azure)
Configurações expressas: conta de logon do serviço de sincronização | Credenciais de logon do usuário que executa o assistente|Conta de logon do serviço de sincronização
Configurações personalizadas: conta de logon do serviço de sincronização |ND|Conta de logon do serviço de sincronização
AD FS: conta do GMSA (aadcsvc$)|Usuário de domínio|Conta de logon do serviço FS


**Recursos adicionais**



* [Permissões para sincronização de senha](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Permissões para Exchange híbrido](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Permissões para write-back de senha](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect no MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->