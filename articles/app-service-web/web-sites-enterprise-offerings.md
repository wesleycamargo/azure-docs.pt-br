---
title: "Ofertas de Aplicativos Web do Serviço de Aplicativo do Azure para Enterprise"
description: "Mostra como usar o Aplicativos Web do Serviço de Aplicativo do Azure para criar soluções de site corporativo para o seu negócio"
services: app-service\web
documentationcenter: 
author: apwestgarth
manager: wpickett
editor: 
ms.assetid: cf9ac3b2-0493-4461-8b64-251d3a5cd5b5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: anwestg
translationtype: Human Translation
ms.sourcegitcommit: 4d3b3096e42736fddd3bb5b9f39376271d193c58
ms.openlocfilehash: 980eb9ff0990d532a77614be571e3f6d24a1dfee

---

# <a name="azure-app-service-web-apps-offerings-for-enterprise-whitepaper"></a>White paper Ofertas de Aplicativos Web do Serviço de Aplicativo do Azure para Enterprise
A necessidade de reduzir custos e fornecer soluções de TI mais rápidas em um ambiente em rápida evolução cria novos desafios para desenvolvedores, profissionais de TI e gerentes. Os usuários procuram cada vez mais por seus aplicativos da Web de linha de negócios (LOB) para serem rápidos, responsivos e terem disponibilidade de qualquer dispositivo. Ao mesmo tempo, as empresas estão tentando aproveitar o aumento da produtividade e da eficiência da integração com a nuvem e os serviços móveis. Isso pode ser algo simples, como logon único em todos os dispositivos usando o Active Directory para colaboração em Office365 usando dados extraídos de um aplicativo LOB interno que, por sua vez, recebe dados da implementação da Salesforce. [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) é um serviço de nuvem de classe empresarial para desenvolver, testar e executar aplicativos móveis e Web, APIs Web e sites genéricos. Ele pode ser usado para executar sites corporativos, sites de intranet, aplicativos de negócios e campanhas de marketing digital em uma rede global de datacenters otimizados para escala e disponibilidade, além de suporte à integração contínua e práticas modernas de DevOps.  

Este white paper destaca os recursos do serviço de [Aplicativos Web](/services/app-service/web/) especificamente voltados para executar aplicativos Web de LOB, abordando a migração de aplicativos web existentes e a implantação de novos aplicativos Web de LOB na plataforma.

## <a name="audience"></a>Público-alvo
Os profissionais de TI, arquitetos e gerentes que desejam migrar para as cargas de trabalho da Web na nuvem que estão sendo executados localmente. Cargas de trabalho da Web podem abranger da empresa ao funcionário ou de negócios a aplicativos da Web de parceiros.

## <a name="introduction"></a>Introdução
O Aplicativos Web do Serviço de Aplicativo é uma plataforma ideal para hospedar aplicativos da web internos e externos e serviços, pois ele fornece uma solução econômica, altamente escalonável e gerenciada, permitindo que você se concentre no fornecimento de valor de negócios para os usuários em vez de gastar uma quantidade significativa de tempo e dinheiro mantendo e dando suporte a ambientes separados. O Aplicativos Web oferece uma plataforma flexível para implantar seus aplicativos Web corporativos, oferecendo a capacidade de continuar a autenticar no Active Directory local por meio da integração com o Active Directory do Microsoft Azure, suporte de implantações rápidas e fáceis, fazendo uso de suas práticas de implantação e integração contínuas e internas, enquanto é dimensionado automaticamente para crescer de acordo com as necessidades da empresa, tudo em uma plataforma gerenciada que permite que você se concentre no aplicativo, não na infraestrutura.

