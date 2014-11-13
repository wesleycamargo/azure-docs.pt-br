<properties urlDisplayName="Install SQL Server" pageTitle="Provisionar uma m&aacute;quina virtual do SQL Server no Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="Um tutorial que ensina como criar e configurar uma m&aacute;quina virtual do SQL Server no Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisionando uma m&aacute;quina virtual do SQL Server no Azure" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint" />

# Provisionando uma máquina virtual do SQL Server no Azure

A galeria de Máquinas Virtuais do Azure inclui várias imagens que contêm o Microsoft SQL Server. Você pode selecionar uma das imagens de máquina virtual da galeria e, com apenas alguns cliques, provisioná-la para o ambiente do Azure.

Neste tutorial, você irá:

-   [Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria.][Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria.]
-   [Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação][Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação]
-   [Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador][Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador]
-   [Próximas etapas][Próximas etapas]

## <span id="Provision"></span>Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria.</a>

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] (a página pode estar em inglês) usando sua conta. Se você não tiver uma conta do Azure, visite [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

2.  No Portal de Gerenciamento do Azure, na parte inferior esquerda da página da Web, clique em **+NOVO**, **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e, em seguida, em **DA GALERIA**.

3.  Na página **Criar uma Máquina Virtual**, selecione uma imagem de máquina virtual que contenha o SQL Server e clique na seta de avanço na parte inferior à direita da página. Para obter as informações mais atualizadas sobre as imagens do SQL Server com suporte no Azure, consulte o tópico [Introdução ao SQL Server em Máquinas Virtuais do Azure][Introdução ao SQL Server em Máquinas Virtuais do Azure] (a página pode estar em inglês) no conjunto de documentação [SQL Server em Máquinas Virtuais do Azure][SQL Server em Máquinas Virtuais do Azure].

    > [WACOM.NOTE] Se você tiver uma máquina virtual criada usando a imagem da plataforma da edição de avaliação do SQL Server, não será possível atualizá-la para uma imagem de edição paga por minuto na galeria. Você pode escolher uma das duas seguintes opções:

    > -   Criar uma nova máquina virtual usando a edição do SQL Server paga por minuto da galeria e migrar seus arquivos de banco de dados para essa nova máquina virtual, seguindo as etapas em [Como migrar os arquivos e o esquema de banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados][Como migrar os arquivos e o esquema de banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados]. **Ou**

    > -   Atualizar uma instância existente da Edição de Avaliação do SQL Server para outra edição do SQL Server sob o contrato [License Mobility por meio do Software Assurance no Azure][License Mobility por meio do Software Assurance no Azure] seguindo as etapas em [Atualizar para outra edição do SQL Server 2014][Atualizar para outra edição do SQL Server 2014]. Para obter informações sobre como adquirir a cópia licenciada do SQL Server, consulte [Como comprar o SQL Server (a página pode estar em inglês)][Como comprar o SQL Server (a página pode estar em inglês)].

4.  Na primeira página **Configuração de Máquina Virtual**, forneça as seguintes informações:

    -   Forneça um **NOME DE MÁQUINA VIRTUAL**.
    -   Na caixa **NOVO NOME DE USUÁRIO**, digite um nome de usuário exclusivo para a conta de administrador local da VM.
    -   Na caixa **NOVA SENHA**, digite uma senha forte. Para obter mais informações, consulte [Senhas fortes (a página pode estar em inglês)][Senhas fortes (a página pode estar em inglês)].
    -   Na caixa **CONFIRMAR SENHA**, digite a senha novamente.
    -   Selecione o **Tamanho** apropriado na lista suspensa.

    > [WACOM.NOTE] O tamanho da máquina virtual é especificado durante o provisionamento:
    > A2 é o menor tamanho recomendado para cargas de trabalho de produção.
    > O tamanho mínimo recomendado para uma máquina virtual é A3 ao usar a edição SQL Server Enterprise.
    > Selecione A3 ou superior ao usar a edição SQL Server Enterprise.
    > Selecione A4 ao usar o SQL Server 2012 Enterprise ou SQL Server 2014 Enterprise otimizado para imagens de cargas de trabalho transacionais.
    > Selecione A7 ao usar o SQL Server 2012 Enterprise ou o SQL Server 2014 Enterprise otimizado para imagens de Data Warehouse.
    > O tamanho selecionado limita o número de discos de dados que você pode configurar. Para obter informações mais atualizadas sobre tamanhos de máquina virtual disponíveis e o número de discos de dados que você pode anexar a uma máquina virtual, consulte [Tamanhos de Máquina Virtual para o Azure][Tamanhos de Máquina Virtual para o Azure] (a página pode estar em inglês).

    Clique na seta de avanço na parte inferior à direita para continuar.

    ![Configuração da VM][Configuração da VM]

5.  Na segunda página **Configuração da máquina virtual**, configure recursos para rede, armazenamento e disponibilidade:

    -   Na caixa **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**.
    -   Na caixa **Nome DNS do Serviço de Nuvem**, forneça a primeira parte de um nome DNS de sua escolha, para que ele complete um nome no formato **TESTNAME.cloudapp.net**
    -   Na caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione uma região onde essa imagem virtual será hospedada.
    -   Na **Conta de Armazenamento**, selecione uma conta de armazenamento existente ou selecione uma gerada automaticamente.
    -   Na caixa **CONJUNTO DE DISPONIBILIDADE**, selecione **(nenhum)**.
    -   Leia e aceite o termos legais.

6.  Clique na seta de avanço para continuar.

7.  Clique na marca de seleção no canto inferior à direita para continuar.

8.  Aguarde enquanto o Azure prepara sua máquina virtual. Espere que o status de máquina virtual prossiga por:

    -   Iniciando (Provisionamento)
    -   Parada
    -   Iniciando (Provisionamento)
    -   Executando (Provisionamento)
    -   Executando

## <span id="RemoteDesktop"></span>Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação</a>

1.  Quando a configuração for concluída, clique no nome da máquina virtual para ir para a página PAINEL. Na parte inferior da página, clique em **Conectar**.

2.  Escolha abrir o arquivo .rpd usando o programa Área de Trabalho Remota do Windows (`%windir%\system32\mstsc.exe`).

3.  Na caixa de diálogo **Segurança do Windows**, forneça a senha da conta do administrador local que você especificou em uma etapa anterior. (Pode ser solicitado que você verifique as credenciais da máquina virtual.)

4.  Na primeira vez que você fizer logon nessa máquina virtual, talvez seja necessário concluir vários processos, incluindo a configuração da sua área de trabalho, atualizações do Windows e a conclusão das tarefas da configuração inicial do Windows (sysprep). Depois que o sysprep do Windows é concluído, a configuração do SQL Server conclui as tarefas de configuração. Essas tarefas podem causar um pequeno atraso enquanto são concluídas.`SELECT @@SERVERNAME` pode não retornar com o nome correto até que a configuração do SQL Server seja concluída.

Quando você está conectado à máquina virtual com a Área de Trabalho Remota do Windows, a máquina virtual funciona de maneira muito semelhante à qualquer outro computador. Conecte-se à instância padrão do SQL Server com o SQL Server Management Studio (em execução na máquina virtual) da maneira normal.

## <span id="SSMS"></span>Concluir as etapas de Configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador</a>

Para poder conectar-se à instância do SQL Server na Internet, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

-   [Criar um ponto de extremidade TCP para a máquina virtual][Criar um ponto de extremidade TCP para a máquina virtual]
-   [Abrir portas TCP no firewall do Windows][Abrir portas TCP no firewall do Windows]
-   [Configurar o SQL Server para escutar no protocolo TCP][Configurar o SQL Server para escutar no protocolo TCP]
-   [Configurar o SQL Server para autenticação de modo misto][Configurar o SQL Server para autenticação de modo misto]
-   [Criar logons de autenticação do SQL Server][Criar logons de autenticação do SQL Server]
-   [Determinar o nome DNS da máquina virtual][Determinar o nome DNS da máquina virtual]
-   [Conectar-se ao Mecanismo de Banco de Dados de outro computador][Conectar-se ao Mecanismo de Banco de Dados de outro computador]
-   [Conectando-se ao Mecanismo de Banco de Dados em seu aplicativo] (\#cdea)

O caminho de conexão é resumido pelo diagrama a seguir:

![Conectando-se a uma máquina virtual do SQL Server][Conectando-se a uma máquina virtual do SQL Server]

### <span id="Endpoint"></span>Criar um ponto de extremidade TCP para a máquina virtual</a>

A máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

1.  No Portal de Gerenciamento do Azure, clique em **MAQUINAS VIRTUAIS**.

2.  Clique na máquina virtual recém-criada. As informações sobre sua máquina virtual são apresentadas.

3.  Próximo à parte superior da página, selecione a página **PONTOS DE EXTREMIDADE** e, na parte inferior da página, clique em **ADICIONAR**.

4.  Na página **Adicionar Ponto de Extremidade à Máquina Virtual**, clique em **Adicionar Ponto de Extremidade Independente** e, em seguida, clique na seta de avanço para continuar.

5.  Na página **Especificar os detalhes do ponto de extremidade**, forneça as seguintes informações.

    -   Na caixa **NOME**, forneça um nome para o ponto de extremidade.
    -   Na caixa **PROTOCOLO**, selecione **TCP**. Você pode digitar a porta de escuta padrão do SQL Server **1433** na caixa **Porta Particular**. Da mesma forma, você pode digitar **57500** na caixa **PORTA PÚBLICA**. Observe que muitas organizações selecionam números de porta diferentes para evitar ataques de segurança mal-intencionados.

6.  Clique na marca de seleção para continuar. O ponto de extremidade é criado.

### <span id="FW"></span>Abrir portas TCP no firewall do Windows para a instância padrão do Mecanismo de Banco de Dados</a>

1.  Conecte-se à máquina virtual por meio da Área de Trabalho Remota do Windows. Quando estiver conectado, no menu Iniciar, clique em **Executar**, digite **WF.msc**e, em seguida, clique em **OK**.

    ![Iniciar o Programa de Firewall][Iniciar o Programa de Firewall]

2.  No **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do mouse em **Regras de Entrada**e, em seguida, clique em **Nova Regra** no painel de ações.

    ![Nova Regra][Nova Regra]

3.  Na caixa de diálogo **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

4.  Na caixa de diálogo **Protocolo e Portas**, selecione **TCP**. Selecione **Portas locais específicas** e, em seguida, digite o número da porta da instância do Mecanismo de Banco de Dados (**1433** para a instância padrão ou sua opção para a porta privada na etapa de ponto de extremidade).

    ![Porta TCP 1433][Porta TCP 1433]

5.  Clique em **Próximo**.

6.  Na caixa de diálogo **Ação**, selecione **Permitir a conexão**, e clique em **Avançar**.

    **Observação de Segurança:** A seleção de **Permitir a conexão se for segura** pode fornecer segurança adicional. Selecione essa opção se você desejar configurar opções de segurança adicionais em seu ambiente.

    ![Permitir Conexões][Permitir Conexões]

7.  Na caixa de diálogo **Perfil**, selecione **Público** e clique em **Avançar**.

    **Observação de Segurança:** A seleção de **Público** permite acesso pela internet. Sempre que possível, selecione um perfil mais restritivo.

    ![Perfil Público][Perfil Público]

8.  Na caixa de diálogo **Nome**, digite um nome e uma descrição para essa regra, e clique em **Concluir**.

    ![Nome da Regra][Nome da Regra]

Abrir portas adicionais para outros componentes conforme necessário. Para obter mais informações, consulte [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)][Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)].

