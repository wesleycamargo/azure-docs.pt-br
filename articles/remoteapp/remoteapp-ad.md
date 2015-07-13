
<properties 
    pageTitle="Configurar o Active Directory para o Azure RemoteApp" 
    description="Saiba como configurar o Active Directory para trabalhar com o Azure RemoteApp." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="elizapo" />



# Configurar o Active Directory para o Azure RemoteApp


Para uma coleção híbrida do RemoteApp, você precisa configurar uma infraestrutura de domínio do Active Directory local e o Azure Active Directory locatário com a Integração de diretório (e opcionalmente logon único). Além disso, você precisa criar alguns objetos do Active Directory no diretório local. Use as seguintes informações para configurar o Active Directory local e o AD do Azure e integre os dois.

## Configurar o Active Directory no local
Iniciar a configuração de seu Active Directory local. Você precisa identificar o sufixo de domínio para usar e, em seguida, criar objetos do Active Directory para o RemoteApp.

Não adicionou serviços de domínio Active Directory para seu ambiente do Windows Server 2012 R2 ainda? Verifique [estas instruções](https://technet.microsoft.com/library/cc731053.aspx) para obter informações sobre como fazer exatamente isso.
### Examinar e definir o sufixo de domínio UPN
Se a floresta não está configurada com um sufixo UPN que corresponda ao domínio do AD do Azure que você usará para o RemoteApp, você precisa adicioná-lo. Determine se o sufixo necessário está configurado:


1. Inicie os computadores e usuários do Active Directory.
2.	Expanda o nome do domínio e, em seguida, clique em **Usuários**.
3.	Clique com o botão direito do mouse em **Administrador** e, em seguida, clique em **Propriedades**.
4.	Na guia **Conta**, examine os nomes UPN configurados para este domínio no campo **Nome de logon do usuário**.
5.	Se você não vir um sufixo que corresponda ao nome de domínio que você deseja usar para sua coleção de RemoteApp, faça o seguinte:
	1.	Inicie as Relações de confiança e Domínios do Active Directory.
	2.	Clique com o botão direito do mouse em **Domínios e relações de confiança do Active Directory** e, em seguida, clique em **Propriedades**.
	3.	Examine a lista de sufixos.
	4.	Digite o FQDN do nome do domínio na caixa e clique em **Adicionar** e, em seguida, **OK**. Exemplo: contoso.com. 

		NÃO inclua o "@" no sufixo aqui.

De agora em diante, quando você criar novos usuários, você pode selecionar o novo sufixo de Nome de logon do usuário. Além disso, você pode alterar o sufixo para usuários existentes na guia Conta nas propriedades dos usuários.

Para obter mais informações, consulte [Adicionar sufixos de nome UPN](http://technet.microsoft.com/library/cc772007.aspx).

### Criar objetos para RemoteApp no Active Directory
O RemoteApp precisa de dois objetos no Active Directory local:


- Uma conta de serviço para fornecer acesso aos recursos do domínio para programas RemoteApp unindo os pontos de extremidade RDSH ao domínio local.
- Uma OU (unidade organizacional) para conter objetos de computador do RemoteApp. Uso da OU é recomendável (mas não obrigatório) para isolar as contas e as políticas que você usará com o RemoteApp.

Use as informações a seguir para criar cada um desses objetos.

#### Crie a conta de serviço:


1. Inicie os computadores e usuários do Active Directory.
2.	Clique com o botão direito do mouse em **Usuário** e, em seguida, clique em **Novo > Usuário**.
3.	Insira um nome de usuário e senha para a conta de serviço do RemoteApp.

	**Nota:** você precisará dessas informações sobre a conta ao criar sua coleção de RemoteApp.

#### Criar uma nova unidade organizacional (OU)


1. Em Usuários e computadores do Active Directory, clique no seu domínio. Clique em **Nova > Unidade organizacional**.
2. Adicione a conta de serviço que você criou para o RemoteApp para essa nova OU.

	Para fazer isso, localize a conta de serviço que você criou. Clique com o botão direito do mouse sobre ela e, em seguida, clique em **Mover**. Selecione a nova OU e, em seguida, clique em **OK**.


1. Conceda à conta de serviço do RemoteApp a autoridade para adicionar e excluir computadores a este OU:
	1. No snap-in dos Usuários e Computadores do Active Directory, clique em **Exibir > Recursos avançados**. Isso adiciona a guia **Segurança** para as Informações de propriedades.
	2. Clique com o botão direito do mouse no serviço OU do RemoteApp e, em seguida, clique em **Propriedades**.
	3. Na guia **Segurança**, clique em **Adicionar**. Selecione o objeto de usuário de conta de serviço do RemoteApp e, em seguida, clique em **OK**.
	4. Clique em **Avançado**.
	5. Na guia **Permissões**, selecione a conta de serviço do RemoteApp e, em seguida, clique em **Editar**.
	6. Selecione **Esse objeto e todos os objetos descendentes** no campo **Aplicar a**.
	7. No campo **Permissões**, selecione **Permitir** próximo a Criar objetos de computador e Excluir objetos de computador e clique em **OK**. 
	8. Clique em **OK** nas duas janelas restantes.


## Configurar o Active Directory do Azure
Agora que o Active Directory local está configurado, mova para o AD do Azure. Você precisa criar um domínio personalizado que coincide com o sufixo de domínio para seu domínio local e configurar a integração de diretório. A coleção híbrida suporta somente as contas do Active Directory do Azure que foram sincronizadas (usando uma ferramenta como DirSync) de uma implantação do Active Directory do Windows Server. Especificamente, ou sincronizado com a opção de sincronização de senha ou sincronizado com federação de serviços de Federação do Active Directory (AD FS) configurada.

Use as informações a seguir para configurar o Active Directory do Azure


- [Adicionar seu domínio](http://technet.microsoft.com/library/hh969247.aspx) – use essas informações para adicionar um domínio que coincide com o sufixo de domínio UPN do seu domínio do Active Directory local.
- [Integração de diretórios](http://technet.microsoft.com/library/jj573653.aspx) – use essas informações para escolher uma opção de integração de diretório – [DirSync com sincronização de senha](http://technet.microsoft.com/library/dn441214.aspx) ou [DirSync com federação](http://technet.microsoft.com/library/dn441213.aspx).

Você também pode configurar a [Autenticação multifator (MFA)](http://technet.microsoft.com/library/dn249466.aspx).

## Problemas ao configurar a sincronização de diretórios?

Se você estiver tendo problemas ao configurar a sincronização de diretórios, verifique o seguinte:

- Você está usando a versão mais recente da ferramenta de sincronização de Diretório do Azure 
-	No portal de gerenciamento, em **Active Directory->Diretório Padrão->Domínios**, você já adicionou seu domínio personalizado (como mydomain.com) e o fez primário.
-	Em **Active Directory->Diretório Padrão-> Usuários**, adicione um novo usuário ao domínio (por exemplo, myAzureSyncUser@mydomain.com).
-	Em seu domínio no Active Directory, você adicionou um novo usuário de domínio e o tornou um membro do grupo Administradores de Empresa (por exemplo, myDomainSyncUser@mydomain.com).

Agora inicie a ferramenta de sincronização de Diretórios do Azure e use as credenciais **myAzureSyncUser@mydomain.com** para o primeiro prompt (credenciais de administrador do Active Directory do Microsoft Azure) e use **myDomainSyncUser@mydomain.com** para o segundo prompt.
 

<!---HONumber=62-->