## <a name="problem-definition"></a>Definição do problema
O cenário de TI está mudando rapidamente, com uma migração da hospedagem em servidores tradicionais com seus custos de capital altos nos tempos de entrega a longo prazo para uma que utiliza o uso sob demanda de serviços que é dimensionado automaticamente para tratar da carga. Os departamentos de TI estão sendo desafiados a reduzir os custos e requisitos de infraestrutura e de manutenção gastos com foco na redução de CAPEX enquanto aumentam a agilidade. O fim da vida útil de plataformas mais antigas de infraestrutura, como o Windows Server 2003, está levando os departamentos de TI a revisar a migração na nuvem como uma possível maneira de evitar os custos de capital novo de longo prazo. No passado, os CIOs faziam as decisões de compra para outros departamentos, porém, cada vez mais, os CMOs e outros diretores de unidade de negócios estão adotando um papel mais ativo em como seu orçamento é gasto e qual é o retorno sobre o investimento. Cada vez mais, as empresas precisam que sua força de trabalho tenha muito mais mobilidade com funcionários trabalhando remotamente, passando mais tempo com clientes que precisam de acesso a sistemas sem complicações.

Necessidades dos negócios mudam mensalmente, semanalmente, diariamente. As empresas estão buscando escala global instantânea com serviços atualizados regulares e completos dos novos recursos, fornecidos por terceiros ou internamente.  Em alguns casos, as empresas também estão buscando os recursos para isolar seus aplicativos e o acesso aos recursos, ao mesmo tempo que também fazem uso dos recursos da Nuvem Pública. Os usuários têm expectativas mais elevadas, muitos deles já usam serviços em suas vidas particulares, como o Office365. Eles esperam ter acesso a serviços com recursos avançados, semelhantes e atualizados em sua rotina de trabalho. Para lidar com essa demanda, o departamento de TI deve ajudar os negócios para permitir isso através da seleção e integração com serviços de terceiros, a seleção cuidadosa de plataformas que podem se adaptar às necessidades comerciais e, ao mesmo tempo, confiáveis e com um menor custo total de propriedade.

As equipes de desenvolvimento estão procurando oferecer vantagem comercial imediata, oferecendo novos recursos com frequência. Elas estão procurando uma plataforma confiável e econômica que se integra a suas ferramentas e práticas existentes – desenvolvimento, teste, versão; e trabalhar em conjunto com os departamentos de TI automatiza a implantação, gerenciamento e alertas, tudo isso com o objetivo de tempo de inatividade zero.

<a name="highlevel"></a>
## <a name="high-level-solution"></a>Solução de alto nível
Estruturas e plataformas da web estão cada vez mais sendo usadas para desenvolver, testar e hospedar aplicativos de linha de negócios.  Com uma linha comum de aplicativo de negócios, como um sistema de despesas de funcionários internos, geralmente consistindo de apenas um aplicativo Web com um banco de dados de backup para armazenar os dados conectados ao aplicativo.

O Aplicativos Web do Serviço de Aplicativo é uma boa opção para hospedar esses aplicativos, oferecendo infraestrutura escalonável e confiável que é gerenciada e corrigida com quase nenhuma intervenção manual e tempo de inatividade. A plataforma Microsoft Azure fornece várias opções de armazenamento de dados para dar suporte a aplicativos web hospedados nos Aplicativos Web do Banco de Dados SQL do Microsoft Azure, um banco de dados como serviço gerenciado relacional e escalável, e serviços populares de nossos parceiros, como banco de dados ClearDB MySQL e MongoDB.

Uma abordagem alternativa é utilizar seu investimento existente local. No cenário de exemplo, um sistema de despesas de funcionários, você pode desejar manter seu armazenamento de dados em sua própria infraestrutura interna. Isso pode ser para integração com sistemas internos (relatórios, folha de pagamento, cobrança etc.) ou para satisfazer um requisito de governança de TI.  Os Aplicativos Web fornecem diversos métodos para permitir que você se conecte à sua infraestrutura local:

* [Ambientes do Serviço de Aplicativo](app-service-app-service-environment-intro.md) - O ASE (Ambientes do Serviço de Aplicativo) é um novo recurso Premium que foi recém-adicionado à oferta do Serviço de Aplicativo do Microsoft Azure.  Os ASEs fornecem um ambiente totalmente isolado e dedicado para executar aplicativos do Serviço de Aplicativo do Azure com segurança em alta escala, oferecendo também isolamento e acesso seguro à rede   
* [Conexões híbridas](../biztalk-services/integration-hybrid-connection-overview.md) – As conexões híbridas são um recurso dos Serviços BizTalk do Microsoft Azure e permitem que os Aplicativos Web se conectem a recursos locais com segurança, por exemplo, SQL Server, MySQL, APIs da Web e serviços Web personalizados.
* [Integração de Rede Virtual](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) : a integração do Aplicativos Web com a Rede Virtual do Azure permite que você conecte seu aplicativo Web a uma Rede Virtual do Azure que, por sua vez, pode ser conectado a sua infraestrutura local através de uma VPN site a site.

