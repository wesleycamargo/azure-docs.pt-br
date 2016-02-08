<properties
   pageTitle="Conectar ao Banco de Dados SQL Usando a Autenticação do Active Directory do Azure | Microsoft Azure"
   description="Saiba como se conectar ao Banco de Dados SQL Usando a Autenticação do Active Directory do Azure."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="jeffreyg"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="12/09/2015"
   ms.author="rick.byham@microsoft.com"/>

# Conectar-se ao Banco de Dados SQL Usando a Autenticação do Active Directory do Azure

A autenticação do Active Directory do Azure é um mecanismo de conexão com o Banco de Dados SQL do Microsoft Azure usando identidades no Active Directory do Azure (AD do Azure). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em um único local central. O gerenciamento central de ID fornece um único local para gerenciar usuários de Banco de Dados SQL e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
- Permite o rodízio de senhas em um único lugar
- Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- A autenticação do Active Directory do Azure usa usuários de banco de dados independente para autenticar identidades no nível do banco de dados.

> [AZURE.IMPORTANT] A autenticação do Active Directory do Azure é um recurso de visualização e está sujeito aos termos de visualização no contrato de licença (por exemplo, o contrato corporativo, Contrato do Microsoft Azure ou Contrato de Assinatura do Microsoft Online), bem como quaisquer [Termos Complementares de Uso para Visualização do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicáveis.

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular um Active Directory do Azure
2. Certificar-se que o banco de dados está em Banco de Dados SQL V12 do Azure
3. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
4. Criar um administrador do Active Directory do Azure para o Azure SQL Server
5. Configurar os computadores cliente
6. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
7. Conectar-se ao banco de dados usando identidades do AD do Azure


## Confiar na arquitetura

O diagrama de alto nível a seguir resume a arquitetura da solução de usar a autenticação do AD do Azure com o Banco de Dados SQL do Azure. As setas indicam caminhos para comunicação.

![diagrama de autenticação do aad][1]

O diagrama a seguir indica as relações de federação, confiança e hospedagem que permitem que um cliente se conecte a um banco de dados enviando um token que foi autenticado por um AD do Azure e que é confiável para o banco de dados. É importante entender que o acesso a um banco de dados usando a autenticação do AD do Azure requer que a assinatura de hospedagem esteja associada ao Active Directory do Azure.

![relação de assinatura][2]

## Estrutura do administrador

Ao usar a autenticação do AD do Azure, haverá duas contas de administrador para o servidor de Banco de Dados SQL: o administrador original do SQL Server e o administrador do AD do Azure. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo múltiplos administradores do AD do Azure para a instância do SQL Server. Usar a conta de grupo como um administrador aprimora a capacidade de gerenciamento, permitindo que você adicione e remova membros do grupo no AD do Azure centralmente, sem alterar os usuários ou permissões no Banco de Dados SQL. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][3]

## Permissões

Para criar novos usuários, você deve ter a permissão **ALTER ANY USER** no banco de dados. A permissão **ALTER ANY USER** pode ser concedida a qualquer usuário do banco de dados. A permissão **ALTER ANY USER** também é mantida pelas contas de administrador do servidor e usuários de banco de dados com a permissão **CONTROL ON DATABASE** ou **ALTER ON DATABASE** para esse banco de dados e por membros da função de banco de dados **db\_owner**.

Para criar um usuário do banco de dados contido no Banco de Dados SQL do Azure, você deve conectar-se ao banco de dados usando uma identidade do AD do Azure. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando o administrador do AD do Azure (que é o proprietário do banco de dados). Isso é demonstrado nas etapas 4 e 5 abaixo.

## Limitações e recursos do AD do Azure

