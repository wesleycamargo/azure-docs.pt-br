<properties
	pageTitle="Visão geral de Aplicativos Web"
	description="Saiba mais sobre os Aplicativos Web do Serviço de Aplicativo"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime.espinosa"/>

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever para obter uma conta do Azure, vá para [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você poderá criar imediatamente um aplicativo Web starter de curta duração no Serviço de Aplicativo. Não é exigido um cartão de crédito; sem compromisso.

#Visão geral de aplicativos Web

O [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) é uma plataforma totalmente gerenciada para desenvolvedores profissionais que proporciona um avançado conjunto de recursos para cenários da Web, móveis e de integração. Crie e implante aplicativos Web críticos que são dimensionados de acordo com seus negócios usando o Serviço de Aplicativo do Azure.

Aproveite a capacidade dos [aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) para usar as linguagens e estruturas que você conhece e com as quais conta, implantar seus aplicativos rapidamente para a nuvem do Azure e melhorar continuamente seu código sem precisar mais se preocupar com a infraestrutura.

![Web Marketplace](./media/app-service-web-overview/marketplace.png)

## Mais do que apenas aplicativos Web##

As empresas modernas interagem com seus clientes de formas cada vez mais sofisticadas. Empresas de todos os tipos consideram sua presença corporativa na Web como parte crucial de seus negócios, um dos principais componentes em seu plano de negócios. Para acomodar essa importância, as empresas estão procurando por uma plataforma que lhes fornece agilidade, segurança e escalabilidade. Além disso, elas exigem a capacidade de se vincular a seus sistemas de negócios existentes, poder implantar novo código rapidamente e criar instâncias em todo o mundo. Com o Serviço de Aplicativo do Azure e os aplicativos Web, as organizações podem agradar a seus clientes de forma rápida e econômica.

## Por que aplicativos Web? ##

Os Aplicativos Web do Serviço de Aplicativo do Azure são uma plataforma totalmente gerenciada que o habilita a criar, implantar e dimensionar aplicativos Web de nível empresarial em segundos. Concentre-se em seu código de aplicativo e deixe que o Azure cuide da infraestrutura para dimensioná-la e executá-la com segurança para você. Os aplicativos Web são:

- **Familiares e Rápidos** - use suas habilidades existentes para escrever código em sua linguagem, estrutura e IDE favoritos. Com apenas alguns cliques, adicione controle de versão, atualização, logon único, agente de identidade, armazenamento isolado e monitoramento de desempenho a seus aplicativos Web existentes.  Acesse uma Galeria avançada para usar como blocos de construção a fim de acelerar seu desenvolvimento. Experimente produtividade de desenvolvedor incomparável com recursos de ponta como integração contínua, depuração de site ativo e IDE do Visual Studio líder do setor.
- **Nível Empresarial** - os aplicativos Web foram projetados para compilar e hospedar aplicativos críticos seguros. Crie aplicativos de negócios integrados do Active Directory que se conectam com segurança a recursos locais e hospede-os em uma plataforma de nuvem segura compatível com PCI, SOC2 e ISO. Ao mesmo tempo, desfrute de SLAs de nível empresarial.
- **Escala Global** - os aplicativos Web foram otimizados para oferecer disponibilidade e dimensionamento automático em uma infraestrutura de data center global. Expanda ou reduza facilmente os aplicativos sob demanda. Com alta disponibilidade fornecida em regiões geográficas diferentes e entre elas. A replicação de dados e a hospedagem de serviços em vários locais são rápidas e fáceis, e basta um clique do mouse para expandir-se para novas regiões e áreas geográficas tão simples.  

## Conceitos de aplicativos Web ##

- **Galeria de Aplicativos Web** - selecione em uma lista crescente de modelos de aplicativos Web existentes. Aproveite o melhor da comunidade de aplicativos OSS com a instalação com um clique de pacotes, como Wordpress, Joomla e Drupal. Inicie seu processo de desenvolvimento de aplicativos da maneira certa tirando proveito de estruturas como .NET MVC, Django e CakePHP.
- **Dimensionamento Automático** - os aplicativos Web permitem que você dimensione vertical ou horizontalmente com rapidez para lidar com qualquer carga de cliente de entrada. Selecione manualmente o número e o tamanho das máquinas virtuais ou configure o dimensionamento automático para dimensionar os servidores com base na carga ou na agenda.
- **Integração Contínua** - configure a integração contínua e fluxos de trabalho de implantação com VSO, GitHub, TeamCity, Hudson ou BitBucket, o que o habilita a compilar, testar e implantar automaticamente seu aplicativo Web a cada check-in de código ou testes de integração bem-sucedidos.
- **Slots de Implantação** - implemente a [Implantação de Teste][Slots] para verificar seu código em um ambiente de pré-produção que é idêntico a seu aplicativo Web de produção no Serviço de Aplicativo do Azure. Quando estiver satisfeito, lance uma nova versão de seu aplicativo sem tempo de inatividade executando uma operação de permuta. 
- **Testes em Produção** - leve as Implantações de Teste para o próximo patamar e execute [testes A/B][Tip] para verificar o novo código com uma fração configurável do tráfego ativo. 
- **Trabalhos Web** - execute qualquer programa ou script em máquinas virtuais de aplicativos Web. Execute trabalhos continuamente ou de acordo com uma agenda e dimensione para execução em várias máquinas virtuais. Use o [SDK WebJobs][Webjobs] do Azure para integrar ao Armazenamento do Azure ou ao Barramento de Serviço.

## Introdução ##
Para começar a usar aplicativos Web, siga o tutorial [Criar um aplicativo Web ASP.NET][create].

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][appservice].

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e Seu Impacto sobre os Serviços Existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[appservice]: app-service-value-prop-what-is.md
[create]: web-sites-dotnet-get-started.md
[Webjobs]: websites-dotnet-webjobs-sdk-get-started.md
[Slots]: web-sites-staged-publishing.md
[Tip]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx 

<!--HONumber=49-->