### <span id="TCP"></span>Configurar o SQL Server para escutar no protocolo TCP</a>

1.  Enquanto estiver conectado à máquina virtual usando a Área de Trabalho Remota, no menu Iniciar, clique em **Todos os Programas**, em **Microsoft SQL Server** *versão*, em **Ferramentas de Configuração** e, em seguida, clique em **SQL Server Configuration Manager**.

    ![Abrir SSCM][Abrir SSCM]

2.  No SQL Server Configuration Manager, no painel do console, expanda **Configuração de Rede do SQL Server**.

3.  No painel do console, clique em **Protocolos para *instance name***. (A instância padrão é **Protocolos para MSSQLSERVER**.)

4.  No painel de detalhes, clique com o botão direito do mouse em TCP, por padrão, ele deve estar habilitado para as imagens da Galeria. Para suas imagens personalizadas, clique em **Habilitar** (se o status for Desabilitado.)

    ![Habilitar TCP][Habilitar TCP]

5.  No painel do console, clique em **Serviços do SQL Server**. (A reinicialização do Mecanismo de Banco de Dados pode ser adiada até a conclusão da próxima etapa.)

6.  No painel de detalhes, clique com o botão direito do mouse em **SQL Server (*instance name*)** (a instância padrão é **SQL Server (MSSQLSERVER)**) e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

    ![Reiniciar o Mecanismo de Banco de Dados][Reiniciar o Mecanismo de Banco de Dados]