Os seguintes membros do Active Directory do Azure podem ser provisionados no SQL Server do Azure: - Membros nativos: um membro criado no AD do Azure no domínio gerenciado ou em um domínio do cliente. Para obter mais informações, consulte [Adicionar seu próprio nome de domínio ao Azure AD](active-directory-add-domain.md). - Membros do domínio federado: um membro criado no AD do Azure com um domínio federado. Para obter mais informações, veja [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/). - Membros importados de outros Active Directories do Azure que são membros de domínio nativo ou federado. - Grupos do Active Directory criados como grupos de segurança.

Não há suporte para contas da Microsoft (por exemplo, outlook.com, hotmail.com, live.com) nem para outras contas de convidado (por exemplo, gmail.com, yahoo.com). Se você pode fazer logon em [https://login.live.com](https://login.live.com) usando a conta e senha, isso significa que você está usando uma conta da Microsoft para a qual que não há suporte para autenticação do AD do Azure para o Banco de Dados SQL do Azure.

### Considerações adicionais

- Para aumentar a capacidade de gerenciamento, é recomendável que você provisione um grupo dedicado do Active Directory do Azure como administrador.
- Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento, para um Azure SQL Server.
- Somente um administrador do Active Directory do Azure pode conectar-se inicialmente ao SQL Server do Azure usando uma conta do Active Directory do Azure. O administrador do Active Directory pode configurar usuários subsequentes de banco de dados do Active Directory do Azure.
- É recomendável configurar o tempo limite da conexão para 30 segundos.
- Não há suporte para algumas ferramentas como o Excel e o BI.
- A autenticação do Active Directory do Azure dá suporte apenas ao **Provedor de Dados do .NET Framework para SQL Server** (é necessário o .NET Framework 4.6 como versão mínima). Portanto, o Management Studio (disponível com o SQL Server 2016) e aplicativos de camada de dados (DAC e .bacpac) podem se conectar, mas **sqlcmd.exe** não pode se conectar porque **sqlcmd** usa o provedor ODBC.
- Não há suporte para a autenticação de dois fatores ou outras formas de autenticação interativa.


## 1\. Criar e popular um Active Directory do Azure

Crie um Active Directory do Azure e popule-o com usuários e grupos. Isso inclui:

- Crie o domínio inicial de domínio gerenciado do AD do Azure.
- Realize a federação de uma instância local dos Serviços de Domínio do Active Directory com o Active Directory do Azure.

Para obter mais informações, veja [Adicionar seu próprio nome de domínio ao AD do Azure](active-directory-add-domain.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do AD do Azure](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Gerenciar o AD do Azure usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## 2\. Certificar-se que o banco de dados está em Banco de Dados SQL V12 do Azure

O mais recente Banco de Dados SQL V12 do Azure dá suporte à autenticação do Active Directory do Azure. Para obter informações sobre Banco de Dados SQL V12 e saber se ele está disponível em sua região, consulte [O que há de novo na Última Atualização do Banco de Dados SQL V12](sql-database-v12-whats-new.md).

Se você tem um banco de dados existente, verifique se ele está hospedado no Banco de Dados SQL V12 conectando-se ao banco de dados (por exemplo, usando o SQL Server Management Studio) e executando o `SELECT @@VERSION;`. A saída esperada para um Banco de Dados SQL V12 é pelo menos **Microsoft SQL Azure (RTM) - 12.0**.

Se o seu banco de dados não estiver hospedado no Banco de Dados SQL V12, consulte [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md) e, em seguida, visite o Portal Clássico do Azure para migrar o banco de dados para Banco de Dados SQL V12.

Como alternativa, você pode criar um novo banco de dados em Banco de Dados SQL V12, seguindo as etapas listadas em [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md). **Dica**: leia a próxima etapa antes de selecionar uma assinatura para o novo banco de dados.

## 3\. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure

Para associar seu banco de dados ao diretório do AD do Azure para sua organização, torne o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para saber mais sobre as assinaturas do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informações adicionais:** cada assinatura do Azure tem uma relação de confiança com uma instância do AD do Azure. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Você pode ver qual diretório é confiável para sua assinatura na guia **Configurações** em [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Os procedimentos a seguir fornecem instruções passo a passo sobre como alterar o diretório associado para uma determinada assinatura.

1. Conecte-se ao seu [portal clássico do Azure](https://manage.windowsazure.com/) usando um administrador de assinatura do Azure.
2. Na faixa esquerda, selecione **CONFIGURAÇÕES**.
3. Suas assinaturas aparecem na tela de configurações. Se a assinatura desejada não aparecer, clique em **Assinaturas** na parte superior, exiba caixa da lista suspensa **FILTRAR POR DIRETÓRIO**, selecione o diretório que contém suas assinaturas e, por fim, clique em **APLICAR**.

	![selecione a assinatura][4]
4. Na área **configurações**, clique em sua assinatura e, em seguida, clique em **EDITAR DIRETÓRIO** na parte inferior da página.

	![ad-settings-portal][5]
5. Na caixa **EDITAR DIRETÓRIO**, selecione o Active Directory do Azure que está associado ao SQL Server e, em seguida, clique na seta para avançar.

	![edit-directory-select][6]
6. Na caixa de diálogo de Mapeamento de diretório **CONFIRMAR**, confirme que "**Todos os coadministradores serão removidos**".

	![edit-directory-confirm][7]
7. Clique na marca de verificação para recarregar o portal.

> [AZURE.NOTE] Quando você muda o diretório, o acesso a todos os coadministradores, usuários e grupos do AD do Azure e usuários de recursos reserva recursos com backup em diretório serão removidos e eles não terão mais acesso a essa assinatura nem a seus recursos. Apenas você, como um administrador de serviços, poderá configurar o acesso para entidades de segurança com base no novo diretório. Essa alteração pode levar bastante tempo para se propagar para todos os recursos. Alterar o diretório também vai alterar o administrador do AD do Azure para o Banco de Dados SQL e removerá a permissão de acesso a Banco de Dados SQL para usuários existentes do AD do Azure. O administrador do AD do Azure deve ser definido novamente (conforme descrito abaixo) e novos usuários do Azure devem ser criados.

## 4\. Criar um administrador do Active Directory do Azure para o Azure SQL Server

Cada servidor do Azure SQL Server começa com uma única conta de administrador do servidor, que é o administrador de todo o Azure SQL Server. Um segundo administrador do servidor deve ser criado, que é uma conta do AD do Azure. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db\_owner** em todos os usuários do banco de dados e inserem cada banco de dados de usuário como o usuário **dbo**. Para obter mais informações sobre as contas de administrador do servidor, consulte [Gerenciamento de Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md) e a seção **Logons e Usuários** de [Limitações e Diretrizes de Segurança de Banco de Dados SQL do Azure](sql-database-security-guidelines.md).

> [AZURE.NOTE] Usuários que não são baseados em uma conta do AD do Azure (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no AD do Azure porque eles não têm permissão para validar os usuários do banco de dados proposto com o AD do Azure.

### Provisionar um administrador do Active Directory do Azure para seu Azure SQL Server usando o Portal clássico do Azure

1. No [Portal clássico do Azure](https://portal.azure.com/), no canto superior direito, clique na conexão para exibir uma lista suspensa com os Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula a associação de assinatura no Active Directory ao Banco de Dados SQL do Azure, certificando-se que a mesma assinatura é usada tanto para o AD do Azure quanto para o SQL Server.

	![choose-ad][8]
2. Na faixa à esquerda, selecione **SQL servers**, selecione seu **SQL server** e, em seguida, na folha **SQL Server**, na parte superior, clique em **Configurações**.

	![configurações de ad.][9]
3. Na folha **Configurações**, clique em **Administrador do Active Directory (visualização)** e aceite a cláusula de visualização.
4. Na folha **Administrador do Active Directory (visualização)**, clique para examinar e em **OK** para aceitar os termos de visualização.
5. Na folha **Administrador do Active Directory (visualização)**, clique em **Administrador do Active Directory** e, na parte superior, clique em **Definir administrador**.
6. Na folha **Adicionar administrador**, procure um usuário, selecione o usuário ou grupo que será um administrador e, em seguida, clique em **Selecionar**. (A folha Administrador do Active Directory mostrará todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Consulte a lista de administradores com suporte em **Limitações e recursos do AD do Azure** acima.) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para o SQL Server.
7. Na parte superior da folha **Administrador do Active Directory**, clique em **SALVAR**.![escolha o administrador][10]

	O processo de alteração do administrador pode levar vários minutos. O novo administrador será exibido na caixa **Administrador do Active Directory**.

> [AZURE.NOTE] Ao configurar o administrador do AD do Azure, o novo nome de administrador (usuário ou grupo) não pode já estar presentes no banco de dados mestre como um logon de autenticação do SQL Server. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse logon de autenticação do SQL Server não é parte do AD do Azure, qualquer esforço para se conectar ao servidor usando a autenticação do AD do Azure falhará.

Para remover o administrador mais tarde, na parte superior da folha **Administrador do Active Directory**, clique em **Remover administrador**.

### Provisionar um administrador do AD do Azure para o Azure SQL Server usando o PowerShell



Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Para provisionar um administrador do AD do Azure, você deve executar os seguintes comandos do Azure PowerShell:

- Add-AzureRmAccount
- Select-AzureRmSubscription


Cmdlets usados para provisionar e gerenciar o administrador do AD do Azure:

| Nome do cmdlet | Descrição |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Configura um administrador do Active Directory do Azure para o Azure SQL Server. (Precisa ser da assinatura atual.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Remove um administrador do Active Directory do Azure para o Azure SQL Server. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | Retorna informações sobre um administrador do Active Directory do Azure atualmente configurado para o Azure SQL Server. |

Use o comando get-help do PowerShell para ver mais detalhes sobre cada um desses comandos, por exemplo, ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

O seguinte script provisiona um grupo de administradores do Azure AD chamado **DBA\_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo\_server** em um grupo de recursos chamado **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [AZURE.NOTE] O comando ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários para os quais não há suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não poderá se conectar a um banco de dados. (Consulte a lista de administradores com suporte em **Limitações e recursos do AD do Azure** acima.)

O seguinte exemplo usa a **ObjectID** opcional:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do AD do Azure para o Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do Azure AD: ```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5\. Configurar os computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure usando identidades do AD do Azure, você deve instalar o software a seguir:

- .NET Framework 4.6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- A Biblioteca de Autenticação do Active Directory do Azure para SQL Server (**ADALSQL.DLL**) está disponível em vários idiomas (tanto x86 quanto amd64) no centro de download da [Biblioteca de Autenticação do Microsoft Active Directory para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### Ferramentas

- Instalar um [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende ao requisito do .NET Framework 4.6.
- O SSMS instala a versão x86 do **ADALSQL.DLL**. (No momento, o SSMS não solicitará uma reinicialização necessária após a instalação. Isso deve ser corrigido em um futuro CTP.)
- O SSDT instala a versão amd64 do **ADALSQL.DLL**. O SSDT oferece suporte apenas parcial à autenticação do AD do Azure.
- O Visual Studio mais recente dos [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito do .NET Framework 4.6, mas não instala a versão amd64 necessária do **ADALSQL.DLL**.

## 6\. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

### Sobre usuários de bancos de dados independentes

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do AD do Azure é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx). Os usuários do banco de dados (com a expectativa dos administradores) não podem ser criados usando o portal, e as funções RBAC não são propagadas para o SQL Server.

### Conectar-se ao banco de dados de usuário usando o SQL Server Management Studio

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD. Para provisionar um usuário de banco de dados independente com base no AD do Azure (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do AD do Azure que tenha acesso ao banco de dados.

> [AZURE.IMPORTANT] O suporte para a autenticação do Active Directory do Azure está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

#### Conectar-se usando a autenticação integrada do Active Directory

Use este método se você efetuou logon no Windows usando suas credenciais do Active Directory do Azure por meio de um domínio federado.

1. Inicie o Management Studio e, na caixa de diálogo **Conectar ao Mecanismo de Banco de Dados** (ou **Conectar ao Servidor**), na caixa **Autenticação**, selecione **Autenticação Integrada do Active Directory**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.
2. Clique no botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar.

#### Conectar-se usando a autenticação de senha do Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para conta federada sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use este método se você estiver conectado no Windows usando as credenciais de um domínio que não está federado com o Azure, ou quando usar a autenticação do AD do Azure usando o Azure AD baseado no domínio de cliente ou inicial.

1. Inicie o Management Studio e, na caixa de diálogo **Conectar ao Mecanismo de Banco de Dados** (ou **Conectar ao Servidor**), na caixa **Autenticação**, selecione **Autenticação de Senha do Active Directory**.
2. Na caixa **Nome de usuário**, digite seu nome de usuário do Active Directory do Azure no formato ****username@domain.com**. Essa deve ser uma conta do Active Directory do Azure ou uma conta de um domínio federado com o Active Directory do Azure.
3. Na caixa **Senha**, digite sua senha de usuário para a conta do Active Directory do Azure ou conta de domínio federado.
4. Clique no botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual deseja se conectar.


### Criar um usuário de banco de dados independente do AD do Azure em um banco de dados do usuário

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD (conforme descrito no procedimento anterior) como um usuário com, no mínimo, a permissão **ALTER ANY USER**. Em seguida, use a sintaxe Transact-SQL a seguir:

	CREATE USER Azure_AD_principal_name
	FROM EXTERNAL PROVIDER;


*Azure\_AD\_principal\_name* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para criar um usuário de banco de dados independente representando um grupo de domínio federado ou do AD do Azure:

	CREATE USER [Nurses] FROM EXTERNAL PROVIDER;


Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Windows dos quais esse usuário faz parte. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md). Um usuário de domínio federado que é importado para um domínio gerenciado deve usar a identidade do domínio gerenciado.

> [AZURE.NOTE] Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL\_USER) e para grupos com o tipo X (EXTERNAL\_GROUPS). Para saber mais, confira [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## 7\. Conectar-se ao banco de dados usando identidades do Active Directory do Azure

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

- Usando a autenticação integrada do Windows
- Com um nome principal e uma senha do AD do Azure

### 7\.1. Conectar-se usando a autenticação integrada (Windows)

Para usar a autenticação integrada do Windows, o Active Directory do seu domínio deve ser federado com o Active Directory do Azure e o seu aplicativo cliente (ou um serviço) se conectando ao banco de dados deve ser executado em um computador associado a um domínio sob as credenciais de domínio de um usuário.

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do AD do Azure, a palavra-chave de Autenticação na cadeia de conexão de banco de dados deve ser definida como Integrada ao Active Directory. O exemplo de código em C# a seguir usa ADO .NET.

	string ConnectionString =
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Observe que, para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão ``Integrated Security=True``.

### 7\.2. Conectar com um nome principal e uma senha do AD do Azure
Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do AD do Azure, a palavra-chave de Autenticação deve ser definida como a Senha do Active Directory e a cadeia de conexão deve conter as palavras-chave e valores ID/UID e Password/PWD. O exemplo de código em C# a seguir usa ADO .NET.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Para exemplos de código específicos relacionados à autenticação do Azure AD, confira o [Blog sobre segurança do SQL Server](http://blogs.msdn.com/b/sqlsecurity/) no MSDN.

## Consulte também

[Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)

[Usuários de Banco de Dados Independente](https://msdn.microsoft.com/library/ff929071.aspx)

[CRIAR USUÁRIO (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png

<!---HONumber=AcomDC_0128_2016-->