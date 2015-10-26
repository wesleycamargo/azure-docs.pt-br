<properties
	pageTitle="SharePoint Server 2010 em VMs do Azure | Microsoft Azure"
	description="Conheça o valor do SharePoint em máquinas virtuais do Azure, o processo passo a passo e os cenários com suporte para o uso do SharePoint 2010."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="josephd"/>


# Implantação do SharePoint 2010 em máquinas virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


O Microsoft SharePoint Server 2010 oferece alta flexibilidade de implantação, o que pode ajudar as organizações a determinarem os cenários de implantação adequados às suas necessidades e objetivos de negócios. Hospedada e gerenciada no Microsoft Azure, a oferta de Máquinas Virtuais do Azure oferece uma infraestrutura completa, confiável e disponível para dar suporte a diversos tipos de cargas de trabalho de aplicativos e bancos de dados sob demanda, como implantações do SharePoint e do Microsoft SQL Server.

Embora as máquinas virtuais do Azure deem suporte a várias cargas de trabalho, este artigo se concentra nas implantações do SharePoint. As máquinas virtuais do Azure habilitam as organizações a criarem e gerenciarem a infraestrutura do SharePoint, provisionando e acessando com rapidez praticamente qualquer host no mundo inteiro. Elas oferecem total controle e gerenciamento de processadores, RAM, tipos de CPU e outros recursos nas máquinas virtuais do SharePoint.

As máquinas virtuais do Azure reduzem a necessidade de softwares para que as organizações não se preocupem com complexidade e altos custos de manuseio e sim com a criação e o gerenciamento de infraestruturas em escala. Isso significa que elas podem inovar, experimentar e iterar por horas em vez de dias e semanas, como acontece em implantações tradicionais.