7.  Feche o SQL Server Configuration Manager.

Para obter mais informações sobre como habilitar protocolos para o Mecanismo de Banco de Dados do SQL Server, consulte [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)][Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)].

### <span id="Mixed"></span>Configurar o SQL Server para autenticação de modo misto</a>

O Mecanismo de Banco de Dados do SQL Server não pode usar a Autenticação do Windows sem um ambiente de domínio. Para conectar-se ao Mecanismo de Banco de Dados de outro computador, configure o SQL Server para a autenticação de modo misto. A autenticação de modo misto permite a Autenticação do SQL Server e a Autenticação do Windows. (A configuração da autenticação de modo misto pode não ser necessária se você tiver configurado uma Rede Virtual do Azure. Para obter mais informações, consulte o tópico [Considerações de conectividade do SQL Serve em Máquinas Virtuais do Azure][Considerações de conectividade do SQL Serve em Máquinas Virtuais do Azure] no conjunto de documentação do [SQL Server em Máquinas Virtuais do Azure][1].

1.  Enquanto estiver conectado à máquina virtual usando a Área de Trabalho Remota, no menu Iniciar, clique em **Todos os Programas**, em **Microsoft SQL Server *version*** e, em seguida, clique em **SQL Server Management Studio**.

    ![Iniciar o SSMS][Iniciar o SSMS]

    Na primeira vez que você abrir o Management Studio ele deve criar o ambiente do Management Studio dos usuários. Isso pode demorar alguns instantes.

2.  Ao abrir, o Management Studio apresenta a caixa de diálogo **Conectar ao Servidor**. Na caixa **Nome do servidor**, digite o nome da máquina virtual para conectar-se ao Mecanismo de Banco de Dados com o Object Explorer. (Em vez do nome da máquina virtual, também é possível usar **(local)** ou um único ponto como o **Nome do Servidor**. Selecione **Autenticação do Windows** e deixe ***your\_VM\_name*\\your\_local\_administrator** na caixa **Nome de usuário**. Clique em **Conectar**.

    ![Conectar-se ao servidor][Conectar-se ao servidor]

3.  No SQL Server Management Studio Object Explorer, clique com o botão direito do mouse no nome da instância do SQL Server (o nome da máquina virtual) e, em seguida, clique em **Propriedades**.

    ![Propriedades do servidor][Propriedades do servidor]

4.  Na página **Segurança**, em **Autenticação do Servidor**, selecione **SQL Server e Modo de Autenticação do Windows** e, em seguida, clique em **OK**.

    ![Selecionar modo de autenticação][Selecionar modo de autenticação]

5.  Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para confirmar a necessidade de reiniciar o SQL Server.

6.  No Object Explorer, clique com o botão direito do mouse no seu servidor, e em seguida, clique em **Reiniciar**. (Se o SQL Server Agent estiver em execução, ele também deverá ser reinicializado.)

    ![Reiniciar][Reiniciar]

7.  Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para concordar que você quer reiniciar o SQL Server.

### <span id="Logins"></span>Criar logons de autenticação do SQL Server</a>

Para conectar-se ao Mecanismo de Banco de Dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.

1.  No SQL Server Management Studio Object Explorer, expanda a pasta da instância do servidor na qual você deseja criar o novo logon.

2.  Clique com o botão direito do mouse na pasta **Segurança**, aponte para **Novo** e selecione **Logon...**.

    ![Novo Logon][Novo Logon]

3.  Na caixa de diálogo **Logon - Novo**, na página **Geral**, digite o nome do novo usuário na caixa **Nome de Logon**.

4.  Selecione **Autenticação do SQL Server**.

5.  Na caixa **Senha**, digite uma senha para o novo usuário. Insira novamente essa senha na caixa **Confirmar Senha**.

6.  Para impor as opções de complexidade e imposição da política de senha, selecione **Impor Política de Senha** (recomendado). Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

7.  Para impor as opções de expiração da política de senha, selecione **Impor Expiração de Senha** (recomendado). A opção Impor a Política de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

8.  Para forçar o usuário a criar uma nova senha após a primeira vez que o logon é usado, selecione **O usuário deve alterar a senha no próximo logon** (recomendado se esse logon for para mais alguém usar. Se o logon for para seu próprio uso, não selecione essa opção.) A opção Impor a Expiração de Senha deve estar selecionada para ativar essa caixa de seleção. Essa é uma opção padrão quando a autenticação do SQL Server está selecionada.

9.  Na lista **Banco de Dados Padrão**, selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um usuário de banco de dados, mantenha essa definição como **mestre**.

10. Na lista **Idioma padrão**, mantenha **padrão** como o valor.

    ![Propriedades de Logon][Propriedades de Logon]

11. Se esse for o primeiro logon que você está criando, talvez você queira designá-lo como um administrador do SQL Server. Em caso afirmativo, na página **Funções de Servidor**, marque **sysadmin**.

    **Observação de Segurança:** Os membros da função de servidor fixa sysadmin tem controle total sobre o Mecanismo de Banco de Dados. Você deve restringir cuidadosamente a associação nessa função.

    ![sysadmin][sysadmin]

12. Clique em OK.

Para obter mais informações sobre logons do SQL Server, consulte [Criar um logon (a página pode estar em inglês)][Criar um logon (a página pode estar em inglês)].

### <span id="DNS"></span>Determinar o nome DNS da máquina virtual</a>

Para conectar-se ao Mecanismo de Banco de Dados do SQL Server em outro computador, você deve saber o nome DNS (Sistema de Nome de Domínio) da máquina virtual. (Esse é o nome que a Internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterado quando o Azure mover os recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)

1.  No Portal de Gerenciamento do Azure (ou na etapa anterior), selecione **MÁQUINAS VIRTUAIS**.

2.  Na página **INSTÂNCIAS DA MÁQUINA VIRTUAL**, na coluna **NOME DNS**, localize e copie o nome DNS para a máquina virtual que aparece precedido por **http://**. (A interface do usuário pode não exibir o nome completo, mas você pode clicar com o botão direito do mouse nele e selecionar Copiar.)

### <span id="cde"></span>Conectar-se ao Mecanismo de Banco de Dados de outro computador</a>

1.  Em um computador conectado à Internet, abra o SQL Server Management Studio.

2.  Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, na caixa **Nome do servidor**, digite o nome DNS da máquina virtual (determinado na tarefa anterior) e um número de porta pública de ponto de extremidade no formato *DNSName, portnumber*, como **tutorialtestVM.cloudapp.net,57500**.

3.  Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

4.  Na caixa **Logon**, digite o nome de um logon que você criou em uma tarefa anterior.

5.  Na caixa **Senha**, digite a senha do logon que você criou em uma tarefa anterior.

6.  Clique em **Conectar**.

    ![Conectar-se usando SSMS][Conectar-se usando SSMS]

### <span id="cdea"></span> Conectando-se ao Mecanismo de Banco de Dados em seu aplicativo</a>

Se você puder se conectar a uma instância do SQL Server em execução em uma máquina virtual do Azure usando o Management Studio, você poderá se conectar usando uma cadeia de conexão semelhante à seguinte.

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Para obter mais informações, consulte [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)][Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)].

