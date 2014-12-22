<properties urlDisplayName="Azure Websites Enterprise Offerings" pageTitle="Migrar os sites do IIS para Sites do Azure usando o Assistente de Migração" metaKeywords="Azure Websites,solution" description="Shows how to use Azure Websites create enterprise website solutions for your business" metaCanonical="" services="web-sites" documentationCenter="" title="Azure Websites Offerings for Enterprise Whitepaper" authors="cephalin,Apurva.Joshi"  solutions="" writer="cephalin" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="cephalin" />

# Ofertas de Sites do Azure para white paper do Enterprise #

A necessidade de reduzir custos e fornecer soluções de TI mais rápidas em um ambiente em rápida evolução cria novos desafios para desenvolvedores, profissionais de TI e gerentes. Os usuários estão aumentando, procurando por seus aplicativos da web de linha de negócios (LOB) para serem rápidos, responsivos e terem disponibilidade de qualquer dispositivo. Ao mesmo tempo, as empresas estão tentando aproveitar o aumento da produtividade e da eficiência da integração com a nuvem e os serviços móveis. Isso pode ser algo simples, como logon único em todos os dispositivos usando o Active Directory para colaboração em Office365 usando dados extraídos de um aplicativo LOB interno que, por sua vez, recebe dados da implementação da Salesforce. [Sites do Microsoft Azure](http://azure.microsoft.com/pt-br/services/websites/) é um serviço de nuvem de classe empresarial para desenvolver, testar e executar aplicativos móveis e da Web, APIs da Web e sites genéricos. Ele pode ser usado para executar sites corporativos, aplicativos de negócios e campanhas de marketing digital em uma rede global de datacenters otimizados para escala e disponibilidade, além de suporte à integração contínua e práticas modernas de DevOps. 

Este white paper destaca os recursos do serviço Sites do Microsoft Azure especificamente voltados para executar aplicativos da Web de LOB, abordando a migração de aplicativos web existentes e a implantação de novos aplicativos de LOB da web na plataforma. 

## Público-alvo ##

Os profissionais de TI, arquitetos e gerentes que desejam migrar para as cargas de trabalho da Web na nuvem que estão sendo executados localmente. Cargas de trabalho da Web podem abranger da empresa ao funcionário ou de negócios a aplicativos da Web de parceiros.

## Introdução ##

O Sites do Microsoft Azure é uma plataforma ideal para hospedar aplicativos da web internos e externos e serviços, pois ele fornece uma solução econômica, altamente escalonável e gerenciada, permitindo que você se concentre no fornecimento de valor de negócios para os usuários em vez de gastar uma quantidade significativa de tempo e dinheiro mantendo e dando suporte a ambientes separados. O Sites do Microsoft Azure oferece uma plataforma flexível para implantar seus aplicativos Web corporativos, oferecendo a capacidade de continuar a autenticar no Active Directory local por meio da integração com o Active Directory do Microsoft Azure, suporte de implantações rápidas e fáceis, fazendo uso de suas práticas de implantação e integração contínuas e internas, enquanto é dimensionado automaticamente para crescer de acordo com as necessidades da empresa. Tudo em uma plataforma gerenciada que permite que você se concentre em seu aplicativo e não na infraestrutura. 

## Definição do problema ##

O cenário de TI está mudando rapidamente com uma migração da hospedagem em servidores tradicionais com seus custos de capital altos nos tempos de entrega a longo prazo para uma que utiliza o uso sob demanda de serviços que é dimensionado automaticamente para tratar da carga. Os departamentos de TI estão sendo desafiados a reduzir os custos e requisitos de infraestrutura e de manutenção gastos com foco na redução de CAPEX enquanto aumentam a agilidade. O fim da vida útil eminente de plataformas mais antigas de infraestrutura, como o Windows Server 2003, está levando os departamentos de TI a revisar a migração em nuvem como uma possível maneira de evitar os custos de capital novo de longo prazo. No passado, os CIOs faziam as decisões de compra para outros departamentos, porém, cada vez mais, os CMOs e outros diretores de unidade de negócios estão adotando um papel mais ativo em como seu orçamento é gasto e qual é o retorno sobre o investimento. Cada vez mais empresas precisam que sua força de trabalho tenha muito mais mobilidade com funcionários trabalhando remotamente, passando mais tempo com clientes que precisam de acesso a sistemas sem complicações.

Necessidades dos negócios mudam mensalmente, semanalmente, diariamente. As empresas estão buscando escala global instantânea com serviços atualizados regulares e completos dos novos recursos, fornecidos por terceiros ou internamente. Os usuários têm expectativas maiores, com muitos fazendo uso de serviços em suas próprias vidas particulares, como o Office365. Eles esperam ter acesso a serviços com muitos recursos semelhantes e atualizados em sua rotina de trabalho. Para lidar com essa demanda, o departamento de TI deve ajudar os negócios para permitir isso através da seleção e integração com serviços de terceiros, a seleção cuidadosa de plataformas que podem se adaptar às necessidades comerciais e, ao mesmo tempo, confiáveis e com um menor custo total de propriedade.

As equipes de desenvolvimento estão procurando oferecer vantagem comercial imediata, oferecendo novos recursos com frequência. Elas estão procurando uma plataforma confiável e econômica que se integra a suas ferramentas e práticas existentes - desenvolvimento, teste, versão; e trabalhar em conjunto com os departamentos de TI automatiza a implantação, gerenciamento e alertas, tudo isso com o objetivo de tempo de inatividade zero.

<a href="highlevel" />
## Solução de alto nível ##

Estruturas e plataformas da web estão cada vez mais sendo usadas para desenvolver, testar e hospedar aplicativos de linha de negócios.  Com uma linha comum de aplicativo de negócios, como um sistema de despesas de funcionários internos, geralmente consistindo de apenas um site com um banco de dados de backup para armazenar os dados conectados ao aplicativo.

O Sites do Microsoft Azure é uma boa opção para hospedar esses aplicativos, oferecendo infraestrutura escalonável e confiável que é gerenciada e corrigida com quase nenhuma intervenção manual e tempo de inatividade. A plataforma Microsoft Azure fornece várias opções de armazenamento de dados para dar suporte a aplicativos web hospedados nos Sites do Microsoft Azure do Banco de Dados SQL do Microsoft Azure, um banco de dados como serviço gerenciado relacional e escalável, e serviços populares de nossos parceiros, como banco de dados ClearDB MySQL e MongoDB.

Uma abordagem alternativa é fazer uso de seu investimento existente no local. No cenário de exemplo, um sistema de despesas de funcionários, você pode desejar manter seu armazenamento de dados em sua própria infraestrutura interna. Isso pode ser para integração com sistemas internos (relatórios, folha de pagamento, cobrança etc.) ou para satisfazer um requisito de governança de TI.  O Sites do Microsoft Azure fornece dois métodos para permitir que você se conecte a sua infraestrutura local:

- [Conexões híbridas](http://aka.ms/hybridconnections) - As conexões híbridas são um recurso dos Serviços BizTalk do Microsoft Azure e permitem que os Sites do Azure se conectem a recursos locais com segurança, por exemplo, SQL Server, MySQL, APIs da Web e serviços Web personalizados. 
- [Integração de Rede Virtual](http://aka.ms/websitesvnet) - A Integração de Rede Virtual dos Sites do Azure permite que você conecte seu site a uma Rede Virtual do Azure que, por sua vez, pode ser conectado a sua infraestrutura local através de uma VPN site a site. 

O diagrama a seguir é uma solução de alto nível de exemplo com as opções de conectividade para recursos locais.

![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png)

## Benefícios para os negócios ##

O Sites do Microsoft Azure fornece uma série de benefícios de negócios que permitem que sua função seja muito mais econômica e ágil na entrega para as necessidades de negócios. 

### Modelo de PaaS ###

O Sites do Azure baseia-se em um modelo de plataforma como serviço que fornece uma série de economias de custo e eficiência.  Não é necessário gastar horas gerenciando VMs, aplicando patches de sistemas operacionais e estruturas. O Sites do Azure é um ambiente corrigido automaticamente que permite que você se concentre no gerenciamento de seus aplicativos da web e não em VMs, deixando as equipes livres para fornecer valor comercial adicional.

O modelo de PaaS que dá suporte aos Sites do Microsoft Azure permite que profissionais da metodologia DevOps atendem suas metas. Como uma empresa, isso significa gerenciamento completo e integração em todo o ciclo de vida do aplicativo, incluindo o desenvolvimento, teste, versão, monitoramento e gerenciamento e suporte. 

Para equipes de desenvolvimento, fluxos de trabalho de implantação e integração contínuos podem ser configurados no Visual Studio Online, no GitHub, no TeamCity, no Hudson ou no BitBucket, habilitando a compilação automatizada, teste e implantação, permitindo ciclos mais rápidos de versão e, ao mesmo tempo, reduzindo a fricção envolvida na liberação na infraestrutura existente. O Sites do Azure também dá suporte à criação de vários ambientes de preparo e teste para seu fluxo de trabalho da versão de teste. Não é mais necessário reservar ou alocar o hardware para esses fins, você pode criar tantos ambientes conforme desejar e definir sua própria promoção para liberar o fluxo de trabalho. Como uma empresa, você poderia optar por liberar um slot de teste do controle do código-fonte, executar uma série de testes e, após a conclusão bem-sucedida, promover um slot de preparo e, finalmente, permutar para produção sem tempo de inatividade, com a vantagem adicional que aplicativos da Web hospedados em sites do Azure são pré-carregados para fornecer a melhor experiência possível para o cliente.

As equipes de operações podem se sentir seguras de que elas estão na melhor posição possível para reagir a problemas com qualquer um dos seus aplicativos Web hospedados em sites do Azure com recursos de alertas e monitoramento interno. As equipes de operações já devem ter investido em análises e soluções de monitoramento do Microsoft Visual Studio Application Insights, New Relic e AppDynamics. Isso também tem total suporte nos sites do Azure, permitindo a continuidade e um ambiente familiar do qual monitorar seus aplicativos da Web.

Por fim, os sites do Azure fornecem a funcionalidade de backup automático de seus sites e bancos de dados hospedados diretamente em um contêiner de armazenamento de Blob do Azure. Fornecendo um método fácil e muito econômico com o qual realizar a recuperação de desastres, reduzindo a necessidade de software e hardware complexos no local.

### Facilidade de migração ###

A rotação e manutenção de hardware são uma questão importante para empresas à medida que os ciclos de lançamento de hardware e sistemas operacionais aceleram. Talvez você tenha um número de servidores Windows Server 2003 R2 que estão chegando ao fim do suporte em 2015, mas eles ainda estão hospedando aplicativos Web fundamentais de sua empresa? O Sites do Azure é um ótimo candidato no qual hospedar esses aplicativos Web e de racionalizar o estado do hardware de negócios. O Sites do Azure fornece acesso a uma variedade de especificações de hardware que são gerenciadas e mantidas como parte do serviço, eliminando a necessidade de considerar os custos de gerenciamento e de substituição como parte de seu orçamento de infraestrutura.  A migração pode ser tão simples quanto uma ação de copiar e colar da sua implantação existente para sites do Azure ou uma migração mais complexa na qual usar o Assistente de Migração de Sites do Azure adicionará valor. Os aplicativos Web migrados aproveitam toda a variedade de serviços do Azure, integrando serviços adicionais aos aplicativos Web. Por exemplo, você pode considerar a inclusão do Active Directory do Azure para controlar o acesso ao seu aplicativo com base na associação de usuários a grupos de segurança. Outro exemplo pode ser adicionar serviços de cache para melhorar o desempenho e reduzir a latência, fornecendo melhor experiência geral do usuário. 

### Hospedagem de classe empresarial ###

O Sites do Azure fornece uma plataforma estável e confiável que é comprovadamente capaz de lidar com uma ampla variedade necessidades, desde negócios de cargas de trabalho de teste e desenvolvimento interno pequeno até sites de alto tráfego altamente dimensionados. Usando o Sites do Azure, você está fazendo uso da mesma plataforma de hospedagem de classe empresarial que a Microsoft, como empresa, usa para cargas de trabalho Web de alto valor. Os Sites do Azure, juntamente com todos os serviços na plataforma Azure, foram criados com segurança e conformidade com as exigências normativas, como ISO (ISO/IEC 27001:2005); certificados SOC1 e SOC2 SSAE 16/ISAE 3402, HIPAA BAA, PCI e Fedramp, bem no centro de cada elemento e recurso, para obter mais informações, consulte [http://aka.ms/azurecompliance](http://aka.ms/azurecompliance). 

A Plataforma Microsoft Azure permite que controles de autorização com base em função, possibilitando níveis corporativos de controle de recursos em sites do Azure. O RBAC proporciona às empresas a capacidade de implementar suas próprias políticas de gerenciamento de acesso para todos os seus ativos no ambiente do Azure, atribuindo usuários a grupos e, por sua vez, atribuindo as permissões necessárias para esses grupos em relação ao ativo, como um site do Azure. Para obter mais informações sobre o RBAC no Azure, consulte [http://aka.ms/azurerbac](http://aka.ms/azurerbac). Ao utilizar os sites do Azure você pode ter certeza de seus aplicativos Web serão implantados em um ambiente seguro e você terá controle total para a região que os ativos são implantados. 

Além disso, o Sites do Azure também é capaz de fazer uso integral de seus investimentos no local, oferecendo a capacidade de se conectar novamente a seus recursos internos, como o data warehouse ou o ambiente do SharePoint. Como discutido em [Solução de alto nível](#highlevel), você pode usar conexões híbridas e conectividade de rede virtual para estabelecer conexões com a infraestrutura local e serviços.

### Escala global ###

O Sites do Azure é uma plataforma escalonável e global, permitindo que seus aplicativos Web se desenvolvam e se adaptem às necessidades de uma empresa em crescimento rapidamente e com planejamento de longo prazo e custo mínimos. Em cenários de infraestrutura local comuns, a expansão e aumento de demanda localmente e geograficamente exigiriam uma grande quantidade de gerenciamento, planejamento e despesas para provisionar e gerenciar uma infraestrutura adicional. O Sites do Azure oferece a capacidade de dimensionar seus aplicativos Web com as oscilações e fluxos de seus requisitos. Por exemplo, usando o aplicativo de despesas como exemplo, na maioria do mês os usuários usam pouco o aplicativo, mas à medida que o prazo de envios de despesas de cada mês inserido e o uso do aplicativo aumentam, o Sites do Azure tem capacidade de provisionar automaticamente mais infraestrutura do seu aplicativo e, em seguida, quando o uso diminuir novamente, ele pode ser dimensionado para a infraestrutura da linha de base que você definir.

O Sites do Azure está disponível globalmente em 17 centros de dados, e aumentando. Para obter a lista mais atualizada de regiões e locais, consulte [http://aka.ms/azlocations](http://aka.ms/azlocations). Com o Sites do Azure sua empresa pode obter facilmente escala e alcance global. À medida que sua empresa cresce em novas regiões, os painéis do aplicativo de relatório que você usa e o host em sites do Azure podem ser implantados facilmente em datacenters adicionais e podem servir usuários locais muito mais rapidamente por meio da combinação de sites do Azure e do Gerenciador de Tráfego do Azure, tudo com o benefício adicional da infraestrutura escalável, podendo contratar e expandir conforme as necessidades de mudança dos escritórios regionais.
 
## Detalhes da solução ##

Vejamos um exemplo de um cenário de migração do aplicativo. Ele descreve os detalhes de como os recursos de sites do Azure são agrupados para fornecer valor comercial e ótima solução.
 
Neste exemplo, o aplicativo de linha de negócios que discutiremos é um aplicativo de relatório de despesas que permite que os funcionários enviem suas despesas para reembolso. O aplicativo é hospedado em um Windows Server 2003 R2 executando IIS6 e o banco de dados é um banco de dados do SQL Server 2005. O motivo pelo qual escolhemos o servidor mais antigo é devido ao final do serviço para o Windows Server 2003 R2 e SQL Server 2005 que ocorrerá em breve, e temos [ferramentas](http://aka.ms/websitesmigration) e [orientações](http://aka.ms/websitesmigrationresources) para migrar cargas de trabalho no Azure automaticamente. Com isso em mente, o padrão usado nesse exemplo se aplica a uma ampla realidade de cenários de migração. 

### Migrar um aplicativo existente ###

A etapa um da solução geral para mover um aplicativo de linha de negócios para sites do Azure é identificar os ativos existentes do aplicativo e a arquitetura. O exemplo neste artigo é um aplicativo Web ASP.NET hospedado em um único Servidor IIS com o banco de dados hospedado em um SQL Server separado, conforme mostrado na figura abaixo. Os funcionários efetuam logon no sistema usando uma combinação de nome de usuário e senha, eles inserem detalhes de despesas e carregam cópias digitalizadas de confirmações, no banco de dados, para cada item de despesa. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### Itens a serem considerados ####

Ao migrar um aplicativo de um ambiente local, convém ter em mente algumas restrições do Sites do Azure. Aqui estão alguns tópicos importantes a serem considerados ao fazer a migração de aplicativos Web para Sites do Azure ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-	Associações de porta - Os sites do Azure dão suporte apenas à porta 80 para HTTP e à porta 443 para tráfego HTTPS, se seu aplicativo ou site usar qualquer outra porta, então, depois da migração, o aplicativo ou site usará a porta 80 para HTTP e a porta 443 para tráfego HTTPS. Isso geralmente não é um grande problema, pois é comum em implantações locais para fazer uso de portas diferentes para superar o uso de nomes de domínio, especialmente em ambientes de desenvolvimento e teste
-	Autenticação - Os Sites do Azure dão suporte à autenticação anônima por padrão e autenticação de formulários como identificado por um aplicativo. O Sites do Azure pode oferecer autenticação do Windows quando o aplicativo ou o site estiver integrado apenas ao Active Directory do Azure e ADFS. Esse é um recurso que é abordado em mais detalhes [aqui](http://aka.ms/azurebizapp) 
-	GAC baseado em assemblies - O Sites do Azure não permite a implantação dos assemblies no Cache de Assembly Global (GAC), portanto, se o aplicativo ou site que está sendo migrado faz uso desse recurso local, mova os assemblies para a pasta bin do aplicativo ou site
-	Modo de compatibilidade do IIS5 - O Sites do Azure não dá suporte ao modo de compatibilidade do IIS5 e, assim, cada site e todos os aplicativos Web no site pai são executados no mesmo processo de trabalho em um único pool de aplicativos
-	Uso de bibliotecas COM - O Sites do Azure não permite o registro de componentes COM na plataforma, portanto, se o site ou aplicativo está fazendo uso de quaisquer componentes COM, eles precisarão ser regravados no código gerenciado e implantados com o site ou aplicativo
-	Filtros ISAPI - Os filtros ISAPI podem ter suporte em sites do Azure, eles precisarão ser implantados como parte do site e registrados no arquivo web.config do aplicativo Web ou site. Para obter mais informações, consulte [http://aka.ms/azurewebsitesxdt](http://aka.ms/azurewebsitesxdt). 

Depois de esses tópicos serem considerados, seu aplicativo Web deve estar pronto para a nuvem. E não se preocupe se alguns tópicos não forem totalmente atendidos, a ferramenta de migração fornecerá o melhor esforço de migração. 

As próximas etapas no processo de migração são para criar um Site do Azure e um Banco de Dados SQL do Azure. Há vários tamanhos de instâncias do Site do Microsoft Azure com vários núcleos de CPU e quantidades de RAM disponíveis para seleção com base em suas necessidades de aplicativos Web. Para obter mais informações, consulte [http://aka.ms/azurewebsitesskus](http://aka.ms/azurewebsitesskus). Da mesma forma, o Banco de Dados SQL do Microsoft Azure atenta para todas as necessidades de uma empresa com vários níveis de serviço e níveis de desempenho para atender aos requisitos. Informações adicionais podem ser encontradas em [http://aka.ms/azuresqldbskus](http://aka.ms/azuresqldbskus). Depois de criado, o site/aplicativo é carregado no site do Azure, por meio de FTP ou WebDeploy e, em seguida, é movido para o banco de dados.

Nessa migração, a solução usa o Banco de Dados SQL do Azure, mas esse não é o único banco de dados que tem suporte no Azure. As empresas também podem fazer uso do MySQL, MongoDB, Banco de Dados de Documentos do Azure e muito mais por meio de complementos que podem ser adquiridos na [Azure Store](http://aka.ms/azurestore). 

Ao criar um Banco de Dados SQL do Azure, várias opções estão disponíveis para importar um banco de dados existente de um servidor local, da geração de um script de banco de dados existente até o uso da [Importação e exportação de aplicativo da camada de dados](http://aka.ms/dacpac). 

O banco de dados de aplicativo de despesas foi elaborado criando-se um novo Banco de Dados SQL do Azure, conectando-se ao banco de dados usando o SQL Server Management Studio e, em seguida, executando um script para criar o esquema de banco de dados e preenchê-lo com dados do banco de dados local.

A etapa final neste primeiro estágio da migração requer a atualização de cadeias de conexão para o banco de dados para o aplicativo. Isso pode ser obtido por meio do Portal de Gerenciamento do Azure, cada site do Azure tem seu próprio painel junto com uma tela de configuração que permite a modificação das configurações específicas do aplicativo, incluindo qualquer cadeia de conexão que está sendo usada pelo aplicativo para se conectar a qualquer banco de dados que está sendo usado.

### Alternativas para o uso de Banco de Dados SQL do Azure ###

A plataforma Azure oferece uma série de alternativas para uso do Banco de Dados SQL do Azure como um banco de dados de aplicativos principais da Web, isso é para permitir que diferentes cargas de trabalho, por exemplo, uso de uma solução NoSQL ou para habilitar a plataforma para atender às necessidades de dados de uma empresa, por exemplo, uma empresa pode ter dados que não devem ser armazenados fora do local ou em um ambiente de nuvem pública e, portanto, buscaria manter o uso de seu banco de dados local.

#### Conectividade aos recursos no local ####
O Sites do Azure oferece dois métodos de conexão para recursos locais, como bancos de dados, permitindo a reutilização de infraestrutura de alto valor existente. Os dois métodos são a integração de rede virtual do Sites do Azure e conexões híbridas:

- A integração de rede virtual de Sites do Azure dá suporte à integração entre sites do Azure e uma rede virtual do Azure, permitindo o acesso a recursos que são executados em sua rede virtual, que, se conectada à sua rede nas instalações com VPN de local a local, possibilita a conectividade direta aos sistemas no local.
- As conexões híbridas são um recurso dos Serviços BizTalk do Azure e fornecem uma maneira fácil de se conectar aos recursos individuais locais, como o SQL Server, MySQL, APIs de Web HTTP e a maioria dos serviços Web personalizados.

#### Dimensionamento e resiliência ####

À medida que uma empresa aumenta sua força de trabalho por meio de aquisições ou do crescimento natural, então, os aplicativos Web também deverão ser dimensionados para atender a essas novas demandas. Atualmente, de fato, é comum ver uma propagação ainda maior de equipes colocalizadas e funcionários remotos, por exemplo, empresas com escritórios nos Estados Unidos, Europa e Ásia, com vendas móveis em muitas outras regiões. O Sites do Azure tem a capacidade de manipular as mudanças elásticas no dimensionamento confortavelmente e automaticamente.

O Sites do Microsoft Azure permite que os aplicativos Web estejam configurado para dimensionar automaticamente pelo Portal de Gerenciamento, dependendo de dois vetores - horários agendados ou pelo uso da CPU. O Dimensionamento Automático do Sites do Microsoft Azure fornece uma maneira extremamente flexível e econômica para atender as alterações maiores em uso para todos os aplicativos de negócios, desde aplicativos Web, como nosso sistema de relatório de despesas, até sites de marketing que enfrentam uma alta intermitência de tráfego para uma promoção rápida. Para obter mais informações e orientações sobre como dimensionar seus aplicativos Web usando Sites do Microsoft Azure, consulte [Como dimensionar sites](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/).

Além da flexibilidade de dimensionamento do Sites do Microsoft Azure, a plataforma geral permite a continuidade de negócios e resiliência por meio da distribuição possível de aplicativos Web e seus ativos em vários data centers e regiões geográficas.

## Resumo ##
O Sites do Microsoft Azure oferece uma solução flexível, econômica e responsiva para as necessidades dinâmicas de uma empresa em um ambiente em rápida evolução. As empresas usam o Sites do Azure para aumentar a produtividade e eficiência fazendo uso de uma plataforma gerenciada com recursos de DevOps modernos e gerenciamento prático reduzo, ao mesmo tempo fornecendo recursos corporativos em escala, resiliência, segurança e integração com ativos locais.

## Plano de ação ##
Para obter mais informações sobre o serviço Sites do Microsoft Azure, acesse [http://aka.ms/enterprisewebsites](http://aka.ms/enterprisewebsites), no qual mais informações podem ser obtidas, e se inscreva para uma versão de avaliação hoje mesmo em [http://aka.ms/azuretrial](http://aka.ms/azuretrial) para avaliar o serviço e descobrir os benefícios para sua empresa.