> [AZURE.NOTE]Para obter informações sobre como implantar o SharePoint 2013 no Azure, consulte [Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint em máquinas virtuais do Azure

O SharePoint 2010 é compatível com a maioria das cargas de trabalho em uma implantação de máquinas virtuais do Azure. As máquinas virtuais do Azure são ideais para FIS (SharePoint Server para sites da Internet) e cenários de desenvolvimento. As cargas de trabalho principais do SharePoint também são compatíveis. Se uma organização deseja gerenciar e controlar sua própria implementação do SharePoint 2010 ao aproveitar opções de virtualização em uma infraestrutura de nuvem, as máquinas virtuais do Azure são ideais para implantação.

A oferta de Máquinas Virtuais do Azure é hospedada e gerenciada na nuvem. Ela fornece flexibilidade de implantação e reduz custos, eliminando altos gastos em compras de hardwares. Com maior agilidade de infraestrutura, as organizações podem implantar o SharePoint Server em horas, em vez de dias ou semanas. As máquinas virtuais do Azure também permitem que as empresas implantem cargas de trabalho do SharePoint na nuvem usando o modelo "pré-pago". À medida que a carga de trabalho do SharePoint aumenta, uma organização poderá expandir rapidamente a infraestrutura. Então, quando as necessidades de computação diminuírem, ela poderá devolver os recursos que não sejam mais necessários, pagando apenas pelo que for usado.

### Mudança no foco de TI

Muitas empresas utilizam componentes comuns de infraestrutura de TI e de gerenciamento terceirizados, como hardwares, sistemas operacionais, segurança, armazenamento de dados e backup, enquanto mantêm o controle de aplicativos essenciais, como o SharePoint Server. Ao delegar todas as camadas de serviço não essenciais de suas plataformas de TI a um provedor virtual, as organizações podem mudar seu foco para os principais serviços do SharePoint e fornecer valor comercial com os projetos do SharePoint, em vez de gastar mais tempo configurando a infraestrutura.

### Implantação mais rápida

O suporte e a implantação de uma grande estrutura do SharePoint podem coibir a agilidade do TI em oferecer suporte a requisitos comerciais. O tempo necessário para criar, testar e preparar os servidores e farms do SharePoint e implantá-los em um ambiente de produção pode levar semanas ou até meses, dependendo dos processos e restrições da organização. As máquinas virtuais do Azure permitem que as empresas implantem cargas de trabalho do SharePoint na nuvem rapidamente sem grandes gastos com hardware. Dessa forma, as organizações podem aproveitar a agilidade da infraestrutura para realizar a implantação em horas, em vez de dias ou semanas.

### Escalabilidade

Sem a necessidade de implantar, testar e preparar os servidores e farms físicos do SharePoint, as organizações podem expandir e contratar recursos de computação sob demanda sem perder tempo. À medida que os requisitos de carga de trabalho do SharePoint crescem, uma organização pode expandir rapidamente a sua infraestrutura de nuvem. Da mesma forma, quando as necessidades de computação diminuem, a organização pode reduzir os recursos, pagando apenas pelo que usar. As máquinas virtuais do Azure reduzem as despesas antecipadas e compromissos de longo prazo, permitindo que as organizações criem e gerenciem estruturas do SharePoint em escala. Novamente, isso significa que essas organizações podem inovar, experimentar e iterar por horas em vez de dias e semanas, como acontece em implantações tradicionais.

### Uso limitado

As máquinas virtuais do Azure fornecem poder de computação, memória e armazenamento para cenários do SharePoint, cujos preços são normalmente baseados no consumo de recursos. As organizações pagam somente pelo que usam, e os serviços fornecem todas as capacidades necessárias para executar a infraestrutura do SharePoint. Para obter informações sobre preços e cobranças, acesse [Detalhes de preços do Azure](http://azure.microsoft.com/pricing/). Observe que há taxas nominais para o armazenamento e a transferência de dados do Azure para uma rede local. No entanto, o Azure não cobra o carregamento de dados.

### Flexibilidade

As máquinas virtuais do Azure fornecem aos desenvolvedores a flexibilidade de escolherem a linguagem ou o ambiente de tempo de execução desejado, com suporte oficial para .NET, Node.js, Java e PHP. Os desenvolvedores também podem escolher suas ferramentas com suporte para Microsoft Visual Studio, WebMatrix e editores de texto. Além disso, a Microsoft fornece uma alternativa mais econômica e segura para a nuvem, com facilidade de provisionamento e implantação para as necessidades de relatório, garantindo o acesso de business intelligence (BI) em todos os dispositivos e locais. Finalmente, com a oferta do Azure, os usuários poderão não só mover VHDs para a nuvem, mas também copiar um VHD e executá-lo localmente ou através de outro provedor de nuvem, contanto que ele tenha a licença adequada.

## Processo de provisionamento

A biblioteca de imagens no Azure fornece a lista de máquinas virtuais pré-configuradas disponíveis. Os usuários podem publicar o SharePoint Server, SQL Server, Windows Server e outros ISO/VHDs à biblioteca de imagens. Para simplificar a criação de máquinas virtuais, as imagens base são criadas e publicadas na biblioteca. Os usuários autorizados podem usar essas imagens para gerar a máquina virtual desejada. Para saber mais, consulte [Criar uma máquina virtual executando o Windows no Portal do Azure](virtual-machines-windows-tutorial-classic-portal.md). A Figura 1 mostra as etapas básicas para criar uma máquina virtual usando o portal do Azure.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

**Figura 1: visão geral das etapas de criação de uma máquina virtual**

Os usuários também podem carregar uma imagem do SysPrep no Portal de Gerenciamento do Azure. Para saber mais, confira [Criar e carregar um VHD do Windows Server no Azure](virtual-machines-create-upload-vhd-windows-server.md). A figura 2 mostra as etapas básicas para carregar uma imagem para criar uma VM.

No portal do Azure, os usuários também podem carregar uma imagem na qual a ferramenta SysPrep já foi executada. Para saber mais, confira [Criar e carregar um VHD do Windows Server no Azure](virtual-machines-create-upload-vhd-windows-server.md). A Figura 2 mostra as etapas básicas para carregar uma imagem para criar uma máquina virtual.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

**Figura 2: visão geral das etapas de carregamento de imagem**

## Implantando o SharePoint 2010 no Azure 

Você pode implantar o SharePoint 2010 no Azure usando o modelo de implantação clássica seguindo estas etapas:

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) usando a conta da sua assinatura do Azure. Se você não tiver uma conta do Azure, [inscreva-se para obter uma versão gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
2. Para criar uma máquina virtual com o sistema operacional base, no portal do Azure, clique em **Novo > Computação > Máquina Virtual > Da Galeria**.
3. A caixa de diálogo **Escolher uma imagem** será exibida. Clique na imagem da plataforma **Windows Server 2008 R2 SP1**. Em seguida, clique na seta para a direita.
4. A caixa de diálogo **Configuração da máquina virtual** será exibida. Forneça as seguintes informações:
	- Digite um **Nome de máquina virtual**.
	- Selecione o tamanho adequado. Para um ambiente de produção (banco de dados e servidor de aplicativos do SharePoint), é recomendável usar A3 (4 núcleos e memória de 7 GB) ou maior.
	- Em **Novo Nome de Usuário**, digite o nome da conta do administrador local.
	- Em **Nova Senha**, digite uma senha forte.
	- Em **Confirmar**, digite a senha novamente e clique na seta para a direita.
5. A segunda caixa de diálogo **Configuração de máquina virtual** é exibida. Forneça as seguintes informações:
	- Em **Serviço de Nuvem**, selecione **Criar um novo serviço de nuvem**. Nesse caso, você também deve fornecer um nome DNS do serviço de nuvem ou selecionar um serviço de nuvem existente.
	- Em caixa **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde a imagem virtual será hospedada.
	- Em **Conta de armazenamento**, clique em **Usar uma conta de armazenamento gerada automaticamente** ou selecione o nome de uma conta de armazenamento existente. Apenas uma conta de armazenamento por região será criada automaticamente. Todas as outras máquinas virtuais criadas com essa configuração estarão localizadas nessa conta de Armazenamento. Você está limitado a 20 contas de armazenamento. Para saber mais, consulte [Criar uma conta de armazenamento no Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure).
	- Em **Conjunto de Disponibilidade**, selecione **(nenhum)** e clique na seta para a direita.
6. Na terceira caixa de diálogo **Configuração de máquina virtual**, clique na marca de seleção para criar a máquina virtual.

Para se conectar à máquina virtual, consulte [Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md).

Crie a máquina virtual do SQL Server usando uma das opções a seguir:

- Crie uma máquina virtual do SQL Server 2012 seguindo as etapas 1 a 7 acima, mas, na etapa 3, use a imagem do SQL Server 2012 em vez da imagem do Windows Server 2008 R2 SP1. Para saber mais, consulte [Provisionando uma máquina virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md).
	- Ao clicar nessa opção, o processo de provisionamento manterá uma cópia dos arquivos de instalação do SQL Server 2012 no caminho de diretório C:\\SQLServer\_11.0\_Full para que você possa personalizar a instalação. Por exemplo, é possível converter a instalação de avaliação do SQL Server 2012 a uma versão licenciada usando sua chave de licença.

- Use a ferramenta de Preparação do Sistema do SQL Server (SysPrep) para instalar o SQL Server na máquina virtual com o sistema operacional base (como exibido acima nas etapas 1 a 7). Para saber mais, confira [Instalar o SQL Server 2012 usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Use o prompt de comando para instalar o SQL Server. Para saber mais, confira [Instalar o SQL Server 2012 do prompt de comando](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Use a mídia com suporte do SQL Server e a sua chave de licença para instalar o SQL Server na máquina virtual com o sistema operacional base (como exibido acima nas etapas 1 a 7).

Crie o farm do SharePoint usando as etapas a seguir:

Etapa 1. Configure a assinatura do Azure usando arquivos de script.

Etapa 2. Provisione os servidores do SharePoint criando outra máquina virtual com o sistema operacional base (como mostrado acima nas etapas 1 a 6). Para criar um servidor do SharePoint nessa máquina virtual, escolha uma das seguintes opções:

- Provisione usando a GUI do SharePoint:
	- Para criar e provisionar um farm do SharePoint, leia [Criar um farm de servidores do Microsoft SharePoint](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Para adicionar um servidor Web ou de aplicativos ao farm, leia [Adicionar um servidor Web ou de aplicativos ao farm](http://technet.microsoft.com/library/cc261752.aspx).
	- Para adicionar um servidor de banco de dados a um farm existente, leia [Adicionar um servidor de banco de dados a um farm existente](http://technet.microsoft.com/library/cc262781).
	- Para usar o SQL Server 2012 no seu farm do SharePoint, é necessário baixar e instalar o Service Pack 1 para o SharePoint Server 2010 depois de instalar o aplicativo e escolher não configurar o servidor. Para obter mais informações, consulte [Service Pack 1 para SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Para aproveitar os recursos do SQL Server BI, é recomendável instalar o SharePoint Server como um farm de servidores em vez de um servidor autônomo. Para saber mais, confira [Instalar os recursos de business intelligence do SQL Server 2012](http://technet.microsoft.com/library/hh231681.aspx).

- Provisionamento usando o Windows PowerShell: você pode usar a ferramenta de linha de comando Psconfig como uma interface alternativa para executar várias opções de controlam a forma como os produtos do SharePoint 2010 são provisionados. Para saber mais, confira [Referência da linha de comando Psconfig](http://technet.microsoft.com/library/cc263093.aspx).

Etapa 3. Configure o SharePoint. Depois que cada máquina virtual do SharePoint estiver no estado Pronto, configure o SharePoint Server em cada servidor usando as seguintes opções:

- Configure o SharePoint a partir da GUI.
- Configure o SharePoint usando o Windows PowerShell. Para saber mais, confira [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- Também é possível usar o AutoSPInstaller do CodePlex Project, que consiste em scripts do Windows PowerShell, um arquivo de entrada XML e um arquivo em lotes padrão do Microsoft Windows. O AutoSPInstaller fornece uma estrutura para um script de instalação do SharePoint 2010 baseada no Windows PowerShell. Para saber mais, confira [CodePlex: AutoSPInstaller (a página pode estar em inglês)](http://autospinstaller.codeplex.com/).

Etapa 4. Depois que o script for concluído, conecte-se à máquina virtual usando o Painel da Máquina Virtual.

Para verificar a configuração do SharePoint, entre no SharePoint Server e use a Administração Central.

> [AZURE.NOTE]Não deixe de configurar a segurança no ponto de extremidade do portal do Azure e de definir uma porta de dados de entrada no Firewall do Windows da máquina virtual. Em seguida, confirme que você pode iniciar uma sessão do Windows PowerShell para um dos servidores de aplicativo do SharePoint abrindo uma sessão do Windows PowerShell com credenciais de administrador.

### Criando e carregando um disco rígido virtual

Você também pode criar suas próprias imagens e carregá-las no Azure como um arquivo VHD. Para obter informações, confira [Criar e carregar um VHD do Windows Server no Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Cenários de uso

Esta seção aborda alguns cenários de cliente importantes para implantações do SharePoint usando as máquinas virtuais do Azure.

### Cenário 1: ambiente simples de desenvolvimento e teste do SharePoint

As organizações buscam maneiras mais rápidas para criar aplicativos do SharePoint e configurar ambientes do SharePoint para testes e desenvolvimentos dentro e fora do país. Essencialmente, elas buscam encurtar o tempo necessário para configurar os projetos de desenvolvimento de aplicativos do SharePoint e diminuir os custou aumentando o uso de seus ambientes de teste. Por exemplo, uma organização pode querer executar um teste de carga sob demanda no SharePoint Server e executar seu teste de aceitação do usuário (UAT) com mais usuários simultaneamente em diferentes localidades. De maneira similar, a integração de equipes dentro e fora do país é uma necessidade comercial cada vez mais importante para muitas organizações hoje em dia.

Este cenário explica como as organizações podem usar farms pré-configurados do SharePoint para desenvolvimento e cargas de trabalho de teste. A topologia de implantação do SharePoint é bastante semelhante a uma implantação local virtualizada. As habilidades de TI necessárias correspondem exatamente a 1:1 para a implantação das máquinas virtuais do Azure, com a grande vantagem de garantir praticamente uma mudança de custos altos a operacionais. Não há necessidade de comprar um servidor físico previamente. As organizações podem eliminar os altos gastos com hardware de servidor e obter flexibilidade reduzindo drasticamente o tempo de provisionamento necessário para criar, configurar ou expandir um farm do SharePoint para um ambiente de teste e desenvolvimento. A TI pode adicionar e remover dinamicamente a capacidade para atender a novas necessidades de teste e desenvolvimento. Além disso, a TI pode se concentrar mais em oferecer valor comercial com projetos do SharePoint e menos em gerenciar infraestruturas.

Para utilizar os computadores de carga e teste completamente, as organizações podem configurar a implantação virtualizada do SharePoint e testar os computadores no Azure com suporte de sistema operacional para o Windows Server 2008 R2. Isso permitirá que as equipes de desenvolvimento criem e testem aplicativos e migrem facilmente para ambientes locais ou de produção da nuvem sem necessidade de alterar códigos. As mesmas estruturas e conjuntos de ferramentas podem ser usados localmente e na nuvem, permitindo o acesso compartilhado da equipe no mesmo ambiente. Os usuários também podem acessar dados e aplicativos localmente estabelecendo uma conexão VPN direta.

A Figura 3 mostra uma implantação e um ambiente de teste do SharePoint na máquina virtual do Azure. Para criar essa implantação, comece usando localmente o mesmo ambiente de teste e desenvolvimento do SharePoint usado para desenvolver aplicativos. Em seguida, carregue e implante os aplicativos na máquina virtual do Azure para teste e desenvolvimento. Se a sua organização decidir retornar o aplicativo para o local físico, poderá fazê-lo sem precisar modificar o aplicativo.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

**Figura 3: ambiente de teste e desenvolvimento do SharePoint nas máquinas virtuais do Azure**

Para implementar um ambiente de teste e desenvolvimento do SharePoint no Azure, siga estas etapas:

1. Provisionar: primeiro, provisione uma conexão de VPN entre o local físico e o Azure usando a Rede Virtual do Azure. (Como o Active Directory não será usado aqui, um túnel VPN será necessário.) Para saber mais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md). Em seguida, use o portal do Azure para provisionar uma nova máquina virtual usando uma imagem de estoque da biblioteca de imagens.
	- Você pode carregar as máquinas virtuais de teste e desenvolvimento locais do SharePoint na sua conta de armazenamento do Azure e fazer referência a elas pela biblioteca de imagens para criar o ambiente necessário.
	- Você pode usar a imagem do SQL Server 2012 em vez da imagem do Windows Server 2008 R2 SP1. Para saber mais, confira [Provisionar uma máquina virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md).

2. Instalar: Instale o SharePoint Server, o Visual Studio e o SQL Server nas máquinas virtuais usando uma conexão de Área de Trabalho Remota.
	- Use o SharePoint 2010 Easy Setup Script para criar um computador de desenvolvedor do SharePoint. Para obter mais informações, consulte [SharePoint 2010 Easy Setup Script (a página pode estar em inglês)](http://www.microsoft.com/download/details.aspx?id=23415). Use o Windows PowerShell. Para saber mais, confira [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Use o AutoSPInstaller do CodePlex Project. Para saber mais, confira [CodePlex: AutoSPInstaller (a página pode estar em inglês)](http://autospinstaller.codeplex.com/).
	- Instalar Visual Studio. Para saber mais, confira [Instalação do Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Instale o SQL Server. Para saber mais, confira [Instalar o SQL Server usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Desenvolver pacotes de implantação e scripts para aplicativos e bancos de dados: se você planeja usar uma máquina virtual disponível da biblioteca de imagens, os aplicativos e bancos de dados locais desejados poderão ser implantados nas máquinas virtuais do Azure:
	- Crie pacotes de implantação para os aplicativos e bancos de dados locais usando o SQL Server Data Tools e o Visual Studio.
	- Use esses pacotes para implantar os aplicativos e bancos de dados nas máquinas virtuais do Azure.
4. Implantar aplicativos e bancos de dados do SharePoint:
	- Configure a segurança no ponto de extremidade do portal do Azure e defina uma porta de dados de entrada no Firewall do Windows da máquina virtual.
	- Implante aplicativos e bancos de dados do SharePoint nas máquinas virtuais do Azure usando os pacotes de implantação e os scripts criados na etapa 3.
- Teste os aplicativos e bancos de dados implantados.
5. Gerenciar máquinas virtuais:
	- Monitore as máquinas virtuais usando o portal do Azure.
	- Gerencie os aplicativos usando o Visual Studio e o SQL Server Management Studio.
	- Também é possível monitorar e gerenciar as máquinas virtuais usando softwares de gerenciamento locais, como o Microsoft System Center Operations Manager.

### Cenário 2: farm do SharePoint para o público com personalização

As organizações querem criar uma presença na Internet que seja hospedada na nuvem e que seja facilmente escalável com base na necessidade e na demanda. Também querem criar sites da extranet de parceiros para colaboração e implementar um processo fácil para criação distribuída e aprovação do conteúdo do site. Por fim, para lidar com o aumento de cargas, essas organizações querem fornecer capacidade sob demanda aos seus sites.

Nesse cenário, o SharePoint Server é usado como base para hospedar um site para o público. Ele permite que as organizações implantem, personalizem e hospedem rapidamente seus sites comerciais em uma estrutura de nuvem segura e escalável. Com os sites do SharePoint voltados para o público no Azure, as organizações podem dimensionar à medida que o tráfego aumenta e pagar somente pelo que usarem. Ferramentas comuns, semelhantes àquelas usadas localmente, podem ser usadas para criação de conteúdo, fluxo de trabalho e aprovação com o SharePoint no Azure.

Além disso, ao usar as Máquinas Virtuais do Azure, as organizações podem facilmente configurar a execução de ambientes de preparo e produção nas máquinas virtuais. As máquinas virtuais do SharePoint voltadas para o público criadas no Azure podem ser armazenadas em backup virtual. Além disso, para fins de recuperação de desastres, o recurso de Replicação Geográfica Contínua permite que as organizações façam backup automático das máquinas virtuais localizadas em uma central de dados para outra a quilômetros de distância.

As máquinas virtuais na infraestrutura do Azure são validadas e compatíveis com outros produtos Microsoft, como o SQL Server e o SharePoint Server. A união do Azure com o SharePoint Server faz a força: ambos são totalmente integrados, compatíveis e testados em conjunto para garantir a melhor experiência. Ambos tem um único ponto de suporte para o aplicativo SharePoint e para a infraestrutura do Azure.

Neste cenário, mais servidores front-end da Web para o SharePoint Server devem ser adicionados para dar maior suporte ao tráfego. Esses servidores exigem maior segurança e controles de domínio do Serviços de Domínio Active Directory para dar suporte à autenticação e autorização de usuários. A Figura 4 mostra o layout desse cenário.

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

**Figura 4: farm do SharePoint voltado para o público com personalização**

Para implementar um farm do SharePoint voltado para o público no Azure, siga estas etapas:

1. Implantar o Active Directory: os requisitos fundamentais para implantar o Active Directory nas máquinas virtuais do Azure são semelhantes, mas não idênticos, aos da implantação em máquinas virtuais (e, até certo ponto, máquinas físicas) no local. Para saber mais sobre as diferenças, diretrizes e outras considerações, acesse [Diretrizes para a implantação do Active Directory em máquinas virtuais do Azure](http://msdn.microsoft.com/library/jj156090). Para implantar o Active Directory no Azure:
	- Defina e crie uma rede virtual na qual as máquinas virtuais podem ser atribuídas a sub-redes específicas.
	- Use o portal do Azure para criar e implantar o controlador do domínio em uma nova máquina virtual no Azure. Você também pode consultar o script do Windows PowerShell para implantar um domínio autônomo na nuvem usando as Máquinas Virtuais e a Rede Virtual do Azure. Para saber mais sobre como criar uma nova floresta do Active Directory em uma máquina virtual na Rede Virtual do Azure, leia [Instalar uma nova floresta do Ative Directory no Azure](active-directory-new-forest-virtual-machine.md).
2. Provisione uma máquina virtual: use o portal do Azure para provisionar uma nova máquina virtual usando uma imagem de estoque da biblioteca de imagens.
3. Implantar um farm do SharePoint.
	- Use o portal do Azure para configurar o balanceamento de carga. Configure os pontos de extremidade da máquina virtual, selecione a opção para balancear a carga do tráfego em um ponto de extremidade existente e especifique o nome da máquina virtual com carga balanceada.
	- Adicione outra máquina virtual da Web de front-end ao farm do SharePoint existente para obter mais tráfego.
3. Gerenciar máquinas virtuais:
	- Monitore as máquinas virtuais usando o portal do Azure.
	- Monitore o farm do SharePoint usando a Administração Central.

### Cenário 3: farm expandido para serviços de BI adicionais

O business intelligence (BI) é essencial para obter informações importantes e tomar decisões rápidas e confiáveis. À medida que as organizações saem da abordagem local, elas não querem fazer mudanças no ambiente de BI ao implantar os aplicativos de BI existentes na nuvem. Elas querem hospedar relatórios do SQL Server Analysis Services (SSAS) ou do SQL Server Reporting Services (SSRS) em um ambiente disponível e altamente durável, enquanto mantêm controle total do aplicativo de BI sem gastar muito tempo e dinheiro em manutenção.

Este cenário descreve como as organizações podem usar as máquinas virtuais do Azure para hospedar aplicativos de BI essenciais. As organizações podem implantar farms do SharePoint em máquinas virtuais do Azure e expandir os componentes de BI da máquina virtual do servidor de aplicativos, como o SSRS ou os Serviços do Excel. Ao dimensionar componentes que consomem muitos recursos na nuvem, elas podem melhorar e facilitar o suporte a cargas de trabalho especializadas. O SQL Server nas máquinas virtuais do Azure funciona bem, e é fácil dimensionar instâncias do SQL Server, que variam de instalações de pequeno a grande porte. Isso fornece flexibilidade, permitindo que as organizações provisionem (expandam) ou desprovisionem (diminuam) instâncias de BI dinamicamente, com base em necessidades de carga de trabalho imediatas.

Migrar aplicativos de BI existentes para o Azure fornece melhor dimensionamento. Com a tecnologia do SQL Server Analysis Services (SSAS), do SQL Server Reporting Service (SSRS) e do SharePoint Server, as organizações podem criar aplicativos de BI e de relatório avançados, além de painéis que podem ser expandidos ou reduzidos. Esses aplicativos e painéis também podem ser integrados com mais segurança em dados e aplicativos locais. O Azure garante conformidade do data center com suporte para ISO 27001. Para saber mais, acesse a [Central de Confiabilidade do Azure](http://azure.microsoft.com/support/trust-center/compliance/).

Para expandir a implantação de componentes de BI, é necessário instalar um novo servidor de aplicativos, com serviços como PowerPivot, Power View, Serviços do Excel ou Serviços PerformancePoint. Ou as instâncias de BI do SQL Server como SSAS ou SSRS devem ser adicionadas no farm existente para oferecer suporte ao processamento de consultas adicional. O servidor pode ser adicionado como uma nova máquina virtual do Azure com o SharePoint 2010 Server ou o SQL Server instalado. Em seguida, os componentes de BI poderão ser instalados, implantados e configurados nesse servidor (Figura 5).

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

**Figura 5: farm do SharePoint reduzido para serviços de BI adicionais**

Para escalar horizontalmente um ambiente de BI no Azure, siga estas etapas:

1. Provisão:
	- Provisione uma conexão de VPN entre o local físico e o Azure usando a Rede Virtual do Azure. Para saber mais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).
	- Use o portal do Azure para provisionar uma nova máquina virtual usando uma imagem de estoque da biblioteca de imagens. Você pode carregar as imagens de carga de trabalho de BI do SharePoint Server ou do SQL Server à biblioteca de imagens, e qualquer usuário autorizado poderá escolher essas máquinas virtuais do componente de BI para criar o ambiente expandido.
2. Instalar: 
	- Se a sua organização não tiver imagens predefinidas dos componentes de BI do SharePoint Server ou do SQL Server, instale o SharePoint Server e o SQL Server nas máquinas virtuais usando uma conexão de Área de Trabalho Remota.
	- Para saber mais sobre como instalar o SharePoint, leia [Instalar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Para obter mais informações sobre como instalar o SQL Server, consulte [Instalar o SQL Server usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Adicionar a máquina virtual de BI:
	- Configure a segurança no ponto de extremidade do portal do Azure e defina uma porta de dados de entrada no Firewall do Windows da máquina virtual.
	- Adicione a máquina virtual de BI recém-criada ao farm do SharePoint ou do SQL Server.
4. Gerenciar máquinas virtuais:
	- Monitore as máquinas virtuais usando o portal do Azure.
	- Monitore o farm do SharePoint usando a Administração Central.
	- Monitore e gerencie as máquinas virtuais usando softwares de gerenciamento locais, como o Microsoft System Center Operations Manager.

### Cenário 4: site baseado no SharePoint totalmente personalizado

Cada vez mais, as empresas desejam criar sites de SharePoint totalmente personalizados na nuvem. Elas necessitam de um ambiente disponível e altamente durável que ofereça controle total para manter os aplicativos complexos em execução na nuvem, mas não desejam gastar tempo e orçamento em excesso.

Nesse cenário, uma organização pode implantar todo o seu farm do SharePoint na nuvem e dimensionar dinamicamente todos os componentes para obter mais capacidade ou expandir sua implantação local para a nuvem para aumentar a capacidade e melhorar o desempenho quando necessário. O cenário aborda as organizações que desejam ter uma experiência do SharePoint completa para o desenvolvimento de aplicativos e gerenciamento de conteúdo empresarial. Os sites mais complexos também podem incluir relatórios aprimorados, Power View, PerformancePoint, PowerPivot, gráficos detalhados e a maioria dos demais recursos de sites do SharePoint para obter todas as funcionalidades disponíveis.

As organizações podem usar as máquinas virtuais do Azure para hospedar aplicativos personalizados e componentes associados em uma infraestrutura de nuvem econômica e altamente segura. Elas também podem usar o Microsoft System Center local como uma ferramenta de gerenciamento comum para aplicativos locais e na nuvem.

Para implantar um site do SharePoint totalmente personalizável no Azure, a organização deve implantar um domínio do Active Directory na nuvem e provisionar novas máquinas virtuais nesse domínio. Em seguida, uma máquina virtual com SQL Server 2012 deve ser criada e configurada como parte de um farm do SharePoint. Por fim, o farm do SharePoint deve ser criado, balanceado e conectado ao Active Directory e ao SQL Server (Figura 6).

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

**Figura 6: site baseado no SharePoint totalmente personalizado**

As etapas a seguir mostram como criar um ambiente de farm do SharePoint personalizado de imagens predefinidas disponíveis na biblioteca de imagens. No entanto, também é possível carregar as máquinas virtuais do farm do SharePoint na biblioteca de imagens. Dessa forma, os usuários autorizados podem escolher essas máquinas virtuais para criar o farm do SharePoint necessário no Azure.

1. Implantar o Active Directory: os requisitos fundamentais para implantar o Active Directory nas máquinas virtuais do Azure são semelhantes, mas não idênticos, aos da implantação em máquinas virtuais (e, até certo ponto, máquinas físicas) no local. Para saber mais sobre as diferenças, diretrizes e outras considerações, acesse [Diretrizes para a implantação do Active Directory em máquinas virtuais do Azure](http://msdn.microsoft.com/library/jj156090). Para implantar o Active Directory no Azure:
	- Defina e crie uma rede virtual na qual as máquinas virtuais podem ser atribuídas a sub-redes específicas.
	- Use o Portal do Azure para criar e implantar o controlador do domínio em uma nova máquina virtual no Azure.
	- Para saber mais sobre como criar uma nova floresta do Active Directory em uma máquina virtual na Rede Virtual do Azure, leia [Instalar uma nova floresta do Ative Directory no Azure](active-directory-new-forest-virtual-machine).
2. Implantar o SQL Server:
	- Use o portal do Azure para provisionar uma nova máquina virtual usando uma imagem de estoque da biblioteca de imagens.
	- Configure o SQL Server na máquina virtual. Para saber mais, confira [Instalar o SQL Server usando SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Adicione a máquina virtual ao domínio do Active Directory recém-criado.
3. Implantar um farm de multisservidor do SharePoint:
	- Crie uma rede virtual. Para saber mais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).
	- Ao implantar as máquinas virtuais do SharePoint, você precisará das sub-redes fornecidas para o SharePoint Server. Dessa forma, os endereços de DNS na caixa do Active Directory local estarão disponíveis durante o provisionamento.
	- Use o portal do Azure para criar uma máquina virtual.
	- Instale o SharePoint Server nessa máquina virtual e gere uma imagem reutilizável. Para saber mais sobre como instalar o SharePoint Server, leia [Instalar e configurar o SharePoint Server 2010 usando o Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) ou [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Configure a máquina virtual do SharePoint para criar e conectar-se ao farm do SharePoint usando o comando [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx).
	- Use o portal do Azure para configurar o balanceamento de carga: configure os pontos de extremidade da máquina virtual, selecione a opção para balancear a carga do tráfego em um ponto de extremidade existente e especifique o nome da máquina virtual com carga balanceada.
4. Gerencie o farm do SharePoint através do System Center:
	- Use o agente do Operations Manager e o novo Pacote de Integração do Azure para conectar o seu System Center local às máquinas virtuais do Azure.
	- Use o App Controller e o Orchestrator local para funções de gerenciamento.

## Resumo

As máquinas virtuais do Azure fornecem uma série completa de implantações para o SharePoint. Elas foram testadas e são totalmente compatíveis com outros produtos Microsoft para oferecer a melhor experiência possível. Dessa forma, as empresas podem facilmente configurar e implantar o SharePoint Server no Azure, seja para provisionar a infraestrutura de uma nova implantação do SharePoint ou para expandir a atual. À medida que as cargas de trabalho aumentam, as organizações podem expandir suas infraestruturas do SharePoint rapidamente. Da mesma forma, se houver necessidade de reduzir a carga de trabalho, as organizações podem contratar recursos sob demanda, pagando apenas pelo que usarem. As máquinas virtuais do Azure fornecem uma infraestrutura excepcional para uma grande variedade de requisitos de negócios, como é mostrado nos cenários do SharePoint discutidos neste artigo.

Uma implantação bem-sucedida do SharePoint Server nas máquinas virtuais do Azure exigem um planejamento sólido, especialmente se considerarmos as variedades de opções de arquitetura de farms essenciais e as opções de implantação. As informações e as práticas recomendadas descritas neste artigo podem ajudar a orientar as decisões de implementação e de implantação do SharePoint.

## Recursos adicionais

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint na Intranet](virtual-machines-workload-intranet-sharepoint-farm)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Testar o SharePoint Server 2016 IT Preview no Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)

<!---HONumber=Oct15_HO3-->