## <span id="Optional"></span>Próximas etapas</a>

Você viu como criar e configurar um SQL Server em uma máquina virtual do Azure usando a imagem de plataforma. Ao usar o SQL Server em Máquinas Virtuais do Azure, recomendamos seguir as diretrizes detalhadas fornecidas na documentação do [SQL Server em Máquinas Virtuais do Azure][SQL Server em Máquinas Virtuais do Azure] na biblioteca. Este conjunto de documentação inclui uma série de artigos e tutoriais que fornecem orientações detalhadas. A série inclui as seguintes sessões:

[SQL Server nas Máquinas Virtuais do Azure][SQL Server em Máquinas Virtuais do Azure]

[Introdução ao SQL Server em Máquinas Virtuais do Azure][Introdução ao SQL Server em Máquinas Virtuais do Azure]

[Preparando-se para migrar para o SQL Server em Máquinas Virtuais do Azure][Preparando-se para migrar para o SQL Server em Máquinas Virtuais do Azure]

-   Como migrar os arquivos e o esquema do Banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados.

[Implantação do SQL Server em Máquinas Virtuais do Azure][Implantação do SQL Server em Máquinas Virtuais do Azure]

-   Como copiar arquivos de dados e de configuração do SQL Server em um disco de dados local para o Azure usando CSUpload
-   Como criar uma máquina virtual básica local usando o Hyper-V
-   Como criar uma máquina virtual do SQL Server no Azure usando um disco do SQL Server local existente
-   Como criar uma máquina virtual SQL Server no Azure usando uma máquina virtual local existente do SQL Server
-   Como usar o PowerShell para configurar uma máquina virtual do SQL Server no Azure
-   Como usar um disco de dados anexado para arquivos de banco de dados do armazenamento

[Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure][Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure]

-   Tutorial: Conectar-se ao SQL Server no mesmo serviço de nuvem
-   Tutorial: Conectar-se ao SQL Server em outro serviço de nuvem
-   Tutorial: Conectar o aplicativo ASP.NET para SQL Server no Azure via Rede Virtual

[Considerações sobre desempenho para o SQL Server em Máquinas Virtuais do Azure][Considerações sobre desempenho para o SQL Server em Máquinas Virtuais do Azure]

[Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure][Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure]

[Soluções de problemas e monitoramento para SQL Server em Máquinas Virtuais do Azure][Soluções de problemas e monitoramento para SQL Server em Máquinas Virtuais do Azure]

[Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure][Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure]

-   Tutorial: Grupos de disponibilidade AlwaysOn no Azure (GUI)
-   Tutorial: Grupos de Disponibilidade AlwaysOn no Azure (PowerShell)
-   Tutorial: Configuração de escutas para grupos de disponibilidade do AlwaysOn
-   Tutorial: Assistente de adição de réplica do Azure
-   Tutorial: Espelhamento de banco de dados para recuperação de desastre no Azure
-   Tutorial: Espelhamento de banco de dados para recuperação de desastre no Hybrid IT
-   Tutorial: Espelhamento de banco de dados de alta disponibilidade no Azure
-   Tutorial: Envio de Logs para recuperação de desastre no Hybrid IT
-   Solucionando problemas de escuta de grupo de disponibilidade no Azure

