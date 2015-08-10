<properties 
	pageTitle="Provisionar uma máquina virtual do SQL Server no Azure" 
	description="Um tutorial que ensina como criar e configurar uma máquina virtual do SQL Server no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="jroth"/>

# Provisionando uma máquina virtual do SQL Server no Azure #

A galeria de Máquinas Virtuais do Azure inclui várias imagens que contêm o Microsoft SQL Server. Você pode selecionar uma das imagens de máquina virtual da galeria e, com apenas alguns cliques, provisioná-la para o ambiente do Azure.

Neste tutorial, você vai:

* [Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria](#Provision)
* [Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação](#RemoteDesktop)
* [Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador](#SSMS)
* [Próximas etapas](#Optional)

##<a id="Provision">Provisionar uma máquina virtual do SQL Server por meio da galeria</a>

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) usando sua conta. Se você não tiver uma conta do Azure, visite [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

2. No Portal de Gerenciamento do Azure, na parte inferior esquerda da página da Web, clique em **+NOVO**, **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e, em seguida, em **DA GALERIA**.

3. Na página **Escolher uma imagem**, clique em **SQL SERVER**. Em seguida, selecione uma imagem do SQL Server. Clique na seta Avançar no canto inferior direito da página. ![Escolha uma imagem][Image34]


Para obter as informações mais atualizadas sobre as imagens do SQL Server com suporte no Azure, consulte o tópico [Introdução ao SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) (a página pode estar em inglês) no conjunto de documentação [SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

   
>[AZURE.NOTE]Se você tiver uma máquina virtual criada usando a imagem da plataforma da edição de avaliação do SQL Server, não será possível atualizá-la para uma imagem de edição paga por minuto na galeria. Você pode escolher uma das seguintes opções: criar uma nova máquina virtual usando a edição paga por minuto do SQL Server por meio da galeria e migrar seus arquivos de banco de dados para essa nova máquina virtual, seguindo as etapas em [Como migrar arquivos de banco de dados do SQL Server e o esquema entre máquinas virtuais no Azure usando discos de dados](http://go.microsoft.com/fwlink/p/?LinkId=294738), **ou** então você pode atualizar uma instância existente da edição de avaliação do SQL Server para outra edição do SQL Server sob o contrato de [Mobilidade de Licenças por meio de Software Assurance no Azure](http://azure.microsoft.com/pricing/license-mobility/), seguindo as etapas em [Atualizar para uma edição diferente do SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Para obter informações sobre como adquirir a cópia licenciada do SQL Server, consulte [Como comprar o SQL Server (a página pode estar em inglês)](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).


4. Na primeira página **Configuração de Máquina Virtual**, forneça as seguintes informações:
	- UMA **DATA DE LANÇAMENTO DA VERSÃO**. Se houver várias imagens, selecione a mais recente.
	- Um **NOME DA MÁQUINA VIRTUAL** exclusivo.
	- Na caixa **NOVO NOME DE USUÁRIO**, digite um nome de usuário exclusivo para a conta de administrador local da máquina.
	- Na caixa **NOVA SENHA**, digite uma senha forte. 
	- Na caixa **CONFIRMAR SENHA**, digite a senha novamente.
	- Selecione o **Tamanho** apropriado na lista suspensa. 

	>[AZURE.NOTE]O tamanho da máquina virtual é especificado durante o provisionamento: A2 é o menor tamanho recomendado para cargas de trabalho de produção. O tamanho mínimo recomendado para uma máquina virtual é A3 ao usar o SQL Server Enterprise Edition. Selecione A3 ou maior ao usar o SQL Server Enterprise Edition. Selecione A4 ao usar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens de cargas de trabalho transacionais. Selecione A7 ao usar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens de cargas de trabalho do Data Warehouse. O tamanho selecionado limita o número de discos de dados que você pode configurar. Para obter informações mais atualizadas sobre tamanhos de máquina virtual disponíveis e o número de discos de dados que você pode anexar a uma máquina virtual, consulte [Tamanhos de Máquina Virtual para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx) (a página pode estar em inglês).

	Clique na seta de avanço na parte inferior à direita para continuar.

	![Configuração da VM][Image4]


5. Na segunda página **Configuração da máquina virtual**, configure recursos para rede, armazenamento e disponibilidade:
	- Na caixa **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**.
	- Na caixa **Nome DNS do Serviço de Nuvem**, forneça a primeira parte de um nome DNS de sua escolha, para que ele complete um nome no formato **TESTNAME.cloudapp.net** 
	- Selecione uma **ASSINATURA**, se você tiver várias assinaturas para escolher. A escolha determina quais **contas de armazenamento** estão disponíveis. - Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.
	- Na **conta de armazenamento**, gere automaticamente uma conta, ou selecione uma na lista. Altere a **ASSINATURA** para ver mais contas. 
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
	

##<a id="RemoteDesktop">Abra a VM usando a Área de Trabalho Remota para concluir a configuração</a>

1. Quando o provisionamento for concluído, clique no nome da sua máquina virtual para ir para a página PAINEL. Na parte inferior da página, clique em **Conectar**.
2. Clique no botão **Abrir**. ![Clique no botão Abrir][Image37]

3. Na caixa de diálogo **Segurança do Windows**, clique em **Usar outra conta**. ![Clique em Usar outra conta][Image38]
4. Use o nome do computador como o nome de domínio, seguido pelo nome do seu administrador neste formato:.`machinename\username` Digite sua senha e conecte-se ao computador.

4. Na primeira vez que você fizer logon, vários processos serão concluídos, incluindo a configuração da área de trabalho, atualizações do Windows e a conclusão das tarefas de configuração inicial do Windows (sysprep). Depois que o sysprep do Windows é concluído, a configuração do SQL Server conclui as tarefas de configuração. Essas tarefas podem causar um atraso de alguns minutos enquanto são concluídas. O `SELECT @@SERVERNAME` pode não retornar o nome correto até a conclusão da instalação do SQL Server e o SQL Server Management Studio pode não ser visível na página inicial.

Quando você está conectado à máquina virtual com a Área de Trabalho Remota do Windows, a máquina virtual funciona de maneira muito semelhante à qualquer outro computador. Conecte-se à instância padrão do SQL Server com o SQL Server Management Studio (em execução na máquina virtual) da maneira normal.

##<a id="SSMS">Conectar à instância VM do SQL Server do SSMS em outro computador</a>

As etapas a seguir demonstram como se conectar à instância do SQL Server pela Internet usando o SSMS (SQL Server Management Studio). No entanto, as mesmas etapas se aplicam para tornar sua máquina virtual de SQL Server acessível para seu aplicativos, em execução local e no Azure.

Para poder conectar-se à instância do SQL Server na Internet ou em outra VM, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

- [Criar um ponto de extremidade TCP para a máquina virtual](#Endpoint)
- [Abrir portas TCP no firewall do Windows](#FW)
- [Configurar o SQL Server para escutar no protocolo TCP](#TCP)
- [Configurar o SQL Server para autenticação do modo misto](#Mixed)
- [Criar logons de autenticação do SQL Server](#Logins)
- [Determinar o nome DNS da máquina virtual](#DNS)
- [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#cde)
- [Conectando-se ao Mecanismo de Banco de Dados em seu aplicativo](#cdea)

O caminho de conexão é resumido pelo diagrama a seguir:

![Conectando-se a uma máquina virtual do SQL Server][Image8b]

###<a id="Endpoint">Criar um ponto de extremidade TCP para a máquina virtual</a>

Para acessar o SQL Server da Internet, a máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

>[AZURE.NOTE]Se estiver se conectando dentro do mesmo serviço de nuvem ou rede virtual, você não precisa criar um ponto de extremidade acessível publicamente. Nesse caso, você pode continuar para a próxima etapa. Para saber mais, consulte [Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133152.aspx).

1. No Portal de Gerenciamento do Azure, clique em **MAQUINAS VIRTUAIS**.
	
2. Clique na máquina virtual recém-criada. As informações sobre sua máquina virtual são apresentadas.
	
3. Próximo à parte superior da página, selecione a página **PONTOS DE EXTREMIDADE** e, na parte inferior da página, clique em **ADICIONAR**.
	
4. Na página **Adicionar Ponto de Extremidade à Máquina Virtual**, clique em **Adicionar Ponto de Extremidade Independente** e, em seguida, clique na seta de avanço para continuar.
	
5. Na página **Especificar os detalhes do ponto de extremidade**, forneça as seguintes informações.

	- Na caixa **NOME**, forneça um nome para o ponto de extremidade.
	- Na caixa **PROTOCOLO**, selecione **TCP**. Você pode digitar **57500** na caixa **PORTA PÚBLICA**. Da mesma forma, você pode digitar a porta de escuta padrão do SQL Server **1433** na caixa **Porta Particular**. Observe que muitas organizações selecionam números de porta diferentes para evitar ataques de segurança mal-intencionados. 

6. Clique na marca de seleção para continuar. O ponto de extremidade é criado.

###<a id="FW">Abrir portas TCP no Firewall do Windows para a instância padrão do Mecanismo de Banco de Dados</a>

1. Conecte-se à máquina virtual por meio da Área de Trabalho Remota do Windows. Uma vez conectado na tela inicial, digite **WF. msc** e pressione ENTER. 

	![Iniciar o Programa de Firewall][Image12]
2. No **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do mouse em **Regras de Entrada**e, em seguida, clique em **Nova Regra** no painel de ações.

	![Nova Regra][Image13]

3. Na caixa de diálogo **Assistente para Nova Regra de Entrada**, em **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

4. Na caixa de diálogo **Protocolo e Portas**, use o **TCP** padrão. Na caixa **Portas locais específicas**, digite o número da porta da instância do Mecanismo de Banco de Dados (\*\*1433\*\* para a instância padrão ou sua opção para a porta privada na etapa de ponto de extremidade).

	![Porta TCP 1433][Image14]

5. Clique em **Próximo**.

6. Na caixa de diálogo **Ação**, selecione **Permitir a conexão**, e clique em **Avançar**.

	**Observação sobre segurança:** a seleção de **Permitir a conexão se for segura** pode fornecer segurança adicional. Selecione essa opção se você desejar configurar opções de segurança adicionais em seu ambiente.

	![Permitir Conexões][Image15]

7. Na caixa de diálogo **Perfil**, selecione **Público**, **Privado** e **Domínio**. Em seguida, clique em **Próximo**.

    **Observação sobre segurança:** a seleção de **Público** permite acesso pela internet. Sempre que possível, selecione um perfil mais restritivo.

	![Perfil Público][Image16]

8. Na caixa de diálogo **Nome**, digite um nome e uma descrição para essa regra, e clique em **Concluir**.

	![Nome da Regra][Image17]

Abrir portas adicionais para outros componentes conforme necessário. Para obter mais informações, consulte [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)](http://msdn.microsoft.com/library/cc646023.aspx).


###<a id="TCP">Configurar o SQL Server para escutar no protocolo TCP</a>

1. Enquanto conectado à máquina virtual, na página inicial, digite **SQL Server Configuration Manager** e pressione ENTER.
	
	![Abrir SSCM][Image9]

2. No SQL Server Configuration Manager, no painel do console, expanda **Configuração de Rede do SQL Server**.

3. No painel de console, clique em **Protocolos para MSSQLSERVER** (ele padroniza o nome da instância). No painel de detalhes, clique com o botão direito do mouse em TCP, por padrão, ele deve estar habilitado para as imagens da Galeria. Para suas imagens personalizadas, clique em **Habilitar** (se o status for Desabilitado.)

	![Habilitar TCP][Image10]

5. No painel do console, clique em **Serviços do SQL Server**. No painel de detalhes, clique com o botão direito do mouse em **SQL Server (\_instance name\_)** (a instância padrão é **SQL Server (MSSQLSERVER)**) e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

	![Reiniciar o Mecanismo de Banco de Dados][Image11]

7. Feche o SQL Server Configuration Manager.

Para obter mais informações sobre como habilitar protocolos para o Mecanismo de Banco de Dados do SQL Server, consulte [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)](http://msdn.microsoft.com/library/ms191294.aspx).

###<a id="Mixed">Configurar o SQL Server para autenticação de modo misto</a>

O Mecanismo de Banco de Dados do SQL Server não pode usar a Autenticação do Windows sem um ambiente de domínio. Para conectar-se ao Mecanismo de Banco de Dados de outro computador, configure o SQL Server para a autenticação de modo misto. A autenticação de modo misto permite a Autenticação do SQL Server e a Autenticação do Windows.

>[AZURE.NOTE]A configuração da autenticação de modo misto pode não ser necessária se você tiver configurado uma Rede Virtual do Azure com um ambiente de domínio configurado.

1. Enquanto conectado à máquina virtual, na página inicial, digite **SQL Server 2014 Management Studio** e clique no ícone selecionado.

	![Iniciar o SSMS][Image18]

	Na primeira vez que você abrir o Management Studio ele deve criar o ambiente do Management Studio dos usuários. Isso pode demorar alguns instantes.

2. O Management Studio apresenta a caixa de diálogo **Conectar ao Servidor**. Na caixa **Nome do servidor**, digite o nome da máquina virtual para conectar-se ao Mecanismo de Banco de Dados com o Object Explorer. (Em vez do nome da máquina virtual, também é possível usar **(local)** ou um único ponto como o **Nome do Servidor**. Selecione **Autenticação do Windows** e deixe **_your\_VM\_name_\\your\_local\_administrator** na caixa **Nome de usuário**. Clique em **Conectar**.

	![Conectar-se ao servidor][Image19]

3. No SQL Server Management Studio Object Explorer, clique com o botão direito do mouse no nome da instância do SQL Server (o nome da máquina virtual) e, em seguida, clique em **Propriedades**.

	![Propriedades do servidor][Image20]

4. Na página **Segurança**, em **Autenticação do Servidor**, selecione **SQL Server e Modo de Autenticação do Windows** e, em seguida, clique em **OK**.

	![Selecionar modo de autenticação][Image21]

5. Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para confirmar a necessidade de reiniciar o SQL Server.

6. No Object Explorer, clique com o botão direito do mouse no seu servidor, e em seguida, clique em **Reiniciar**. (Se o SQL Server Agent estiver em execução, ele também deverá ser reinicializado.)

	![Reiniciar][Image22]

7. Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para concordar que você quer reiniciar o SQL Server.

###<a id="Logins">Criar logons de autenticação do SQL Server</a>

Para conectar-se ao Mecanismo de Banco de Dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.

1. No SQL Server Management Studio Object Explorer, expanda a pasta da instância do servidor na qual você deseja criar o novo logon.

2. Clique com o botão direito do mouse na pasta **Segurança**, aponte para **Novo** e selecione **Logon...**.

	![Novo Logon][Image23]

3. Na caixa de diálogo **Logon - Novo**, na página **Geral**, digite o nome do novo usuário na caixa **Nome de Logon**.

4. Selecione **Autenticação do SQL Server**.

5. Na caixa **Senha**, digite uma senha para o novo usuário. Insira novamente essa senha na caixa **Confirmar Senha**.

6. Para impor as opções de complexidade e imposição da política de senha, selecione **Impor Política de Senha** (recomendado). Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

7. Para impor as opções de expiração da política de senha, selecione **Impor Expiração de Senha** (recomendado). A opção Impor a Política de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

8. Para forçar o usuário a criar uma nova senha após a primeira vez que o logon é usado, selecione **O usuário deve alterar a senha no próximo logon** (recomendado se esse logon for para mais alguém usar. Se o logon for para seu próprio uso, não selecione essa opção.) A opção Impor a Expiração de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

9. Na lista **Banco de Dados Padrão**, selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um usuário de banco de dados, mantenha essa definição como **mestre**.

10. Na lista **Idioma padrão**, mantenha **padrão** como o valor.
    
	![Propriedades de Logon][Image24]

11. Se esse for o primeiro logon que você está criando, talvez você queira designá-lo como um administrador do SQL Server. Em caso afirmativo, na página **Funções de Servidor**, marque **sysadmin**.

	**Observação sobre segurança:** os membros da função de servidor fixa sysadmin tem controle total sobre o Mecanismo de Banco de Dados. Você deve restringir cuidadosamente a associação nessa função.

	![sysadmin][Image25]

12. Clique em OK.

Para obter mais informações sobre logons do SQL Server, consulte [Criar um logon (a página pode estar em inglês)](http://msdn.microsoft.com/library/aa337562.aspx).



###<a id="DNS">Determinar o nome DNS da máquina virtual</a>

Para conectar-se ao Mecanismo de Banco de Dados do SQL Server em outro computador, você deve saber o nome DNS (Sistema de Nome de Domínio) da máquina virtual. (Esse é o nome que a Internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterado quando o Azure mover os recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)

1. No Portal de Gerenciamento do Azure (ou na etapa anterior), selecione **MÁQUINAS VIRTUAIS**. 

2. Na página **INSTÂNCIAS DA MÁQUINA VIRTUAL** na coluna **Visão rápida**, localize e copie o nome DNS para a máquina virtual.

![Nome DNS][Image35]
	

### <a id="cde">Conectar-se ao Mecanismo de Banco de Dados de outro computador</a>
 
1. Em um computador conectado à Internet, abra o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, na caixa \*\*Nome do servidor\*\*, digite o nome DNS da máquina virtual (determinado na tarefa anterior) e um número da porta pública de ponto de extremidade no formato *NomeDNS,númerodaporta*, como **tutorialtestVM.cloudapp.net,57500**. Para obter o número da porta, faça logon no Portal de Gerenciamento do Azure e encontre a Máquina Virtual. No painel, clique em **PONTOS DE EXTREMIDAD**E e use a **PORTA PÚBLICA** atribuída ao **MSSQL**. ![Porta pública][Image36]
3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.
5. Na caixa **Logon**, digite o nome de um logon que você criou em uma tarefa anterior.
6. Na caixa **Senha**, digite a senha do logon que você criou em uma tarefa anterior.
7. Clique em **Conectar**.

	![Conectar-se usando SSMS][Image33]

## <a id="cdea">Conectar-se ao Mecanismo de Banco de Dados em seu aplicativo</a>

Se você puder se conectar a uma instância do SQL Server em execução em uma máquina virtual do Azure usando o Management Studio, você poderá se conectar usando uma cadeia de conexão semelhante à seguinte.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Para obter mais informações, consulte [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Próximas etapas</a>
Você viu como criar e configurar um SQL Server em uma máquina virtual do Azure usando a imagem de plataforma. Em muitos casos, a próxima etapa é migrar os bancos de dados para essa nova VM do SQL Server. Para obter diretrizes sobre migração de banco de dados, consulte [Migrando um banco de dados para SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md).

Além desses recursos, é recomendável seguir as diretrizes detalhadas fornecidas na documentação [SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) na biblioteca. Este conjunto de documentação inclui uma série de artigos e tutoriais que fornecem orientações detalhadas. A série inclui as seguintes sessões:

[SQL Server nas Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introdução ao SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparando-se para migrar para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

[Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

[Considerações sobre desempenho para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Soluções de problemas e monitoramento para SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Tutorial: Grupos de disponibilidade AlwaysOn no Azure (GUI)
- Tutorial: Grupos de Disponibilidade AlwaysOn no Azure (PowerShell)
- Tutorial: Configuração de escutas para grupos de disponibilidade do AlwaysOn
- Tutorial: adicionar Assistente de réplica do Azure
- Solucionando problemas de escuta de grupo de disponibilidade no Azure

[Backup e restauração para o SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Business Intelligence do SQL Server em Máquinas Virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

[Cargas de trabalho transacionais e data warehouse do SQL Server nas máquinas virtuais do Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Artigos técnicos sobre o SQL Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [White paper: compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [White paper: estratégias de desenvolvimento e padrões de aplicativo para o SQL Server em Máquinas Virtuais do Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

[Image4]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
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
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Private-Domain-Profile.png
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
[Image34]: ./media/virtual-machines-provision-sql-server/choose-sql-vm.png
[Image35]: ./media/virtual-machines-provision-sql-server/sql-vm-dns-name.png
[Image36]: ./media/virtual-machines-provision-sql-server/sql-vm-port-number.png
[Image37]: ./media/virtual-machines-provision-sql-server/click-open-to-connect.png
[Image38]: ./media/virtual-machines-provision-sql-server/credentials.png
 

<!---HONumber=July15_HO5-->