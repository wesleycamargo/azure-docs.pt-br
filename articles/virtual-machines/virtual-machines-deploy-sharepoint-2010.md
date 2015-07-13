<properties
	pageTitle="Implantação do SharePoint 2010 em máquinas virtuais do Azure"
	description="Entenda os cenários com suporte de uso do SharePoint 2010 nas máquinas virtuais do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="josephd"/>


# Implantação do SharePoint 2010 em máquinas virtuais do Azure

O Microsoft SharePoint Server 2010 fornece alta flexibilidade de implantação que pode ajudar as organizações a determinarem os cenários de implantação adequados às suas necessidades e metas de negócios. Hospedada e gerenciada na nuvem, a oferta das Máquinas Virtuais do Azure fornece uma infraestrutura completa, confiável e disponível para dar suporte a diversos tipos de cargas de trabalho de aplicativos e bancos de dados sob demanda, como implantações do SharePoint e do Microsoft SQL Server.

Enquanto as Máquinas Virtuais do Azure dão suporte a várias cargas de trabalho, este artigo se concentra nas implantações do SharePoint. As Máquinas Virtuais do Azure habilitam as organizações a criarem e gerenciarem a infraestrutura do SharePoint com rapidez, provisionando e acessando praticamente qualquer host no mundo inteiro. Elas oferecem total controle e gerenciamento de processadores, RAM, tipos de CPU e outros recursos nas máquinas virtuais (VMs) do SharePoint.

As Máquinas Virtuais do Azure reduzem a necessidade de softwares para que as organizações não se preocupem com complexidade e altos custos de manuseio e sim com construção e gerenciamento de infraestruturas em escala. Isso significa que elas podem inovar, experimentar e iterar por horas em vez de dias e semanas, como acontece em implantações tradicionais.