O diagrama a seguir mostra um exemplo de solução de alto nível com as opções de conectividade para os recursos locais.  O primeiro exemplo mostra como isso pode ser feito usando os recursos padrão do Serviço de Aplicativo do Azure e o segundo mostra como isso pode ser obtido com a oferta premium, Ambientes do Serviço de Aplicativo.

Usando os recursos padrão do Serviço de Aplicativo: ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Using Standard App Service Features")

Excluindo um Ambiente do Serviço de Aplicativo: ![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Using an App Service Environment")

## <a name="business-benefits"></a>Benefícios para os negócios
O Aplicativos Web do Serviço de Aplicativo fornece uma série de benefícios de negócios que permitem que sua função seja muito mais econômica e ágil na entrega para as necessidades de negócios.

### <a name="paas-model"></a>Modelo de PaaS
O Aplicativos Web do Serviço de Aplicativo baseia-se em um modelo de plataforma como serviço que fornece uma série de economias de custo e eficiência.  Não é necessário gastar horas gerenciando VMs, aplicando patches de sistemas operacionais e estruturas. O Aplicativos Web é um ambiente corrigido automaticamente que permite que você se concentre no gerenciamento de seus aplicativos da web e não em VMs, deixando as equipes livres para fornecer valor comercial adicional.

O modelo de PaaS que dá suporte ao Aplicativos Web permite que profissionais da metodologia DevOps atendem suas metas. Como uma empresa, isso significa gerenciamento completo e integração em todo o ciclo de vida do aplicativo, incluindo o desenvolvimento, teste, versão, monitoramento e gerenciamento e suporte.

Para equipes de desenvolvimento, fluxos de trabalho de implantação e integração contínuos podem ser configurados no Visual Studio Team Services, no GitHub, no TeamCity, no Hudson ou no BitBucket, habilitando a compilação automatizada, teste e implantação, permitindo ciclos mais rápidos de versão e, ao mesmo tempo, reduzindo a fricção envolvida na liberação na infraestrutura existente. O Aplicativos Web também dá suporte à criação de vários ambientes de preparo e teste para seu fluxo de trabalho da versão de teste. Não é mais necessário reservar ou alocar o hardware para esses fins, você pode criar tantos ambientes conforme desejar e definir sua própria promoção para liberar o fluxo de trabalho. Como uma empresa, você poderia optar por liberar um slot de teste do controle do código-fonte, executar uma série de testes e, após a conclusão bem-sucedida, promover um slot de preparo e, finalmente, permutar para produção sem tempo de inatividade, com a vantagem adicional que aplicativos da Web hospedados no Aplicativos Web são pré-carregados para fornecer a melhor experiência possível para o cliente.  Além disso, as empresas podem usar os recursos de Teste em Produção dos Aplicativos Web do Serviço de Aplicativo para direcionar uma seção do tráfego para um slot diferente, validar as alterações antes de mudar todo o tráfego para a nova implantação ou de reverter todo o tráfego para a implantação anterior.

As equipes de operações podem se sentir seguras de que elas estão na melhor posição possível para reagir a problemas com qualquer um dos seus aplicativos Web hospedados em Aplicativos Web com recursos de alertas e monitoramento interno. As equipes de operações já devem ter investido em análises e soluções de monitoramento do Microsoft Visual Studio Application Insights, New Relic e AppDynamics. Isso também tem total suporte no Aplicativos Web, permitindo a continuidade e um ambiente familiar do qual monitorar seus aplicativos Web.

Por fim, o Aplicativos Web fornece a funcionalidade de backup automático de seus aplicativos e bancos de dados hospedados diretamente em um contêiner de armazenamento de Blob do Azure. Fornecendo um método fácil e muito econômico com o qual realizar a recuperação de desastres, reduzindo a necessidade de software e hardware complexos no local.

### <a name="ease-of-migration"></a>Facilidade de migração
A rotação e manutenção de hardware são uma questão importante para empresas à medida que os ciclos de lançamento de hardware e sistemas operacionais aceleram. Talvez você tenha um número de servidores Windows Server 2003 R2 que estão chegando ao fim do suporte em 2015, mas eles ainda estão hospedando aplicativos Web fundamentais de sua empresa? O Aplicativos Web do Serviço de Aplicativo é um ótimo candidato no qual hospedar esses aplicativos Web e de racionalizar o estado do hardware de negócios. O Aplicativos Web fornece acesso a uma variedade de especificações de hardware que são gerenciadas e mantidas como parte do serviço, eliminando a necessidade de considerar os custos de gerenciamento e de substituição como parte de seu orçamento de infraestrutura.  A migração pode ser tão simples quanto uma ação de copiar e colar da sua implantação existente para Aplicativos Web ou uma migração mais complexa na qual usar o Assistente de Migração de Aplicativos Web adicionará valor. Os aplicativos Web migrados aproveitam toda a variedade de serviços do Azure, integrando serviços adicionais aos aplicativos Web. Por exemplo, você pode considerar a inclusão do Active Directory do Azure para controlar o acesso ao seu aplicativo com base na associação de usuários a grupos de segurança. Outro exemplo pode ser adicionar serviços de cache para melhorar o desempenho e reduzir a latência, fornecendo melhor experiência geral do usuário.

### <a name="enterprise-class-hosting"></a>Hospedagem de classe empresarial
O Aplicativos Web do Serviço de Aplicativo fornece uma plataforma estável e confiável que é comprovadamente capaz de lidar com uma ampla variedade necessidades, desde negócios de cargas de trabalho de teste e desenvolvimento interno pequeno até sites de alto tráfego altamente dimensionados. Usando o Aplicativos Web, você está fazendo uso da mesma plataforma de hospedagem de classe empresarial que a Microsoft, como empresa, usa para cargas de trabalho Web de alto valor. O Aplicativos Web, juntamente com todos os serviços na plataforma Azure, foi criado com segurança e conformidade com as exigências normativas, como ISO (ISO/IEC 27001:2005); certificados SOC1 e SOC2 SSAE 16/ISAE 3402, HIPAA BAA, PCI e Fedramp, bem no centro de cada elemento e recurso, Para saber mais, confira [http://aka.ms/azurecompliance](/support/trust-center/compliance/).

A Plataforma Microsoft Azure permite que controles de autorização com base em função, possibilitando níveis corporativos de controle de recursos no Aplicativos Web. O RBAC proporciona às empresas a capacidade de implementar suas próprias políticas de gerenciamento de acesso para todos os seus ativos no ambiente do Azure, atribuindo usuários a grupos e, por sua vez, atribuindo as permissões necessárias para esses grupos em relação ao ativo, como um aplicativo Web. Para saber mais sobre o RBAC no Azure, confira [http://aka.ms/azurerbac](../active-directory/role-based-access-control-configure.md). Ao utilizar Aplicativos Web você pode ter certeza de seus aplicativos Web serão implantados em um ambiente seguro e você terá controle total para a região que os ativos são implantados.

Os Ambientes do Serviço de Aplicativo do Azure [http://aka.ms/aseintro](http://aka.ms/aseintro) são uma nova opção de plano de serviço premium para os clientes corporativos que desejam usar o Serviço de Aplicativo do Azure; eles também fornecem um ambiente totalmente isolado e dedicado.  Isso permite que os clientes corporativos implantem aplicativos que podem aproveitar uma escala muito alta, ao mesmo tempo que também têm controle total sobre o tráfego de rede de entrada e saída; além disso, os ASEs permitem que os aplicativos tenham conexões seguras de alta velocidade em redes virtuais oas recursos locais.

Além disso, os Aplicativos Web do Serviço de Aplicativo também podem usar por completo de seus investimentos locais, oferecendo a capacidade de se conectar novamente a seus recursos internos, como o data warehouse ou o ambiente do SharePoint. Como discutido em [Solução de alto nível](#high-level-solution) , você pode usar conexões híbridas e conectividade de rede virtual para estabelecer conexões com a infraestrutura local e serviços.

### <a name="global-scale"></a>Escala global
O Aplicativos Web do Serviço de Aplicativo é uma plataforma escalonável e global, permitindo que seus aplicativos Web se desenvolvam e se adaptem às necessidades de uma empresa em crescimento rapidamente e com planejamento de longo prazo e custo mínimos. Em cenários de infraestrutura local comuns, a expansão e aumento de demanda localmente e geograficamente exigiriam uma grande quantidade de gerenciamento, planejamento e despesas para provisionar e gerenciar uma infraestrutura adicional. O Aplicativos Web oferece a capacidade de dimensionar seus aplicativos Web com as oscilações e fluxos de seus requisitos. Por exemplo, usando o aplicativo de despesas como exemplo, na maioria do mês os usuários usam pouco o aplicativo, mas à medida que o prazo de envios de despesas de cada mês inserido e o uso do aplicativo aumentam, o Aplicativos Web tem capacidade de provisionar automaticamente mais infraestrutura do seu aplicativo e, em seguida, quando o uso diminuir novamente, ele pode ser dimensionado para a infraestrutura da linha de base que você definir.

Os Aplicativos Web estão disponíveis globalmente em 24 datacenters no mundo todo e esse número aumenta cada vez mais. Para obter a lista mais atualizada de regiões e locais, confira [http://aka.ms/azlocations](http://aka.ms/azlocations). Com o Aplicativos Web sua empresa pode obter facilmente escala e alcance global. À medida que sua empresa cresce em novas regiões, os painéis do aplicativo de relatório que você usa e o host no Aplicativos Web podem ser implantados facilmente em datacenters adicionais e podem servir usuários locais muito mais rapidamente por meio da combinação do Aplicativos Web e do Gerenciador de Tráfego do Azure, tudo com o benefício adicional da infraestrutura escalável, podendo contratar e expandir conforme as necessidades de mudança dos escritórios regionais.

## <a name="solution-details"></a>Detalhes da solução
Vejamos um exemplo de um cenário de migração do aplicativo. Ele descreve os detalhes de como os recursos do Aplicativos Web do Serviço de Aplicativo são agrupados para fornecer valor comercial e ótima solução.

Neste exemplo, o aplicativo de linha de negócios que discutiremos é um aplicativo de relatório de despesas que permite que os funcionários enviem suas despesas para reembolso. O aplicativo é hospedado em um Windows Server 2003 R2 executando IIS6 e o banco de dados é um banco de dados do SQL Server 2005. O motivo pelo qual escolhemos o servidor mais antigo se deve ao final do serviço para o Windows Server 2003 R2 e SQL Server 2005 que ocorrerá em breve, e temos [ferramentas](http://aka.ms/websitesmigration) e [orientações](http://aka.ms/websitesmigrationresources) para migrar cargas de trabalho no Azure automaticamente. Com isso em mente, o padrão usado nesse exemplo se aplica a uma ampla realidade de cenários de migração.

### <a name="migrate-existing-application"></a>Migrar um aplicativo existente
A etapa um da solução geral para mover um aplicativo de linha de negócios para o Aplicativos Web é identificar os ativos existentes do aplicativo e a arquitetura. O exemplo neste artigo é um aplicativo Web ASP.NET hospedado em um único Servidor IIS com o banco de dados hospedado em um SQL Server separado, conforme mostrado na figura abaixo. Os funcionários efetuam logon no sistema usando uma combinação de nome de usuário e senha, eles inserem detalhes de despesas e carregam cópias digitalizadas de confirmações, no banco de dados, para cada item de despesa.

![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### <a name="items-to-consider"></a>Itens a serem considerados
Ao migrar um aplicativo de um ambiente local, convém ter em mente algumas restrições do Aplicativos Web. Aqui estão alguns tópicos importantes a serem considerados ao fazer a migração de aplicativos Web para o Aplicativos Web ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

* Associações de porta – aplicativos Web somente dão suporte à porta 80 para HTTP e à porta 443 para tráfego HTTPS. Se seu aplicativo usa outra porta, depois de migrados, o aplicativo usará a porta 80 para HTTP e a porta 443 para tráfego HTTPS. Isso geralmente não é um grande problema, pois é comum em implantações locais para fazer uso de portas diferentes para superar o uso de nomes de domínio, especialmente em ambientes de desenvolvimento e teste
* Autenticação – os aplicativos Web dão suporte à Autenticação Anônima por padrão e à Autenticação de Formulários quando identificadas por um aplicativo. O Aplicativos Web pode oferecer Autenticação do Windows somente quando o aplicativo está integrado ao Active Directory do Azure e ao ADFS. Esse recurso é abordado mais detalhadamente [aqui](http://aka.ms/azurebizapp)
* Assemblies baseados em GAC – o Aplicativos Web não permite a implantação de assemblies para o Cache de Assembly Global (GAC). Portanto, se o aplicativo em migração usa esse recurso no local, considere mover os assemblies para a pasta bin do aplicativo.
* Modo de compatibilidade do IIS5 – O Aplicativos Web não dá suporte ao modo de compatibilidade do IIS5 e, assim, cada instância do Aplicativos Web e todos os aplicativos Web na instância principal do Aplicativos Web são executados no mesmo processo de trabalho em um único pool de aplicativos.
* Uso de bibliotecas COM – o Aplicativos Web não permite o registro de componentes COM na plataforma. Portanto, se o aplicativo usa componentes COM, eles precisam ser regravados no código gerenciado e implantados com o aplicativo.
* Filtros ISAPI – os Filtros ISAPI podem ter suporte no Aplicativos Web. Eles precisam ser implantados como parte do aplicativo e registrados no arquivo web.config do aplicativo Web. Para saber mais, confira [http://aka.ms/azurewebsitesxdt](web-sites-transform-extend.md).

Depois de esses tópicos serem considerados, seu aplicativo Web deverá estar pronto para a nuvem. E não se preocupe se alguns tópicos não forem totalmente atendidos, a ferramenta de migração fornecerá o melhor esforço de migração.

As próximas etapas no processo de migração são para criar um aplicativo Web do Serviço de Aplicativo e um Banco de Dados SQL do Azure. Há vários tamanhos de instâncias do Aplicativos Web com vários núcleos de CPU e quantidades de RAM disponíveis para seleção com base em suas necessidades de aplicativos Web. Para obter mais informações sobre preços, consulte [https://azure.microsoft.com/pricing/details/app-service/](https://azure.microsoft.com/pricing/details/app-service/). Da mesma forma, o Banco de Dados SQL do Microsoft Azure atenta para todas as necessidades de uma empresa com vários níveis de serviço e níveis de desempenho para atender aos requisitos. Informações adicionais podem ser encontradas em [https://azure.microsoft.com/pricing/details/sql-database/](https://azure.microsoft.com/pricing/details/sql-database/). Depois de criado, o aplicativo é carregado no Aplicativos Web do Serviço de Aplicativo, por meio de FTP ou WebDeploy e, em seguida, é movido para o banco de dados.

Nessa migração, a solução usa o Banco de Dados SQL do Azure, mas esse não é o único banco de dados que tem suporte no Azure. As empresas também podem fazer uso do MySQL, MongoDB, Banco de Dados de Documentos do Azure e muito mais por meio de complementos que podem ser adquiridos na [Azure Store](/marketplace/partner-program/).

Ao criar um Banco de Dados SQL do Azure, várias opções estão disponíveis para importar um banco de dados existente de um servidor local, da geração de um script de banco de dados existente até o uso da [Importação e exportação de aplicativo da camada de dados](http://aka.ms/dacpac).

O banco de dados de aplicativo de despesas foi elaborado criando-se um novo Banco de Dados SQL do Azure, conectando-se ao banco de dados usando o SQL Server Management Studio e, em seguida, executando um script para criar o esquema de banco de dados e preenchê-lo com dados do banco de dados local.

A etapa final neste primeiro estágio da migração requer a atualização de cadeias de conexão para o banco de dados para o aplicativo. Isso pode ser obtido pelo Portal do Azure. Você pode modificar as configurações específicas de cada aplicativo Web, inclusive as cadeias de conexão usadas pelo aplicativo para se conectar a bancos de dados em uso.

### <a name="alternatives-to-using-azure-sql-database"></a>Alternativas para o uso de Banco de Dados SQL do Azure
A plataforma Azure oferece uma série de alternativas ao uso de Banco de Dados SQL do Azure como um banco de dados primário de aplicativos Web, para permitir diferentes cargas de trabalho, ou seja, o uso de uma solução NoSQL, ou para permitir que a plataforma atenda às necessidades de dados das empresas. Por exemplo, uma empresa pode armazenar dados que não devem ser armazenados fora do local ou em um ambiente de nuvem pública e, portanto, procuraria manter o uso de seu banco de dados local.

#### <a name="connectivity-to-on-premises-resources"></a>Conectividade aos recursos no local
Os Aplicativos Web do Serviço de Aplicativo oferecem várias opções para conexão aos recursos locais, como bancos de dados, permitindo a reutilização da infraestrutura de alto valor existente. As opções são listadas abaixo:

* Os Ambientes do Serviço de Aplicativo são isolados e criados em uma sub-rede de uma rede virtual, permitindo que o ambiente se comunique com pontos de extremidade privados localizados na mesma rede virtual — [http://aka.ms/appserviceasenetworking](http://aka.ms/appserviceasenetworking)
* A integração de rede virtual do Aplicativos Web dá suporte à integração entre o Aplicativos Web e uma rede virtual do Azure, permitindo o acesso a recursos que são executados em sua rede virtual, que, se conectada à sua rede nas instalações com VPN de local a local, possibilita a conectividade direta aos sistemas no local.
* As conexões híbridas são um recurso dos Serviços BizTalk do Azure e fornecem uma maneira fácil de se conectar aos recursos individuais locais, como o SQL Server, MySQL, APIs de Web HTTP e a maioria dos serviços Web personalizados.

#### <a name="scale-and-resiliency"></a>Dimensionamento e resiliência
À medida que uma empresa aumenta sua força de trabalho por meio de aquisições ou do crescimento natural, então, os aplicativos Web também deverão ser dimensionados para atender a essas novas demandas. Atualmente, de fato, é comum ver uma propagação ainda maior de equipes colocalizadas e funcionários remotos, por exemplo, empresas com escritórios nos Estados Unidos, Europa e Ásia, com vendas móveis em muitas outras regiões. O Aplicativos Web tem a capacidade de manipular as mudanças elásticas no dimensionamento confortavelmente e automaticamente.

O Aplicativos Web do Serviço de Aplicativo permite que os aplicativos Web estejam configurado para dimensionar automaticamente pelo Portal do Azure, dependendo de dois vetores – horários agendados ou pelo uso da CPU. O Dimensionamento Automático do Aplicativos Web fornece uma maneira extremamente flexível e econômica para atender as alterações maiores em uso para todos os aplicativos de negócios, desde aplicativos Web, como nosso sistema de relatório de despesas, até sites de marketing que enfrentam uma alta intermitência de tráfego para uma promoção rápida. Para saber mais e orientações sobre como dimensionar seus aplicativos Web usando Aplicativos Web, confira [Como dimensionar sites](web-sites-scale.md).

Além da flexibilidade de dimensionamento do Aplicativos Web, a plataforma geral permite a continuidade de negócios e resiliência por meio da distribuição possível de aplicativos Web e seus ativos em vários data centers e regiões geográficas.

## <a name="summary"></a>Resumo
O Aplicativos Web do Serviço de Aplicativo oferece uma solução flexível, econômica e responsiva para as necessidades dinâmicas de uma empresa em um ambiente em rápida evolução. As empresas usam o Aplicativos Web para aumentar a produtividade e eficiência fazendo uso de uma plataforma gerenciada com recursos de DevOps modernos e gerenciamento prático reduzo, ao mesmo tempo fornecendo recursos corporativos em escala, resiliência, segurança e integração com ativos locais.

## <a name="call-to-action"></a>Plano de ação
Para saber mais sobre o serviço de Aplicativos Web do Serviço de Aplicativo do Azure, acesse [http://aka.ms/enterprisewebsites](/services/websites/enterprise/), no qual mais informações podem ser obtidas e se inscreva para uma versão de avaliação hoje mesmo em [https://azure.microsoft.com/pricing/free-trial/](https://azure.microsoft.com/pricing/free-trial/) para avaliar o serviço e descobrir os benefícios para sua empresa.

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]



<!--HONumber=Dec16_HO1-->


