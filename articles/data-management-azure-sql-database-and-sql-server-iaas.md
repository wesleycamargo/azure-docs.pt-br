<properties 
	pageTitle="Noções básicas sobre o Banco de Dados SQL do Azure e SQL Server em máquinas virtuais do Azure" 
	description="Aprenda sobre o banco de dados SQL do Azure e SQL Server em máquinas virtuais do Azure. Revise comuns motivadores comerciais para determinar qual tecnologia SQL funciona melhor para seu aplicativo." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="selcint"/>

# Noções básicas sobre o Banco de Dados SQL do Azure e SQL Server em máquinas virtuais do Azure

O Microsoft Azure fornece duas opções ao hospedar seus dados baseados no SQL Server: **Banco de Dados SQL do Azure** e **SQL Server na máquina virtual do Azure**. Neste artigo, vamos começar examinando como cada opção se ajusta ao panorama na Plataforma de Dados da Microsoft e, em seguida, vamos para discussões mais profundas com base nos requisitos de negócios que motivam sua escolha. Se você priorizar economia ou administração mínima à frente de tudo, este artigo pode ajudá-lo a decidir qual abordagem é a melhor, com base em como cada uma delas cumpre os requisitos de negócios com os quais você se preocupa mais.

- [Plataforma de Dados da Microsoft](#platform)
- [Uma análise mais profunda do Banco de Dados SQL do Azure e SQL Server na VM do Azure](#close)	
- [Motivações de negócios ao escolher o Banco de Dados SQL do Azure ou o SQL Server na VM do Azure](#business)	
	- [Custo](#cost)
		- [Noções básicas de licenciamento e cobrança](#billing)	
		- [Calculando o custo total do aplicativo](#appcost)	
	- [Administração](#admin)	
	- [Contrato de nível de serviço (SLA)](#sla)	
	- [Tempo para colocação no mercado](#market)	
- [Resumo](#summary)	
- [Confirmações](#ack)	
- [Recursos adicionais](#resources)	


##<a name="platform"></a>Plataforma de Dados da Microsoft

Uma das primeiras coisas a compreender em qualquer discussão do Azure versus bancos de dados local do SQL Server é que você pode usá-lo. A Plataforma de Dados da Microsoft aproveita a tecnologia do SQL Server e o torna disponível em computadores locais físicos, ambientes de nuvem privada, ambientes de nuvem privada hospedados de terceiros e nuvem pública. Isso permite que você atenda a necessidades comerciais exclusivas e diversificadas através de uma combinação de implantações locais e hospedadas em nuvem, ao usar o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e experiência nesses ambientes.

   ![][1]

Como visto no diagrama, cada oferta pode ser caracterizada por nível de administração que você tem sobre a infraestrutura (no eixo X) e pelo grau de economia obtida com a consolidação de nível de banco de dados e automação (no eixo Y).

Ao criar um aplicativo, quatro opções básicas estão disponíveis para hospedar a parte do SQL Server do aplicativo: 

- SQL Server em computadores físicos não virtualizados 
- SQL Server em computadores virtualizados locais (nuvem privada)
- SQL Server na Máquina Virtual do Azure (nuvem pública)
- Banco de Dados SQL do Azure (nuvem pública)

Nas seções a seguir, aprenderemos sobre os dois últimos: Banco de Dados SQL do Azure e SQL Server em VMs do Azure. Além disso, iremos explorar motivadores comerciais comuns para determinar qual opção funciona melhor para seu aplicativo.

##<a name="close"></a>Uma análise mais profunda do Banco de Dados SQL do Azure e SQL Server na VM do Azure

**Microsoft Azure SQL Database (Banco de dados SQL do Azure)** é um banco de dados como serviço relacional, que se encaixa na categoria do setor *Platform as a Service (PaaS)*. O Banco de Dados SQL do Azure baseia-se em hardware e software padronizados que são de propriedade, hospedados e mantidos pela Microsoft. Com o Banco de Dados SQL, você pode desenvolver diretamente no serviço usando a funcionalidade e recursos internos. Ao usar o Banco de Dados SQL, você paga pelo uso com opções para escalar verticalmente ou horizontalmente para ter maior capacidade.

**O SQL Server na Máquina Virtual do Azure (VM)** se enquadra na categoria do setor *Infrastructure as a Service (IaaS)* e permite executar o SQL Server dentro de uma máquina virtual na nuvem. Semelhante ao Banco de Dados SQL do Azure, ele se baseia no hardware padronizado que é de propriedade, hospedado e mantido pela Microsoft. Ao usar o SQL Server em uma VM, você pode colocar sua própria licença do SQL Server no Azure ou usar uma das imagens pré-configuradas do SQL Server no portal do Azure.

Em geral, essas duas opções de SQL são otimizadas para finalidades diferentes:

- **O Banco de Dados SQL do Azure** é otimizado para reduzir os custos gerais ao mínimo para provisionar e gerenciar vários bancos de dados. Ela reduz os custos de administração contínua porque você não precisa gerenciar nenhuma máquina virtual, sistema operacional ou software de banco de dados, incluindo atualizações, alta disponibilidade e backups. Em geral, o Banco de Dados SQL pode aumentar drasticamente o número de bancos de dados gerenciados por um único recurso de TI ou desenvolvimento.
- **O SQL Server em execução na VM do Azure** é otimizado para estender aplicativos existentes do SQL Server local no Azure em um cenário híbrido ou implantar um aplicativo existente no Azure em um cenário de migração ou no cenário de desenvolvimento e teste. Um exemplo de cenário híbrido é manter réplicas de banco de dados secundário no Azure via [Rede Virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx). Com o SQL Server em VMs do Azure, você tem os direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Com o SQL Server na VM, você pode criar um sistema altamente personalizado para lidar com requisitos específicos de desempenho e disponibilidade do aplicativo.

A tabela a seguir resume as principais características do Banco de Dados SQL do Azure e SQL Server na VM do Azure:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Banco de Dados SQL Azure</th>
   <th align="left" valign="middle">SQL Server na VM do Azure</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>Melhor para</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Novos aplicativos baseados em nuvem que têm restrições de tempo no desenvolvimento e marketing. 
          <li type=round>Aplicativos que precisam de alta disponibilidade automática interna, solução de recuperação de desastres e mecanismos de atualização.
          <li type=round>Se você tiver centenas ou milhares de bancos de dados, mas não deseja gerenciar o sistema operacional subjacente, hardware e as definições de configuração. 
         <li type=round>Aplicativos que usam padrões de expansão.
         <li type=round>Bancos de dados de até 500 GB de tamanho.
         <li type=round>Criação de aplicativos de Software como Serviço.
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Aplicativos existentes que requerem migração rápida para a nuvem com alterações mínimas.
      <li type=round>Aplicativos do SQL Server que exigem acesso a recursos locais (como o Active Directory) do Azure por meio de um túnel seguro. 
      <li type=round>Se você precisar de um ambiente de TI personalizado com direitos administrativos completos.
      <li type=round>Cenários de rápido desenvolvimento e teste quando você não deseja comprar hardware do SQL Server sem produção local.
      <li type=round>Recuperação de desastres para aplicativos do SQL Server no local usando <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup no armazenamento do Azure</a> ou <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Réplicas do AlwaysOn em VMs do Azure</a>.
      <li type=round>Grandes bancos de dados maiores que 1 TB em tamanho.
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Recursos</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>Você não deseja empregar recursos de TI para suporte e manutenção da infraestrutura subjacente.
       <li type=round>Você deseja se concentrar na camada do aplicativo.
       </ul></td>
   <td valign="middle"><ul><li type=round>Você tem recursos de TI para manutenção e suporte.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Custo total de propriedade</b></p></td>
   <td valign="middle"><ul><li type=round>Elimina os custos de hardware. Reduz os custos administrativos.</ul></td>
   <td valign="middle"><ul><li type=round>Elimina os custos de hardware. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Continuidade dos negócios</b></p></td>
   <td valign="middle"><ul><li type=round>Além dos recursos internos de infraestrutura de tolerância a falhas, o Banco de Dados SQL do Azure fornece recursos como a Restauração Pontual, Restauração Geográfica e Replicação Geográfica para aumentar a continuidade dos negócios. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Continuidade dos negócios no Banco de dados SQL do Azure</a>.</ul></td>
   <td valign="middle"><ul><li type=round>O SQL Server na VM do Azure permite que você configure uma solução de alta disponibilidade e recuperação de desastres para as necessidades específicas do seu banco de dados. Portanto, você pode ter um sistema altamente otimizado para seu aplicativo. Você pode testar e executar failovers por conta própria, quando necessário. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure</a>.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Nuvem híbrida</b></p></td>
   <td valign="middle"><ul><li type=round>Seu aplicativo local pode acessar dados no Banco de Dados SQL do Azure.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Com o SQL Server em VMs do Azure, você pode ter aplicativos que executam parcialmente na nuvem e parcialmente localmente. Por exemplo, você pode estender a rede local e o diretório de domínio ativo para a nuvem por meio dos <a href="http://msdn.microsoft.com/library/azure/gg433091.aspx">Serviços de Rede do Azure</a>. Além disso, você pode armazenar arquivos de dados locais no armazenamento do Azure usando os <a href="http://msdn.microsoft.com/library/dn385720.aspx">Arquivos de dados do SQL Server no recurso do Azure</a>. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/dn606154.aspx">Introdução à nuvem híbrida do SQL Server 2014</a>.
      <li type=round>Oferece suporte à recuperação de desastres para aplicativos do SQL Server no local  usando  <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup no armazenamento do Azure</a> ou <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Réplicas do AlwaysOn em VMs do Azure</a>.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Motivações de negócios ao escolher o Banco de Dados SQL do Azure ou o SQL Server na VM do Azure

###<a name="cost"></a>Custo

Seja você uma startup sem recursos financeiros ou uma equipe em uma empresa estabelecida que opera com restrições de orçamento apertado, financiamento limitado costuma ser o principal motivador ao decidir como hospedar os bancos de dados. Nesta seção, primeiro aprenderemos sobre noções básicas de cobrança e licenciamento no Azure com relação a essas duas opções de banco de dados relacional: Banco de Dados SQL do Azure e SQL Server na VM do Azure. Em seguida, vamos ver como devemos calcular o custo total do aplicativo.

####<a name="billing"></a>Noções básicas de licenciamento e cobrança

**O Banco de Dados SQL do Azure** é vendido a clientes como um serviço, não com uma licença, enquanto o SQL Server na VM do Azure exige licenciamento tradicional do SQL Server. 

Atualmente, o **Banco de Dados SQL do Azure** está disponível em várias camadas de serviço. Para as camadas de serviço Basic, Standard e Premium, você será cobrado por hora a uma taxa fixa com base na camada de serviço e no nível de desempenho que você escolher. As camadas de serviço Basic, Standard e Premium são projetadas para oferecer um desempenho previsível com vários níveis de desempenho para atender às necessidades de pico do seu aplicativo. Você pode alterar entre as camadas de serviço e os níveis de desempenho para atender às necessidades de produtividade variadas do seu aplicativo. Para obter as informações mais recentes sobre as camadas de serviço com suporte atuais, consulte [Camadas de serviço do Banco de Dados SQL do Azure (Edições)](http://msdn.microsoft.com/library/azure/dn741340.aspx).

Com o **Banco de Dados SQL do Azure**, o software de banco de dados é configurado automaticamente, corrigido e atualizado pelo Microsoft Azure nos data centers em todo o mundo. Portanto, você obtém redução nos custos de administração. Além disso, seus [recursos internos de backup](http://msdn.microsoft.com/library/azure/jj650016.aspx) ajudarão a obter economia significativa, especialmente quando você tem grande número de bancos de dados. Ao usar o Banco de Dados SQL do Azure, você não será cobrado por consultas individuais em execução no Banco de Dados SQL do Azure ou tráfego de Internet de entrada/saída. Se seu banco de dados tiver alto volume transacional e a necessidade de dar suporte a muitos usuários simultâneos, é recomendável que você use as camadas de serviço Premium, em vez de Basic ou Standard. 

Com o **SQL Server na VM do Azure**, você utiliza o licenciamento tradicional do SQL Server. Você pode usar a imagem do SQL Server fornecida pela plataforma ou colocar sua licença do SQL Server no Azure. Ao usar imagens do SQL Server fornecidas pela plataforma, o custo depende do tamanho da VM, bem como a versão do SQL Server que você escolher. Basicamente, você paga por minuto o custo de licenciamento do SQL Server, o licenciamento por minuto do Windows Server e o custo de armazenamento do Azure. A opção de cobrança por minuto permite que você use o SQL Server pelo tempo necessário sem adquirir licença completa do SQL Server. Se você colocar sua própria licença do SQL Server no Azure, você será cobrado apenas pelos custos de computação e armazenamento do Azure. Para obter mais informações, consulte [Mobilidade de licenças por meio do Software Assurance no Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Calculando o custo total do aplicativo

Quando você começar a usar uma plataforma de nuvem, o custo da execução do seu aplicativo principalmente inclui os custos de desenvolvimento e administração; e também os custos de serviço exigidos pela plataforma de nuvem pública.

Aqui está o cálculo do custo detalhado para seu aplicativo em execução no Banco de Dados SQL do Azure e SQL Server na VM do Azure:

**Ao usar o Banco de Dados SQL do Azure:**

*Custo total do aplicativo = custos administrativos altamente minimizados + custos de desenvolvimento de software + custos de serviço de Banco de Dados SQL do Azure*

**Ao usar o SQL Server na VM do Azure:**

*Custo total do aplicativo = custos minimizados de desenvolvimento/modificação do software + custos de administração + custos de licenciamento do SQL Server e Windows Server + custos do Armazenamento do Azure* 

**Observação importante:** atualmente, o Banco de dados SQL do Azure não oferece suporte a todos os recursos do SQL Server. Para obter informações detalhadas de comparação, consulte [Diretrizes e limitações do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ff394102.aspx). Lembre-se disso quando desejar mover um banco de dados existente para o Banco de dados SQL do Azure, já que talvez seja necessário algum orçamento adicional para reprojetar o banco de dados. O Banco de dados SQL do Azure é uma oferta de plataforma como serviço da Microsoft. Quando você migra um aplicativo existente do SQL Server local para o Banco de dados SQL do Azure, recomendamos que você atualize o aplicativo para obter todas as vantagens da oferta de plataforma como serviço. Por exemplo, comece usando o [Sites do Azure](http://azure.microsoft.com/documentation/services/websites/) ou [Serviços de Nuvem do Azure](http://azure.microsoft.com/services/cloud-services/) na camada de aplicativo para aumentar a relação custo/benefício. Além disso, valide seu aplicativo em diferentes níveis de serviço de banco de dados SQL e verifique qual deles adequa-se melhor às necessidades do seu aplicativo. Esse processo ajuda você a obter melhores resultados de desempenho e custos minimizados. Para obter mais informações, consulte [Níveis de desempenho e camadas de serviço do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx).

Para obter uma estimativa de custo detalhada, use a [Calculadora de Preços do Azure](http://azure.microsoft.com/pricing/calculator/). 

Para obter mais informações sobre preços, consulte os recursos a seguir:

- [Detalhes de preços do Banco de Dados SQL do Azure](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Detalhes de preços de máquina virtual](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [SQL Server em VMs do Azure - Detalhes de preços](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server em VMs do Azure - Detalhes de preços](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>Administração

Se suas mãos já estão repletas de muitas tarefas, talvez assumir a administração do servidor e do banco de dados não seja algo que você esteja ansioso para fazer. Para muitas empresas, a decisão de usar um serviço de nuvem significa a capacidade de descarregar a complexidade da administração. Com o **Banco de Dados SQL do Azure**, a Microsoft administra o hardware físico, como discos rígidos, servidores e armazenamento; replica automaticamente todos os dados para fornecer alta disponibilidade; configura e atualiza o software de banco de dados; gerencia o balanceamento de carga; além disso faz o failover transparente, se houver uma falha no servidor. Você pode continuar a administrar suas instâncias de Banco de Dados SQL do Azure, mas sem controlar os recursos físicos da instância do SQL Server subjacente e da plataforma do Azure.  Por exemplo, você pode administrar logons e bancos de dados, ajustar o índice e otimizar consultas, mas não pode administrar tabelas do sistema nem o gerenciamento de grupo de arquivos. Para obter mais informações, consulte [Diretrizes e limitações do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/ff394102.aspx). 

Por outro lado, você pode ter conhecimento na empresa e desejo de manter controle sobre o local do banco de dados para o próprio computador. Com o **SQL Server em execução na VM do Azure**, você tem controle total sobre o sistema operacional e a configuração da instância do SQL Server. Em uma VM, cabe a você decidir quando atualizar o sistema operacional e o software de banco de dados e quando instalar algum software adicional, como ferramentas de backup e antivírus. Além disso, você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos.  Por exemplo, o Azure permite alterar o tamanho de uma VM em execução, conforme necessário. Para obter informações, consulte [Tamanhos de máquinas virtuais e serviço de nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

###<a name="sla"></a>Contrato de nível de serviço (SLA)

Para alguns de nós, atender às obrigações de tempo de um contrato de nível de serviço (SLA) é a principal prioridade. Nesta seção, vamos examinar o que significa SLA para cada opção de hospedagem do banco de dados.

Para o **Banco de Dados SQL do Azure**, considerando as camadas de serviço Basic, Standard e Premium, a Microsoft fornece um SLA com 99,99% de disponibilidade.  Observe que a disponibilidade do SLA aborda a capacidade de se conectar ao banco de dados. Em outras palavras, é um SLA de nível de banco de dados. Para obter as informações mais recentes sobre SLAs, consulte o [Contrato de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/). Para obter as informações mais recentes sobre as Camadas de serviço de Banco de Dados SQL do Azure (Edições) e os planos de continuidade dos negócios com suporte, consulte as [Camadas de serviço do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn741340.aspx).

Para **Máquinas virtuais hospedadas no Azure**, a Microsoft fornece um SLA de 99,95% de disponibilidade, e essa disponibilidade serve para a VM, não para os processos em execução dentro da VM (por exemplo, SQL Server). O [SLA de VM](http://www.microsoft.com/download/details.aspx?id=38427) exige que você hospede pelo menos duas VMs em um conjunto de disponibilidade. Com essa configuração, o Azure garante que pelo menos uma das VMs estará disponível 99,95% do tempo.  Para o banco de dados alta disponibilidade (HA) em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como grupos de disponibilidade AlwaysOn. Observe que a configuração AlwaysOn no Azure requer o gerenciamento e a configuração manuais, e você paga um adicional para cada banco de dados secundário que opera.


###<a name="market"></a>Tempo para colocação no mercado

**O Banco de Dados SQL do Azure SQL** é a solução ideal para aplicativos baseados em nuvem quando a produtividade do desenvolvedor e o tempo de colocação no mercado rápido são essenciais. Com a funcionalidade de programação como DBA, ele é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes. Ele ajuda os desenvolvedores a compreender e configurar tarefas relacionadas ao banco de dados. Por exemplo, você pode usar os cmdlets de [API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) e do [PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar e gerenciar operações administrativas para milhares de bancos de dados. Com a escala elástica na nuvem, você pode facilmente se concentrar na camada de aplicativo e colocar seu aplicativo no mercado mais rapidamente. 

**O SQL Server em execução na VM do Azure** é perfeito se os aplicativos novos e existentes exigirem acesso e controle para todos os recursos de uma instância do SQL Server, e também para quando você deseja migrar aplicativos locais e bancos de dados existentes para a nuvem no estado em que se encontram. Já que você não precisa alterar a apresentação, o aplicativo e as camadas de dados, você economiza tempo e orçamento em rearquitetura da solução existente. Em vez disso, você pode se concentrar na migração de todos os pacotes de solução para as VMs e fazer algumas otimizações de desempenho exigidas pela plataforma do Azure. Para obter informações, consulte [Práticas recomendadas de desempenho do SQL Server em máquinas virtuais do Azure](http://msdn.microsoft.com/library/azure/dn133149.aspx).

##<a name="summary"></a>Resumo

Neste artigo, exploramos: Banco de Dados SQL do Azure e SQL Server na VM do Azure. Além disso, discutimos motivadores comerciais comuns que podem afetar a tomada de decisão sobre qual escolher.  

Este é um resumo de sugestões a serem consideradas quando usar um ou outro:

Escolha o **Banco de Dados SQL do Azure**, se:

- Você estiver criando aplicativos totalmente novos baseados em nuvem; ou você desejar migrar seu banco de dados existente do SQL Server para o Azure e seu banco de dados não estiver usando uma das funcionalidades sem suporte no Banco de Dados SQL do Azure. Para obter mais informações, consulte [Referência Transact-SQL do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). Essa abordagem oferece os benefícios de um serviço de nuvem totalmente gerenciado e garante que o tempo de colocação no mercado seja rápido.

- Você deseja que a Microsoft execute operações de gerenciamento comuns em seus bancos de dados e exija SLAs de maior disponibilidade para bancos de dados. Essa abordagem pode minimizar os custos de administração e ao mesmo tempo fornece uma disponibilidade garantida do banco de dados. 

Escolha **SQL Server na VM do Azure**, se:

- Você tiver aplicativos locais existentes e desejar manter seu próprio hardware ou considerar soluções híbridas. Essa abordagem permite que você obtenha acesso a alta capacidade de banco de dados mais rapidamente e também conecta seus aplicativos locais à nuvem por meio de um túnel seguro.

- Você tiver recursos de TI existentes, precisar de plenos direitos administrativos sobre o SQL Server e exigir a compatibilidade total com o SQL Server local (por exemplo, alguns recursos não existem no Banco de Dados SQL do Azure). Essa abordagem permite minimizar os custos de desenvolvimento ou modificações de aplicativos existentes com a flexibilidade para executar a maioria dos aplicativos. Além disso, ela fornece controle total sobre a VM, o sistema operacional e a configuração de banco de dados.

##<a name="ack"></a>Confirmações

Este artigo do grupo Microsoft Cloud e Enterprise Content Services foi produzido com a ajuda de muitas pessoas dentro da comunidade Microsoft.

**Autor:** Selcin Turkarslan

**Colaboradores técnicos:** Conor Cunningham

**Revisores técnicos:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Revisores editoriais:** Heidi Steen, Maggie Sparkman.

Obrigado a todos por dar vida a este artigo!

##<a name="resources"></a>Recursos adicionais 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Descrição</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Banco de Dados SQL Azure</a></p>
<p><a href="http://msdn.microsoft.com/library/azure/jj823132.aspx">MSDN: SQL Server nas Máquinas Virtuais do Azure</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: Banco de Dados SQL Azure</a></p></td>
   <td valign="middle">Links para a documentação da biblioteca.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj879332.aspx">Banco de Dados SQL do Azure e SQL Server - Comparação e contraste de desempenho e escalabilidade</p></td>
   <td valign="middle">Este artigo explica as diferenças de desempenho e as técnicas de solução de problemas ao usar o Banco de Dados SQL do Azure e o SQL Server em execução localmente ou em uma VM. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/dn574746.aspx">Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure</p></td>
   <td valign="middle">Este artigo descreve os padrões de aplicativo mais comuns que se aplicam ao SQL Server em VMs do Azure e também cenários híbridos incluindo Banco de Dados SQL do Azure. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Bloco de aplicativos de manipulação de falhas transitórias da Biblioteca Empresarial da Microsoft</p></td>
   <td valign="middle">Esta biblioteca permite aos desenvolvedores tornar aplicativos em execução no Banco de Dados SQL do Azure mais flexíveis, adicionando lógica robusta de manipulação de falhas transitórias. Falhas transitórias são erros que ocorrem devido a alguma condição temporária como problemas de conectividade de rede ou indisponibilidade do serviço. Uma vez que o Banco de Dados SQL do Azure é um serviço multilocatário, é importante tratar esses erros para minimizar qualquer tempo de inatividade do aplicativo. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png


<!--HONumber=42-->