> [AZURE.NOTE]Para obter informações sobre como implantar o SharePoint 2013 no Azure, confira [Planejando o SharePoint 2013 nos Serviços de Infraestrutura do Azure](https://msdn.microsoft.com/library/dn275958.aspx) e [farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint em Máquinas Virtuais do Azure

O SharePoint 2010 é compatível com a maioria das cargas de trabalho em uma implantação de Máquinas Virtuais do Azure. As Máquinas Virtuais do Azure são ideais para FIS (SharePoint Server para sites da Internet) e cenários de desenvolvimento. De modo similar, as cargas de trabalho principais do SharePoint também são compatíveis. Se uma organização deseja gerenciar e controlar sua própria implementação do SharePoint 2010 ao aproveitar opções de virtualização na nuvem, as Máquinas Virtuais do Azure são ideais para implantação.

A oferta de Máquinas Virtuais do Azure é hospedada e gerenciada na nuvem. Ela fornece flexibilidade de implantação e reduz custos, eliminando altos gastos em compras de hardwares. Com maior agilidade de infraestrutura, as organizações podem implantar o SharePoint Server em horas, em vez de dias ou semanas. As Máquinas Virtuais do Azure também permitem que as empresas implantem cargas de trabalho do SharePoint na nuvem usando o modelo "pré-pago". À medida que as cargas de trabalho do SharePoint crescem, uma organização pode expandir a infraestrutura rapidamente e, quando as necessidades de computação diminuírem, poderá devolver os recursos que não são mais necessários, pagando apenas pelo uso deles.

### Mudança no foco de TI

Muitas empresas utilizam componentes comuns de infraestrutura de TI e de gerenciamento terceirizados, como hardwares, sistemas operacionais, segurança, armazenamento de dados e backup, enquanto mantêm o controle de aplicativos essenciais, como o SharePoint Server. Ao delegar todas as camadas de serviço não essenciais de suas plataformas de TI a um provedor virtual, as organizações podem mudar seu foco para os principais serviços do SharePoint e fornecer valor comercial com os projetos do SharePoint, em vez de gastar mais tempo configurando a infraestrutura.

### Implantação mais rápida

O suporte e a implantação de uma grande estrutura do SharePoint podem coibir a agilidade do TI em oferecer suporte a requisitos comerciais. O tempo necessário para criar, testar e preparar os servidores e farms do SharePoint e implantá-los em um ambiente de produção pode levar semanas ou até meses, dependendo dos processos e restrições da organização. As Máquinas Virtuais do Azure permitem que as empresas implantem cargas de trabalho do SharePoint na nuvem rapidamente sem grandes gastos com hardware. Dessa forma, as organizações podem aproveitar a agilidade da infraestrutura para realizar a implantação em horas, em vez de dias ou semanas.

### Escalabilidade

Sem a necessidade de implantar, testar e preparar os servidores e farms físicos do SharePoint, as organizações podem expandir e contratar recursos de computação sob demanda sem perder tempo. À medida que os requisitos de carga de trabalho do SharePoint crescem, uma organização pode expandir rapidamente a sua infraestrutura na nuvem. Da mesma forma, quando as necessidades de computação diminuem, a organização pode reduzir os recursos, pagando apenas pelo que usar. As Máquinas Virtuais do Azure reduzem as despesas antecipadas e compromissos de longo prazo, permitindo que as organizações criem e gerenciem estruturas do SharePoint em escala. Novamente, isso significa que essas organizações podem inovar, experimentar e iterar por horas em vez de dias e semanas, como acontece em implantações tradicionais.

### Uso limitado

As Máquinas Virtuais do Azure fornecem poder de computação, memória e armazenamento para cenários do SharePoint, cujos preços são normalmente baseados no consumo de recursos. As organizações pagam somente pelo que usam, e os serviços fornecem todas as capacidades necessárias para executar a infraestrutura do SharePoint. Para obter mais informações sobre preços e cobranças, consulte [Detalhes de preços do Azure (a página pode estar em inglês)](http://azure.microsoft.com/pricing/). Há taxas nominais para armazenamento e para transferência de dados de uma rede local para a nuvem do Azure. No entanto, o Azure não cobra o carregamento de dados.

### Flexibilidade

As Máquinas Virtuais do Azure fornecem aos desenvolvedores a flexibilidade de escolherem o idioma ou o ambiente de tempo de execução desejado, compatíveis com .NET, Node.js, Java e PHP. Os desenvolvedores também podem escolher suas ferramentas com suporte para Microsoft Visual Studio, WebMatrix e editores de texto. Além disso, a Microsoft fornece uma alternativa mais econômica e segura para a nuvem, com facilidade de provisionamento e implantação às necessidades de relatório, que garante o acesso de inteligência (BI) em todos os dispositivos e locais. Finalmente, com a oferta do Azure, os usuários poderão não só mover VHDs para a nuvem, mas também copiar um VHD e executá-lo localmente ou através de outro provedor de nuvem, contanto que ele tenha a licença adequada.

## Processo de provisionamento

A biblioteca de imagens no Azure fornece a lista de VMs pré-configuradas disponíveis. Os usuários podem publicar o SharePoint Server, SQL Server, Windows Server e outros ISO/VHDs à biblioteca de imagens. Para simplificar a criação de VMs, as imagens base são criadas e publicadas na biblioteca. Os usuários autorizados podem usar essas imagens para gerar o VM desejado. Para saber mais, confira [Criar uma máquina virtual executando o Windows no Portal de Visualização do Azure](virtual-machines-windows-tutorial.md). A figura 1 mostra as etapas básicas para criar uma VM usando o Portal de Gerenciamento do Azure.

**Figura 1: visão geral das etapas de criação de uma VM**

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Os usuários também podem carregar uma imagem do SysPrep no Portal de Gerenciamento do Azure. Para saber mais, confira [Criar e carregar um VHD do Windows Server no Azure](virtual-machines-create-upload-vhd-windows-server.md). A figura 2 mostra as etapas básicas para carregar uma imagem para criar uma VM.

**Figura 2: visão geral das etapas de carregamento de imagem**

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

## Implantando o SharePoint 2010 no Azure 

Você pode implantar o SharePoint 2010 no Azure seguindo estas etapas:

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) através da sua conta. Se você não tiver uma conta do Azure, [inscreva-se para obter uma versão gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
2. Para criar uma máquina virtual com o sistema operacional base, no Portal de gerenciamento do Azure, clique **Novo > Computação > Máquina virtual > Da galeria**.
3. A caixa de diálogo **Escolher uma imagem** será exibida. Clique na imagem da plataforma **Windows Server 2008 R2 SP1**. Em seguida, clique na seta para a direita.
4. A caixa de diálogo **Configuração da máquina virtual** será exibida. Forneça as seguintes informações:
	- Digite um **Nome de máquina virtual**.
	- Selecione o tamanho adequado. Para um ambiente de produção (banco de dados e servidor do aplicativo SharePoint), é recomendável usar A3 (4 núcleos e memória de 7 GB) ou maior.
	- Em **Novo Nome de Usuário**, digite o nome da conta do administrador local.
	- Em **Nova Senha**, digite uma senha forte.
	- Em **Confirmar**, digite a senha novamente e clique na seta para a direita.
5. A segunda caixa de diálogo **Configuração de máquina virtual** é exibida. Forneça as seguintes informações:
	- Em **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**. Nesse caso, você também deve fornecer um nome DNS do serviço de nuvem ou selecionar um serviço de nuvem existente.
	- Em caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde a imagem virtual será hospedada.
	- Em **Conta de armazenamento**, escolha **Usar uma conta de armazenamento gerada automaticamente** ou selecione um nome de conta de armazenamento existente. Apenas uma conta de armazenamento por região será criada automaticamente. Todas as outras VMs criadas com essa configuração estarão localizadas nessa conta de armazenamento. Você está limitado a 20 contas de armazenamento. Para obter mais informações, consulte [Criar uma conta de armazenamento no Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure) (a página pode estar em inglês).
	- Em **Conjunto de Disponibilidade**, selecione **(nenhum)** e clique na seta para a direita.
6. Na terceira caixa de diálogo **Configuração de máquina virtual**, clique na marca de seleção para criar a máquina virtual.

Para se conectar à máquina virtual, confira [Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md).

Crie a VM do SQL Server usando uma das opções a seguir:

- Crie uma VM do SQL Server 2012 seguindo as etapas 1 a 7 acima - com exceção da etapa 3, use a imagem do SQL Server 2012 em vez da imagem do Windows Server 2008 R2 SP1. Para obter mais informações, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure (a página pode estar em inglês)](virtual-machines-provision-sql-server.md).
	- Quando você escolher essa opção, o processo de provisionamento manterá uma cópia dos arquivos de instalação do SQL Server 2012 no caminho de diretório C:\SQLServer_11.0_Full para que você possa personalizar a instalação. Por exemplo, é possível converter a instalação de avaliação do SQL Server 2012 a uma versão licenciada usando sua chave de licença.

- Use a ferramenta SQL Server System Preparation (SysPrep) para instalar o SQL Server na VM com o sistema operacional base (conforme exibido acima nas etapas 1 a 7). Para obter mais informações, consulte [Instalar o SQL Server 2012 usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Use o prompt de comando para instalar o SQL Server. Para obter mais informações, consulte [Instalar o SQL Server 2012 do prompt de comando](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Use a mídia com suporte do SQL Server e a sua chave de licença para instalar o SQL Server na VM com o sistema operacional base (conforme exibido acima nas etapas 1 a 7).

Crie o farm do SharePoint usando as etapas a seguir:

Etapa 1. Configure a assinatura do Azure usando arquivos de script.

Etapa 2. Provisione os servidores do SharePoint criando outra VM com o sistema operacional base (conforme mostrado acima nas etapas 1 a 6). Para criar um servidor do SharePoint nesta VM, escolha uma das seguintes opções:

- Provisione usando a GUI do SharePoint:
	- Para criar e provisionar um farm do SharePoint, consulte [Crie e configure o farm](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Para adicionar uma rede ou um servidor de aplicativos, confira [Adicionar servidores de aplicativos ou da Web a farms](http://technet.microsoft.com/library/cc261752.aspx).
	- Para adicionar um servidor de banco de dados a um farm existente, consulte [Adicionar um servidor de banco de dados a um farm existente](http://technet.microsoft.com/library/cc262781).
	- Para usar o SQL Server 2012 no seu farm do SharePoint, é necessário baixar e instalar o Service Pack 1 para o SharePoint Server 2010 depois de instalar o aplicativo e escolher não configurar o servidor. Para obter mais informações, consulte [Service Pack 1 para SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Para aproveitar os recursos do SQL Server BI, é recomendável instalar o SharePoint Server como um farm de servidores em vez de um servidor autônomo. Para obter mais informações, consulte [Instalar os recursos do SQL Server 2012 Business Intelligence](http://technet.microsoft.com/library/hh231681.aspx).

- Provisionamento usando o Microsoft Windows PowerShell: Você pode usar a ferramenta de linha de comando Psconfig como uma interface alternativa para executar várias opções de controlam a forma como os produtos do SharePoint 2010 são provisionados. Para obter mais informações, consulte [Referência da linha de comando Psconfig (a página pode estar em inglês)](http://technet.microsoft.com/library/cc263093.aspx).

Etapa 3. Configure o SharePoint. Depois que cada VM do SharePoint estiver no estado Pronto, configure o SharePoint Server em cada servidor usando as seguintes opções:

- Configure o SharePoint a partir da GUI.
- Configure o SharePoint usando o Windows PowerShell. Para obter mais informações, consulte [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- Também é possível usar o AutoSPInstaller do CodePlex Project, que consiste em scripts do Windows PowerShell, um arquivo de entrada XML e um arquivo em lotes padrão do Microsoft Windows. O AutoSPInstaller fornece uma estrutura para um script de instalação do SharePoint 2010 baseada no Windows PowerShell. Para saber mais, confira [CodePlex: AutoSPInstaller (a página pode estar em inglês)](http://autospinstaller.codeplex.com/).

Etapa 4. Depois que o script for concluído, conecte-se à VM usando o Painel da VM.

Para verificar a configuração do SharePoint, faça logon no SharePoint Server e use a Administração Central.

> [AZURE.NOTE]Não se esqueça de configurar a segurança no ponto de extremidade do Portal de Gerenciamento e de definir uma porta de dados de entrada no Firewall do Windows da VM. Em seguida, confirme que você pode iniciar uma sessão do Windows PowerShell para um dos servidores de aplicativo do SharePoint abrindo uma sessão do Windows PowerShell com credenciais do Administrador.

### Criando e carregando um Disco Rígido Virtual

Você também pode criar suas próprias imagens e carregá-las no Azure como um arquivo VHD. Para obter informações, confira [Criar e carregar um VHD do Windows Server no Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Cenários de uso

Esta seção aborda alguns cenários de cliente importantes para implantações do SharePoint usando as Máquinas Virtuais do Azure.

### Cenário 1: Implantação simples do SharePoint e ambiente de teste

As organizações buscam maneiras mais rápidas para criar aplicativos do SharePoint e configurar ambientes do SharePoint para testes e desenvolvimentos dentro e fora do país. Essencialmente, elas buscam encurtar o tempo necessário para configurar os projetos de desenvolvimento de aplicativos do SharePoint e diminuir os custou aumentando o uso de seus ambientes de teste. Por exemplo, uma organização pode querer executar um teste de carga sob demanda no SharePoint Server e executar seu teste de aceitação do usuário (UAT) com mais usuários simultaneamente em diferentes localidades. De maneira similar, a integração de equipes dentro e fora do país é uma necessidade comercial cada vez mais importante para muitas organizações hoje em dia.

Este cenário explica como as organizações podem usar farms pré-configurados do SharePoint para desenvolvimento e cargas de trabalho de teste. A topologia de implantação do SharePoint é bastante semelhante a uma implantação local virtualizada. As habilidades de TI necessárias correspondem exatamente a 1:1 para a implantação das Máquinas Virtuais do Azure, com a grande vantagem de garantir praticamente uma mudança de custos altos a operacionais - não há necessidade de comprar um servidor físico previamente. As organizações podem eliminar os altos gastos com hardware de servidor e obter flexibilidade reduzindo drasticamente o tempo de provisionamento necessário para criar, configurar ou expandir um farm do SharePoint para um ambiente de teste e desenvolvimento. A TI pode adicionar e remover dinamicamente a capacidade para atender a novas necessidades de teste e desenvolvimento. Além disso, a TI pode se concentrar mais em oferecer valor comercial com projetos do SharePoint e menos em gerenciar infraestruturas.

Para utilizar as máquinas de carga e teste completamente, as organizações podem configurar a implantação virtualizada do SharePoint e testar máquinas no Azure com suporte de sistema operacional para o Windows Server 2008 R2. Isso permitirá que as equipes de desenvolvimento criem e testem aplicativos e migrem facilmente para ambientes locais ou de produção da nuvem sem necessidade de alterar códigos. As mesmas estruturas e conjuntos de ferramentas podem ser usados localmente e na nuvem, permitindo o acesso compartilhado da equipe no mesmo ambiente. Os usuários também podem acessar dados e aplicativos localmente estabelecendo uma conexão VPN direta.

A Figura 3 mostra uma implantação e um ambiente de teste do SharePoint na VM do Azure. Para criar essa implantação, comece usando localmente o mesmo ambiente de teste e desenvolvimento do SharePoint usado para desenvolver aplicativos. Em seguida, carregue e implante os aplicativos na VM do Azure para teste e desenvolvimento. Se a sua organização decidir retornar o aplicativo para o local físico, poderá fazê-lo sem precisar modificar o aplicativo.

**Figura 3: ambiente de teste e desenvolvimento do SharePoint nas Máquinas Virtuais do Azure**

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

Para implementar um ambiente de teste e desenvolvimento do SharePoint no Azure, siga estas etapas:

1. Provisionar: primeiro, provisione uma conexão de VPN entre o local físico e o Azure usando a Rede Virtual do Azure. Como o Active Directory não será usado aqui, um túnel VPN será necessário. Para saber mais, confira [Visão geral da rede virtual](http://msdn.microsoft.com/library/jj156007.aspx). Em seguida, use o Portal de Gerenciamento para provisionar uma nova VM usando uma imagem de estoque da biblioteca de imagens.
	- Você pode carregar as VMs de teste e desenvolvimento locais do SharePoint na sua conta de armazenamento do Azure e consultá-las através da biblioteca de imagens para criar o ambiente necessário.
	- Você pode usar a imagem do SQL Server 2012 em vez da imagem do Windows Server 2008 R2 SP1. Para obter mais informações, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure (a página pode estar em inglês)](virtual-machines-provision-sql-server.md).

2. Instalar: Instale o SharePoint Server, o Visual Studio e o SQL Server nas VMs usando uma conexão de área de trabalho remota.
	- Use o SharePoint 2010 Easy Setup Script para criar um computador de desenvolvedor do SharePoint. Para obter mais informações, consulte [SharePoint 2010 Easy Setup Script (a página pode estar em inglês)](http://www.microsoft.com/download/details.aspx?id=23415). Use o Windows PowerShell. Para obter mais informações, consulte [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Use o AutoSPInstaller do CodePlex Project. Para saber mais, confira [CodePlex: AutoSPInstaller (a página pode estar em inglês)](http://autospinstaller.codeplex.com/).
	- Instalar Visual Studio. Para obter mais informações, consulte [Instalando o Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Instale o SQL Server. Para obter mais informações, consulte [Instalar o SQL Server 2012 usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Desenvolver pacotes de implantação e scripts para aplicativos e bancos de dados: Se você planeja usar uma VM disponível da biblioteca de imagens, os aplicativos e bancos de dados locais desejados poderão ser implantados nas Máquinas Virtuais do Azure:
	- Crie pacotes de implantação para os aplicativos e bancos de dados locais usando o SQL Server Data Tools e o Visual Studio.
	- Use esses pacotes para implantar os aplicativos e bancos de dados nas Máquinas Virtuais do Azure.
4. Implantar aplicativos e bancos de dados do SharePoint:
	- Configure a segurança no ponto de extremidade do Portal de Gerenciamento e defina uma porta de dados de entrada no Firewall do Windows da VM.
	- Implante aplicações e bancos de dados do SharePoint nas Máquinas Virtuais do Azure usando os pacotes de implantação e os scripts criados na etapa 3.
- Teste os aplicativos e bancos de dados implantados.
5. Gerenciar VMs:
	- Monitore as VMs usando o Portal de Gerenciamento.
	- Gerencie os aplicativos usando o Visual Studio e o SQL Server Management Studio.
	- Também é possível monitorar e gerenciar as VMs usando softwares de gerenciamento locais, como o Microsoft System Center Operations Manager.

### Cenário 2: Farm do SharePoint para o público com personalização

As organizações querem criar uma presença na Internet que seja hospedada na nuvem e que seja facilmente escalável com base na necessidade e na demanda. Também querem criar sites da extranet de parceiros para colaboração e implementar um processo fácil para criação distribuída e aprovação do conteúdo do site. Por fim, para lidar com o aumento de cargas, essas organizações querem fornecer capacidade sob demanda aos seus sites.

Nesse cenário, o SharePoint Server é usado como base para hospedar um site para o público. Ele permite que as organizações implantem, personalizem e hospedem rapidamente seus sites comerciais em uma estrutura de nuvem segura e escalável. Com os sites do SharePoint voltados para o público no Azure, as organizações podem dimensionar à medida que o tráfego aumenta e pagar somente pelo que usarem. Ferramentas comuns, semelhantes àquelas usadas localmente, podem ser usadas para criação de conteúdo, fluxo de trabalho e aprovação com o SharePoint no Azure.

Além disso, usando as Máquinas Virtuais do Azure, as organizações podem facilmente configurar a execução de ambientes de preparo e produção nas VMs. As VMs do SharePoint voltadas para o público que foram criadas no Azure podem ser armazenadas em backup virtual. Além disso, para fins de recuperação de desastres, o recurso de replicação geográfica contínua permite que as organizações façam backup automático das VMs que operam em uma central de dados para outra a quilômetros de distância.

As VMs na infraestrutura do Azure são validadas e com suporte para funcionar com outros produtos Microsoft, como o SQL Server e o SharePoint Server. A união do Azure com o SharePoint Server faz a força: ambos são parte da família Microsoft e são continuamente integrados, compatíveis e testados em conjunto para garantir a melhor experiência. Ambos tem um único ponto de suporte para o aplicativo SharePoint e para a infraestrutura do Azure.

Neste cenário, mais servidores front-end da Web para o SharePoint Server devem ser adicionados para dar maior suporte ao tráfego. Esses servidores exigem maior segurança e controles de domínio do Serviços de Domínio Active Directory para dar suporte à autenticação e autorização de usuários. A Figura 4 mostra o layout desse cenário.

**Figura 4: farm do SharePoint voltado para o público com personalização**

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

Para implementar um farm do SharePoint voltado para o público no Azure, siga estas etapas:

1. Implantar o Active Directory: os requisitos fundamentais para implantar o Active Directory em Máquinas Virtuais do Azure são semelhantes, mas não idênticos, aos da implantação em VMs (e, de algum modo, máquinas físicas) no local. Para obter mais informações sobre as diferenças, bem como as diretrizes e outras considerações, consulte [Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/jj156090). Para implantar o Active Directory no Azure:
	- Defina e crie uma rede virtual na qual as VMs podem ser atribuídas a sub-redes específicas.
	- Use o Portal de Gerenciamento para criar e implantar o controlador do domínio em uma nova VM no Azure. Você também pode consultar o script do Windows PowerShell para implantar um domínio autônomo na nuvem usando as Máquinas Virtuais do Azure a Rede Virtual. Para obter mais informações sobre como criar uma nova floresta do Active Directory em uma Rede Virtual do Azure, consulte [Instalação de uma nova floresta de Ative Directory no Azure](active-directory-new-forest-virtual-machine.md) (a página pode estar em inglês).
2. Provisione uma VM: Use o Portal de Gerenciamento para provisionar uma nova VM de uma imagem de estoque na biblioteca de imagens.
3. Implantar um farm do SharePoint.
	- Use o Portal de Gerenciamento para configurar o balanceamento de carga. Configure os pontos de extremidade da VM, selecione a opção para balancear a carga do tráfego em um ponto de extremidade existente e especifique o nome da VM com carga balanceada.
	- Adicione outra VM da Web de front-end ao farm do SharePoint existente para obter mais tráfego.
3. Gerenciar VMs:
	- Monitore as VMs usando o Portal de Gerenciamento.
	- Monitore o farm do SharePoint usando a Administração Central.

### Cenário 3: Farm reduzido para serviços de BI adicionais

O Business intelligence é essencial para obter informações importantes e tomar decisões rápidas e confiáveis. À medida que as organizações saem da abordagem local, elas não querem fazer mudanças no ambiente de BI ao implantar os aplicativos de BI existentes na nuvem. Elas querem hospedar relatórios do SQL Server Analysis Services (SSAS) ou do SQL Server Reporting Services (SSRS) em um ambiente disponível e altamente durável, enquanto mantêm controle total do aplicativo de BI sem gastar muito tempo e dinheiro em manutenção.

Este cenário descreve como as organizações podem usar as Máquinas Virtuais do Azure para hospedar aplicativos de BI essenciais. As organizações podem implantar farms do SharePoint em Máquinas Virtuais do Azure e reduzir os componentes de BI da VM do servidor do aplicativo, como o SSRS ou os Serviços do Excel. Ao dimensionar componentes que consomem muitos recursos na nuvem, elas podem melhorar e facilitar o suporte a cargas de trabalho especializadas. O SQL Server nas Máquinas Virtuais do Azure funciona bem, e é fácil dimensionar instâncias do SQL Server, que variam de instalações pequenas a avantajadas. Isso fornece flexibilidade, permitindo que as organizações provisionem (expandam) ou desprovisionem (diminuam) instâncias de BI dinamicamente, com base em necessidades de carga de trabalho imediatas.

Migrar aplicativos de BI existentes para o Azure fornece melhor dimensionamento. Com a tecnologia do SSAS, do SSRS e do SharePoint Server, as organizações podem criar aplicativos de BI e de relatório avançados, além de painéis que podem aumentar ou reduzir. Esses aplicativos e painéis também podem ser integrados com mais segurança em dados e aplicativos locais. O Azure garante conformidade do data center com suporte para ISO 27001. Para saber mais, vá para o [Azure Trust Center](http://azure.microsoft.com/support/trust-center/compliance/).

Para escalar horizontalmente a implantação de componentes de BI, um novo servidor de aplicativos, com serviços como PowerPivot, Power View, Serviços do Excel ou Serviços PerformancePoint, deve ser instalado. Ou as instâncias de BI do SQL Server como SSAS ou SSRS devem ser adicionadas no farm existente para oferecer suporte ao processamento de consultas adicional. O servidor pode ser adicionado como uma nova VM do Azure com o SharePoint 2010 Server ou o SQL Server instalado. Em seguida, os componentes de BI poderão ser instalados, implantados e configurados nesse servidor (Figura 5).

**Figura 5: farm do SharePoint reduzido para serviços de BI adicionais**

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

Para escalar horizontalmente um ambiente de BI no Azure, siga estas etapas:

1. Provisão:
	- Provisione uma conexão de VPN entre o local físico e o Azure usando a Rede Virtual do Azure. Para saber mais, confira [Visão geral da rede virtual](http://msdn.microsoft.com/library/jj156007.aspx).
	- Use o Portal de Gerenciamento para provisionar uma nova VM de uma imagem de estoque na biblioteca de imagens. Você pode carregar as imagens de cara de trabalho de BI do SharePoint Server ou do SQL Server à biblioteca de imagens, e qualquer usuário autorizado poderá escolher essas VMs do componente de BI para criar o ambiente reduzido.
2. Instalar: 
	- Se a sua organização não tiver imagens predefinidas dos componentes de BI do SharePoint Server ou do SQL Server, instale o SharePoint Server e o SQL Server nas VMs usando uma conexão de área de trabalho remota.
	- Para saber mais sobre como instalar o SharePoint Server, confira [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex: AutoSPInstaller (a página pode estar em inglês)](http://autospinstaller.codeplex.com/).
	- Para obter mais informações sobre como instalar o SQL Server, consulte [Instalar o SQL Server usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Adicionar a VM de BI:
	- Configure a segurança no ponto de extremidade do Portal de Gerenciamento e defina uma porta de dados de entrada no Firewall do Windows da VM.
	- Adicione a VM de BI recém-criada ao farm do SharePoint ou do SQL Server.
4. Gerenciar VMs:
	- Monitore as VMs usando o Portal de Gerenciamento.
	- Monitore o farm do SharePoint usando a Administração Central.
	- Monitore e gerencie as VMs usando softwares de gerenciamento locais como o Microsoft System Center - Operations Manager.

### Cenário 4: Site baseado no SharePoint totalmente personalizado

Cada vez mais, as empresas desejam criar sites de SharePoint totalmente personalizados na nuvem. Elas necessitam de um ambiente disponível e altamente durável que ofereça controle total para manter os aplicativos complexos em execução na nuvem, mas não desejam gastar tempo e orçamento em excesso.

Nesse cenário, uma organização pode implantar todo o seu farm do SharePoint na nuvem e dimensionar dinamicamente todos os componentes para obter mais capacidade ou expandir sua implantação local para a nuvem para aumentar a capacidade e melhorar o desempenho quando necessário. O cenário aborda as organizações que desejam ter uma experiência do SharePoint completa para o desenvolvimento de aplicativos e gerenciamento de conteúdo empresarial. Os sites mais complexos também podem incluir relatórios aprimorados, Power View, PerformancePoint, PowerPivot, gráficos detalhados e a maioria dos demais recursos de sites do SharePoint para obter todas as funcionalidades disponíveis.

As organizações podem usar as Máquinas Virtuais do Azure para hospedar aplicativos personalizados e componentes associados em uma infraestrutura de nuvem econômica e altamente segura. Elas também podem usar o Microsoft System Center local como uma ferramenta de gerenciamento comum para aplicativos locais e na nuvem.

Para implantar um site do SharePoint totalmente personalizável no Azure, a organização deve implantar um domínio do Active Directory na nuvem e provisionar novas VMs nesse domínio. Em seguida, uma VM com SQL Server 2012 deve ser criada e configurada como parte de um farm do SharePoint. Por fim, o farm do SharePoint deve ser criado, balanceado e conectado ao Active Directory e ao SQL Server (Figura 6).

**Figura 6: site baseado no SharePoint totalmente personalizado**

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

As etapas a seguir mostram como criar um ambiente de farm do SharePoint personalizado de imagens predefinidas disponíveis na biblioteca de imagens. No entanto, também é possível carregar as VMs do farm do SharePoint na biblioteca de imagens, e os usuários autorizados podem escolher essas VMs para criar o farm do SharePoint necessário no Azure.

1. Implantar o Active Directory: os requisitos fundamentais para implantar o Active Directory nas Máquinas Virtuais do Azure são semelhantes, mas não idênticos, aos da implantação em VMs (e, de algum modo, máquinas físicas) no local. Para obter mais informações sobre as diferenças, bem como as diretrizes e outras considerações, consulte [Diretrizes para implantação do Active Directory do Windows Server em Máquinas Virtuais do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/jj156090). Para implantar o Active Directory no Azure:
	- Defina e crie uma rede virtual na qual as VMs podem ser atribuídas a sub-redes específicas.
	- Use o Portal de Gerenciamento para criar e implantar o controlador do domínio em uma nova VM no Azure.
	- Para obter mais informações sobre como criar uma nova floresta do Active Directory em uma Rede Virtual do Azure, consulte [Instalação de uma nova floresta de Ative Directory no Azure](active-directory-new-forest-virtual-machine) (a página pode estar em inglês).
2. Implantar o SQL Server:
	- Use o Portal de Gerenciamento para provisionar uma nova VM de uma imagem de estoque na biblioteca de imagens.
	- Configure o SQL Server na VM. Para obter mais informações, consulte [Instalar o SQL Server 2012 usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Adicione a VM ao domínio do Active Directory recém-criado.
3. Implantar um farm de multisservidor do SharePoint:
	- Crie uma rede virtual. Para saber mais, confira [Visão geral da rede virtual](http://msdn.microsoft.com/library/jj156007.aspx).
	- Ao implantar as VMs do SharePoint, você precisará das sub-redes fornecidas para o SharePoint Server; dessa forma, os endereços de DNS na caixa do Active Directory local estarão disponíveis durante o provisionamento.
	- Use o Portal de Gerenciamento para criar uma VM.
	- Instale o SharePoint Server nesta VM e gere uma imagem reutilizável. Para saber mais sobre como instalar o SharePoint Server, confira [Instalar e configurar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Configure a VM do SharePoint para criar e conectar-se ao farm do SharePoint usando o comando [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx).
	- Use o Portal de Gerenciamento para configurar o balanceamento de carga: configure os pontos de extremidade da VM, selecione a opção para balancear a carga do tráfego em um ponto de extremidade existente e especifique o nome da VM com carga balanceada.
4. Gerencie o farm do SharePoint através do System Center:
	- Use o agente do Operations Manager e o novo Pacote de Integração do Azure para conectar o seu System Center local às Máquinas Virtuais do Azure.
	- Use o App Controller e o Orchestrator local para funções de gerenciamento.

## Resumo

As Máquinas Virtuais do Azure fornecem uma série completa de implantações para o SharePoint. Elas foram testadas e são totalmente compatíveis com outros produtos Microsoft para oferecer a melhor experiência possível. Dessa forma, as empresas podem facilmente configurar e implantar o SharePoint Server no Azure, seja para provisionar a infraestrutura de uma nova implantação do SharePoint ou para expandir a atual. À medida que as cargas de trabalho aumentam, as organizações podem expandir suas infraestruturas do SharePoint rapidamente. Da mesma forma, se houver necessidade de reduzir a carga de trabalho, as organizações podem contratar recursos sob demanda, pagando apenas pelo que usarem. As Máquinas Virtuais do Azure fornecem uma infraestrutura excepcional para uma grande variedade de requisitos de negócios, conforme mostrado nos cenários do SharePoint discutidos neste artigo.

Uma implantação bem-sucedida do SharePoint Server nas Máquinas Virtuais do Azure exigem um planejamento sólido, especialmente se considerarmos as variedades de opções de arquitetura de farms essenciais e as opções de implantação. As informações e as práticas recomendadas descritas neste artigo podem ajudar a orientar as decisões de implementação e de implantação do SharePoint.

## Recursos adicionais

[SharePoint em máquinas virtuais do Azure](http://msdn.microsoft.com/library/dn275955.aspx)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint da Intranet](virtual-machines-workload-intranet-sharepoint-farm)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

 

<!---HONumber=July15_HO1-->