[Backup e restauração para o SQL Server em Máquinas Virtuais do Azure][Backup e restauração para o SQL Server em Máquinas Virtuais do Azure]

[Business Intelligence do SQL Server em máquinas virtuais do Azure][Business Intelligence do SQL Server em máquinas virtuais do Azure]

-   Use o PowerShell para criar uma VM do Azure com o SQL Server BI e o SharePoint 2010
-   Use o PowerShell para criar uma VM do Azure com o SQL Server BI e o SharePoint 2013
-   Use o PowerShell para criar uma VM do Azure com um servidor de relatório em modo nativo

[Data warehouse do SQL Server em Máquinas Virtuais do Azure][Data warehouse do SQL Server em Máquinas Virtuais do Azure]

[Artigos técnicos para o SQL Server nas Máquinas Virtuais do Azure][Artigos técnicos para o SQL Server nas Máquinas Virtuais do Azure]

-   [White paper: Compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure][White paper: Compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure]

-   [White paper: Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure][White paper: Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure]

-   [White paper: Implantar o Business Intelligence do SQL Server em máquinas virtuais do Azure][White paper: Implantar o Business Intelligence do SQL Server em máquinas virtuais do Azure]

-   [White paper: Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure][White paper: Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure]

-   [White paper: Controle de visualização de relatórios do Reporting Services e máquina virtual do Microsoft Azure com base em servidores de relatórios][White paper: Controle de visualização de relatórios do Reporting Services e máquina virtual do Microsoft Azure com base em servidores de relatórios]

  [Conectar-se ao portal de gerenciamento do Azure e provisionar uma máquina virtual na galeria.]: #Provision
  [Abrir a máquina virtual usando a Área de Trabalho Remota e concluir a instalação]: #RemoteDesktop
  [Concluir as etapas de configuração para conectar-se à máquina virtual usando o SQL Server Management Studio em outro computador]: #SSMS
  [Próximas etapas]: #Optional
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
  [Introdução ao SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294720
  [SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294719
  [Como migrar os arquivos e o esquema de banco de dados do SQL Server entre máquinas virtuais no Azure usando discos de dados]: http://go.microsoft.com/fwlink/p/?LinkId=294738
  [License Mobility por meio do Software Assurance no Azure]: http://www.windowsazure.com/pt-br/pricing/license-mobility/
  [Atualizar para outra edição do SQL Server 2014]: http://go.microsoft.com/fwlink/?LinkId=396915
  [Como comprar o SQL Server (a página pode estar em inglês)]: http://www.microsoft.com/pt-br/sqlserver/get-sql-server/how-to-buy.aspx
  [Senhas fortes (a página pode estar em inglês)]: http://msdn.microsoft.com/library/ms161962.aspx
  [Tamanhos de Máquina Virtual para o Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn197896.aspx
  [Configuração da VM]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
  [Criar um ponto de extremidade TCP para a máquina virtual]: #Endpoint
  [Abrir portas TCP no firewall do Windows]: #FW
  [Configurar o SQL Server para escutar no protocolo TCP]: #TCP
  [Configurar o SQL Server para autenticação de modo misto]: #Mixed
  [Criar logons de autenticação do SQL Server]: #Logins
  [Determinar o nome DNS da máquina virtual]: #DNS
  [Conectar-se ao Mecanismo de Banco de Dados de outro computador]: #cde
  [Conectando-se a uma máquina virtual do SQL Server]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
  [Iniciar o Programa de Firewall]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
  [Nova Regra]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
  [Porta TCP 1433]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
  [Permitir Conexões]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
  [Perfil Público]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
  [Nome da Regra]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
  [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/cc646023.aspx
  [Abrir SSCM]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
  [Habilitar TCP]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
  [Reiniciar o Mecanismo de Banco de Dados]: ./media/virtual-machines-provision-sql-server/11Restart.png
  [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/ms191294.aspx
  [Considerações de conectividade do SQL Serve em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/?LinkId=294723
  [1]: http://go.microsoft.com/fwlink/?LinkId=294719
  [Iniciar o SSMS]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
  [Conectar-se ao servidor]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
  [Propriedades do servidor]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
  [Selecionar modo de autenticação]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
  [Reiniciar]: ./media/virtual-machines-provision-sql-server/22Restart2.png
  [Novo Logon]: ./media/virtual-machines-provision-sql-server/23New-Login.png
  [Propriedades de Logon]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
  [sysadmin]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
  [Criar um logon (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/aa337562.aspx
  [Conectar-se usando SSMS]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
  [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server (a página pode estar em inglês)]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx
  [Preparando-se para migrar para o SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294721
  [Implantação do SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294722
  [Considerações sobre conectividade para o SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294723
  [Considerações sobre desempenho para o SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/?LinkId=294724
  [Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294725
  [Soluções de problemas e monitoramento para SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294726
  [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294727
  [Backup e restauração para o SQL Server em Máquinas Virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294728
  [Business Intelligence do SQL Server em máquinas virtuais do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=294729
  [Data warehouse do SQL Server em Máquinas Virtuais do Azure]: http://msdn.microsoft.com/library/windowsazure/dn387396.aspx
  [Artigos técnicos para o SQL Server nas Máquinas Virtuais do Azure]: http://msdn.microsoft.com/library/azure/dn248435.aspx
  [White paper: Compreender o banco de dados SQL Azure e o SQL Server em máquinas virtuais do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/
  [White paper: Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure]: http://msdn.microsoft.com/library/azure/dn574746.aspx
  [White paper: Implantar o Business Intelligence do SQL Server em máquinas virtuais do Azure]: http://msdn.microsoft.com/library/windowsazure/dn321998.aspx
  [White paper: Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure]: http://msdn.microsoft.com/library/windowsazure/dn248436.aspx
  [White paper: Controle de visualização de relatórios do Reporting Services e máquina virtual do Microsoft Azure com base em servidores de relatórios]: http://msdn.microsoft.com/library/azure/dn753698.aspx
