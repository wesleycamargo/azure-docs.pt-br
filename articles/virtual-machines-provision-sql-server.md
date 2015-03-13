<properties 
	pageTitle="Provisionar uma máquina virtual do SQL Server no Azure" 
	description="Um tutorial que ensina como criar e configurar uma máquina virtual do SQL Server no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2014" 
	ms.author="selcint"/>





# Provisionando uma máquina virtual do SQL Server no Azure #

A galeria de Máquinas Virtuais do Azure inclui várias imagens que contêm o Microsoft SQL Server. Você pode selecionar uma das imagens de máquina virtual da galeria e, com apenas alguns cliques, provisioná-la para o ambiente do Azure.

Neste tutorial, você vai:

* [Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria](#Provision)
* [Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação](#RemoteDesktop)
* [Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador](#SSMS)
* [Próximas etapas](#Optional)

##<a id="Provision">Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria</a>

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) (a página pode estar em inglês) usando sua conta. Se você não tiver uma conta do Azure, visite [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

2. No Portal de Gerenciamento do Azure, na parte inferior esquerda da página da Web, clique em **+NOVO**, **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e, em seguida, em **DA GALERIA**.

3. Na página **Criar uma Máquina Virtual**, selecione uma imagem de máquina virtual que contenha o SQL Server e clique na seta de avanço na parte inferior à direita da página. Para obter as informações mais atualizadas sobre as imagens do SQL Server com suporte no Azure, consulte o tópico [Introdução ao SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) (a página pode estar em inglês) no conjunto de documentação [SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719). 

    >[AZURE.NOTE] Se você tiver uma máquina virtual criada usando a imagem da plataforma da edição de avaliação do SQL Server, não será possível atualizá-la para uma imagem de edição paga por minuto na galeria. Você pode escolher uma das duas seguintes opções:
    
    > - Você pode criar uma nova máquina virtual usando a edição do SQL Server paga por minuto da galeria e migrar seus arquivos de banco de dados para essa nova máquina virtual, seguindo as etapas em [Como migrar os arquivos e o esquema de banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados](http://go.microsoft.com/fwlink/p/?LinkId=294738). **Ou**,

    > - Atualizar uma instância existente da Edição de Avaliação do SQL Server para outra edição do SQL Server sob o contrato [License Mobility por meio do Software Assurance no Azure](http://azure.microsoft.com/pricing/license-mobility/) seguindo as etapas em [Atualizar para outra edição do SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Para obter informações sobre como adquirir a cópia licenciada do SQL Server, consulte [Como comprar o SQL Server (a página pode estar em inglês)](http://www.microsoft.com/pt-br/sqlserver/get-sql-server/how-to-buy.aspx).
   

4. Na primeira página **Configuração de Máquina Virtual**, forneça as seguintes informações:
	- Forneça um **NOME DE MÁQUINA VIRTUAL**.
	- Na caixa **NOVO NOME DE USUÁRIO**, digite um nome de usuário exclusivo para a conta de administrador local da VM.
	- Na caixa **NOVA SENHA**, digite uma senha forte. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)](http://msdn.microsoft.com/library/ms161962.aspx).
	- Na caixa **CONFIRMAR SENHA**, digite a senha novamente.
	- Selecione o **Tamanho** apropriado na lista suspensa. 

	>[AZURE.NOTE] O tamanho da máquina virtual é especificado durante o provisionamento:
 	> Um2 é o tamanho menor recomendado para cargas de trabalho de produção. 
    > O tamanho mínimo recomendado para uma máquina virtual é A3 ao usar o SQL Server Enterprise Edition.
    > Selecione A3 ou maior ao usar o SQL Server Enterprise Edition.
   	> Selecione A4 ao usar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens de cargas de trabalho transacionais.  
   	> Selecione A7 ao usar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens de cargas de trabalho do Data Warehouse. 
   	> O tamanho selecionado limita o número de discos de dados que você pode configurar. Para obter informações mais atualizadas sobre tamanhos de máquina virtual disponíveis e o número de discos de dados que você pode anexar a uma máquina virtual, consulte [Tamanhos de Máquina Virtual para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

	Clique na seta de avanço na parte inferior à direita para continuar.

	![VM Configuration](./media/virtual-machines-provision-sql-server/4VM-Config.png)


5. Na segunda página **Configuração da máquina virtual**, configure recursos para rede, armazenamento e disponibilidade:
	- Na caixa **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**.
	- Na caixa **Nome DNS do Serviço de Nuvem**, forneça a primeira parte de um nome DNS de sua escolha, para que ele complete um nome no formato **TESTNAME.cloudapp.net** 
	- Na caixa **REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL**, selecione uma região em que esta imagem virtual será hospedada.
	- Na **Conta de Armazenamento**, selecione uma conta de armazenamento existente ou selecione uma gerada automaticamente.
	- Na caixa **CONJUNTO DE DISPONIBILIDADE**, selecione **(nenhum)**.
	- Leia e aceite o termos legais.
	

6. Clique na seta de avanço para continuar.


7. Clique na marca de seleção no canto inferior à direita para continuar.

8. Aguarde enquanto o Azure prepara sua máquina virtual. Espere que o status de máquina virtual prossiga por:

	- Iniciando (Provisionamento)
	- Parada
	- Iniciando (Provisionamento)
	- Executando (Provisionamento)
	- Executando
	

##<a id="RemoteDesktop">Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação</a>

1. Quando o provisionamento for concluído, clique no nome da máquina virtual para ir para a página PAINEL. Na parte inferior da página, clique em **Conectar**.

	
2. Escolha abrir o arquivo .rpd usando o programa Área de Trabalho Remota do Windows (`%windir%\system32\mstsc.exe`).

	
3. Na caixa de diálogo **Segurança do Windows**, forneça a senha da conta do administrador local que você especificou em uma etapa anterior. (Pode ser solicitado que você verifique as credenciais da máquina virtual.)

4. Na primeira vez que você fizer logon nessa máquina virtual, talvez seja necessário concluir vários processos, incluindo a configuração da sua área de trabalho, atualizações do Windows e a conclusão das tarefas de configuração inicial do Windows (sysprep). Depois que o sysprep do Windows é concluído, a configuração do SQL Server conclui as tarefas de configuração. Essas tarefas podem causar um atraso de alguns minutos enquanto são concluídas. `SELECT @@SERVERNAME` podem não retornar o nome correto até a conclusão da instalação do SQL Server e o SQL Server Management Studio pode não ser visível na página inicial.

Quando você está conectado à máquina virtual com a Área de Trabalho Remota do Windows, a máquina virtual funciona de maneira muito semelhante à qualquer outro computador. Conecte-se à instância padrão do SQL Server com o SQL Server Management Studio (em execução na máquina virtual) da maneira normal. 

##<a id="SSMS">Concluir as etapas de Configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador</a>

Para poder conectar-se à instância do SQL Server na Internet, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

- [Criar um ponto de extremidade TCP para a máquina virtual](#Endpoint)
- [Abrir portas TCP no firewall do Windows](#FW)
- [Configurar o SQL Server para escutar no protocolo TCP](#TCP)
- [Configurar o SQL Server para autenticação de modo misto](#Mixed)
- [Criar logons de autenticação do SQL Server](#Logins)
- [Determinar o nome DNS da máquina virtual](#DNS)
- [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#cde)
- [Conectando-se ao Mecanismo de Banco de Dados em seu aplicativo](#cdea)

O caminho de conexão é resumido pelo diagrama a seguir:

![Connecting to a SQL Server virtual machine][Image8b]

###<a id="Endpoint">Criar um ponto de extremidade TCP para a máquina virtual</a>

A máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

1. No Portal de Gerenciamento do Azure, clique em **MAQUINAS VIRTUAIS**.

	
2. Clique na máquina virtual recém-criada. As informações sobre sua máquina virtual são apresentadas.
	

3. Próximo à parte superior da página, selecione a página **PONTOS DE EXTREMIDADE** e, na parte inferior da página, clique em **ADICIONAR**.
	

4. Na página **Adicionar Ponto de Extremidade à Máquina Virtual**, clique em **Adicionar Ponto de Extremidade Independente** e, em seguida, clique na seta de avanço para continuar.

	
5. Na página **Especificar os detalhes do ponto de extremidade**, forneça as seguintes informações.

	- Na caixa **NOME**, forneça um nome para o ponto de extremidade.
	- Na caixa **PROTOCOLO**, selecione **TCP**. Você pode digitar **57500** na caixa **PORTA PÚBLICA**. Da mesma forma, você pode digitar a porta de escuta padrão do SQL Server **1433** na caixa **Porta Particular**. Observe que muitas organizações selecionam números de porta diferentes para evitar ataques de segurança mal-intencionados. 


6. Clique na marca de seleção para continuar. O ponto de extremidade é criado.
	

###<a id="FW">Abrir portas TCP no firewall do Windows para a instância padrão do Mecanismo de Banco de Dados</a>

1. Conecte-se à máquina virtual por meio da Área de Trabalho Remota do Windows. Quando estiver conectado, no menu Iniciar, clique em **Executar**, digite **WF.msc**e, em seguida, clique em **OK**. Se estiver executando o Windows Server 2012 ou posterior, você também pode digitar **WF** no menu Iniciar e selecionar **Firewall do Windows com Segurança Avançada**.

	![Start the Firewall Program][Image12]
2. No **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do mouse em **Regras de Entrada**e, em seguida, clique em **Nova Regra** no painel de ações.

	![Nova regra][Image13]

3. Na caixa de diálogo **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

4. Na caixa de diálogo **Protocolo e Portas**, selecione **TCP**. Selecione **Portas locais específicas** e, em seguida, digite o número da porta da instância do Mecanismo de Banco de Dados (**1433** para a instância padrão ou sua opção para a porta privada na etapa de ponto de extremidade). 

	![TCP Port 1433][Image14]

5. Clique em **Próximo**.

6. Na caixa de diálogo **Ação**, selecione **Permitir a conexão**, e clique em **Avançar**.

	**Observação de segurança:**Ao selecionar **Permitir a conexão se for segura** pode fornecer segurança adicional. Selecione esta opção se desejar configurar opções de segurança adicionais em seu ambiente.

	![Allow Connections][Image15]

7. Na caixa de diálogo **Perfil**, selecione **Público** e clique em **Avançar**. 

    **Observação de Segurança:**  A seleção de **Público** permite acesso pela Internet. Sempre que possível, selecione um perfil mais restritivo.

	![Public Profile][Image16]

8. Na caixa de diálogo **Nome**, digite um nome e uma descrição para essa regra, e clique em **Concluir**.

	![Rule Name][Image17]

Abrir portas adicionais para outros componentes conforme necessário. Para obter mais informações, consulte [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)](http://msdn.microsoft.com/library/cc646023.aspx).


###<a id="TCP">Configurar o SQL Server para escutar no protocolo TCP</a>

1. Enquanto estiver conectado à máquina virtual usando a Área de Trabalho Remota, no menu Iniciar, clique em **Todos os Programas**, em **Microsoft SQL Server** *version*, clique em **Ferramentas de Configuração** e, em seguida, clique em **SQL Server Configuration Manager**.
	
	![Open SSCM][Image9]

2. No SQL Server Configuration Manager, no painel do console, expanda **Configuração de Rede do SQL Server**.

3. No painel do console, clique em **Protocolos para _instance name_**. (A instância padrão é **Protocolos para MSSQLSERVER**.)

4. No painel de detalhes, clique com o botão direito do mouse em TCP, por padrão, ele deve estar habilitado para as imagens da Galeria. Para suas imagens personalizadas, clique em **Habilitar** (se o status for Desabilitado.)

	![Enable TCP][Image10]

5. No painel do console, clique em **Serviços do SQL Server**. (A reinicialização do Mecanismo de Banco de Dados pode ser adiada até a conclusão da próxima etapa.)

6. No painel de detalhes, clique com o botão direito do mouse em **SQL Server (_instance name_)** (a instância padrão é **SQL Server (MSSQLSERVER)**) e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server. 

	![Restart Database Engine][Image11]

7. Feche o SQL Server Configuration Manager.

Para obter mais informações sobre como habilitar protocolos para o Mecanismo de Banco de Dados do SQL Server, consulte [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)](http://msdn.microsoft.com/library/ms191294.aspx).

###<a id="Mixed">Configurar o SQL Server para autenticação de modo misto</a>

O Mecanismo de Banco de Dados do SQL Server não pode usar a Autenticação do Windows sem um ambiente de domínio. Para conectar-se ao Mecanismo de Banco de Dados de outro computador, configure o SQL Server para a autenticação de modo misto. A autenticação de modo misto permite a Autenticação do SQL Server e a Autenticação do Windows. (A configuração da autenticação de modo misto pode não ser necessária se você tiver configurado uma Rede Virtual do Azure. Para obter mais informações, consulte o tópico [Considerações de conectividade do SQL Serve em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/?LinkId=294723) no conjunto de documentação do [SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/?LinkId=294719).

1. Enquanto estiver conectado à máquina virtual usando a Área de Trabalho Remota, no menu Iniciar, clique em **Todos os Programas**, em **Microsoft SQL Server _versão_** e, em seguida, clique em **SQL Server Management Studio**. 

	![Start SSMS][Image18]

	Na primeira vez que você abrir o Management Studio ele deve criar o ambiente do Management Studio dos usuários. Isso pode demorar alguns instantes.

2. Ao abrir, o Management Studio apresenta a caixa de diálogo **Conectar ao Servidor**. Na caixa **Nome do servidor**, digite o nome da máquina virtual para conectar-se ao Mecanismo de Banco de Dados com o Object Explorer. (Em vez do nome da máquina virtual, também é possível usar **(local)** ou um único ponto como o **Nome do Servidor**. Selecione **Autenticação do Windows** e deixe **_your_VM_name_\your_local_administrator** na caixa **Nome de usuário**. Clique em **Conectar**.

	![Connect to Server][Image19]

3. No SQL Server Management Studio Object Explorer, clique com o botão direito do mouse no nome da instância do SQL Server (o nome da máquina virtual) e, em seguida, clique em **Propriedades**.

	![Server Properties][Image20]

4. Na página **Segurança**, em **Autenticação do Servidor**, selecione **SQL Server e Modo de Autenticação do Windows** e, em seguida, clique em **OK**.

	![Select Authentication Mode][Image21]

5. Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para confirmar a necessidade de reiniciar o SQL Server.

6. No Object Explorer, clique com o botão direito do mouse no seu servidor, e em seguida, clique em **Reiniciar**. (Se o SQL Server Agent estiver em execução, ele também deverá ser reinicializado.)

	![Restart][Image22]

7. Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para concordar que você quer reiniciar o SQL Server.

###<a id="Logins">Criar logons de autenticação do SQL Server</a>

Para conectar-se ao Mecanismo de Banco de Dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.

1. No SQL Server Management Studio Object Explorer, expanda a pasta da instância do servidor na qual você deseja criar o novo logon.

2. Clique com o botão direito do mouse na pasta **Segurança**, aponte para **Novo** e selecione **Logon...**.

	![New Login][Image23]

3. Na caixa de diálogo **Logon - Novo**, na página **Geral**, digite o nome do novo usuário na caixa **Nome de Logon**.

4. Selecione **Autenticação do SQL Server**.

5. Na caixa **Senha**, digite uma senha para o novo usuário. Insira novamente essa senha na caixa **Confirmar Senha**.

6. Para impor as opções de complexidade e imposição da política de senha, selecione **Impor Política de Senha** (recomendado). Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

7. Para impor as opções de expiração da política de senha, selecione **Impor Expiração de Senha** (recomendado). A opção Impor a Política de Senha deve estar selecionada para habilitar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

8. Para forçar o usuário a criar uma nova senha após a primeira vez que o logon é usado, selecione **O usuário deve alterar a senha no próximo logon** (recomendado se esse logon for para mais alguém usar. Se o logon for para seu próprio uso, não selecione essa opção.) A opção Impor a Expiração de Senha deve estar selecionada para habilitar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada. 

9. Na lista **Banco de dados padrão**, selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um usuário de banco de dados, mantenha essa definição como **mestre**.

10. Na lista **Idioma padrão**, mantenha **padrão** como o valor.
    
	![Login Properties][Image24]

11. Se esse for o primeiro logon que você está criando, talvez você queira designá-lo como um administrador do SQL Server. Em caso afirmativo, na página **Funções de Servidor**, marque **sysadmin**. 

	**Observação de segurança:** Os membros da função de servidor fixa sysadmin têm controle total sobre o mecanismo de banco de dados. Você deve restringir cuidadosamente a associação nessa função.

	![sysadmin][Image25]

12. Clique em OK.

Para obter mais informações sobre logons do SQL Server, consulte [Criar um logon (a página pode estar em inglês)](http://msdn.microsoft.com/library/aa337562.aspx).



###<a id="DNS">Determinar o nome DNS da máquina virtual</a>

Para conectar-se ao Mecanismo de Banco de Dados do SQL Server em outro computador, você deve saber o nome DNS (Sistema de Nome de Domínio) da máquina virtual. (Esse é o nome que a Internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterado quando o Azure mover os recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)  

1. No Portal de Gerenciamento do Azure (ou na etapa anterior), selecione **MÁQUINAS VIRTUAIS**. 

2. Na página **INSTÂNCIAS DA MÁQUINA VIRTUAL**, na coluna **NOME DNS**, localize e copie o nome DNS para a máquina virtual que aparece precedido por **http://**. (A interface do usuário pode não exibir o nome completo, mas você pode clicar com o botão direito do mouse nele e selecionar Copiar.)
	

### <a id="cde">Conectar-se ao Mecanismo de Banco de Dados de outro computador</a>
 
1. Em um computador conectado à Internet, abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, na caixa **Nome do servidor**, digite o nome DNS da máquina virtual (determinado na tarefa anterior) e o número da porta pública do ponto de extremidade no formato de *DNSName,portnumber* como **tutorialtestVM.cloudapp.net,57500**.

3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

4. Na caixa **Logon**, digite o nome de um logon que você criou em uma tarefa anterior.

5. Na caixa **Senha**, digite a senha do logon que você criou em uma tarefa anterior.

6. Clique em **Conectar**.

	![Connect using SSMS][Image33]

### <a id="cdea"> Conectando-se ao Mecanismo de Banco de Dados em seu aplicativo</a>

Se você puder se conectar a uma instância do SQL Server em execução em uma máquina virtual do Azure usando o Management Studio, você poderá se conectar usando uma cadeia de conexão semelhante à seguinte.

	connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Para obter mais informações, consulte [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Próximas etapas</a>
Você viu como criar e configurar um SQL Server em uma máquina virtual do Azure usando a imagem de plataforma. Ao usar o SQL Server em Máquinas Virtuais do Azure, recomendamos seguir as diretrizes detalhadas fornecidas na documentação do [SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) na biblioteca. Este conjunto de documentação inclui uma série de artigos e tutoriais que fornecem orientações detalhadas. A série inclui as seguintes sessões:

[SQL Server nas Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introdução ao SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparando-se para migrar para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- Como migrar os arquivos e o esquema do Banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados

[Implantação do SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- Como copiar arquivos de dados e de configuração do SQL Server em um disco de dados local para o Azure usando CSUpload
- Como criar uma máquina virtual básica local usando o Hyper-V
- Como criar uma máquina virtual do SQL Server no Azure usando um disco do SQL Server local existente
- Como criar uma máquina virtual SQL Server no Azure usando uma máquina virtual local existente do SQL Server 
- Como usar o PowerShell para configurar uma máquina virtual do SQL Server no Azure 
- Como usar um disco de dados anexado para arquivos de banco de dados do armazenamento

[Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- Tutorial: Conectar-se ao SQL Server no mesmo serviço de nuvem 
- Tutorial: Conectar-se ao SQL Server em outro serviço de nuvem 
- Tutorial: Conectar o aplicativo ASP.NET para SQL Server no Azure via Rede Virtual 

[Considerações sobre desempenho para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Soluções de problemas e monitoramento para SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Tutorial: Grupos de disponibilidade AlwaysOn no Azure (GUI)
- Tutorial: Grupos de Disponibilidade AlwaysOn no Azure (PowerShell)
- Tutorial: Configuração de escutas para grupos de disponibilidade do AlwaysOn
- Tutorial: Assistente de adição de réplica do Azure
- Tutorial: Espelhamento de banco de dados para recuperação de desastre no Azure
- Tutorial: Espelhamento de banco de dados para recuperação de desastre no Hybrid IT 
- Tutorial: Espelhamento de banco de dados de alta disponibilidade no Azure
- Tutorial: Envio de Logs para recuperação de desastre no Hybrid IT 
- Solucionando problemas de escuta de grupo de disponibilidade no Azure

[Backup e restauração para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Business Intelligence do SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- Use o PowerShell para criar uma VM do Azure com o SQL Server BI e o SharePoint 2010
- Use o PowerShell para criar uma VM do Azure com o SQL Server BI e o SharePoint 2013
- Use o PowerShell para criar uma VM do Azure com um servidor de relatório em modo nativo

[Data warehouse do SQL Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Artigos técnicos para o SQL Server nas Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [White paper: Compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure](http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/)

- [White paper: Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [White paper: Implantar o Business Intelligence do SQL Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [White paper: Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

- [White paper: Controle de visualização de relatórios do Reporting Services e máquina virtual do Microsoft Azure com base em servidores de relatórios](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLServerinVMConnectionMap.png
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
<!--HONumber=42-->
