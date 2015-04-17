<properties
	pageTitle="Eu uso Serviços Móveis, como o Serviço de Aplicativo ajuda?"
	description="Saiba quais são as vantagens do Serviço de Aplicativo para projetos de serviços móveis existentes."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"></a>Eu uso Serviços Móveis, como o Serviço de Aplicativo ajuda?

##Visão geral
Seu serviço móvel existente é seguro e permanecerá com suporte.  No entanto, há várias vantagens que a plataforma *Azure App Service* fornece para seu aplicativo móvel que não estão disponíveis com os serviços móveis:  

- Oferta mais simples, fácil e econômica para aplicativos que incluem clientes Web e móveis
- Novos recursos de host, incluindo trabalhos Web, CNames personalizados e melhor monitoramento
- Integração completa com o Office 365, Dynamics CRM, Salesforce e outras APIs SaaS vitais.
- Suporte para código de back-end Java e PHP, além de Node. js e .NET 
- Integração completa com o Gerenciador de Tráfego
- Conectividade com seus recursos locais e VPNs usando VNet, além de conexões híbridas
- Monitoramento e solução de problemas do seu aplicativo usando NewRelic ou AppInsights, bem como alertas
- Espectro mais avançado de recursos de computação subjacente, por exemplo, tamanhos de VM 
- Dimensionamento automático, balanceamento de carga e monitoramento de desempenho internos.
- Recursos internos de preparação, backup, reversão e testes em produção 

## Novos recursos de hospedagem
Em *Azure App Service*, o *Mobile App* código de back-end é executado no mesmo contêiner que o Aplicativo Web e o Aplicativo de API.  Dessa forma, você pode tirar proveito de todos os recursos nesse contêiner, incluindo alguns que não estão atualmente presentes nos serviços móveis: 

- adicionar lógica de back-end em execução contínua por meio de trabalhos Web 
- garanta que o código de back-end esteja sempre em execução
- use CNames personalizados para fornecer nomes amigáveis e estáveis para seus pontos de extremidade móvel de back-end
- dimensione seu aplicativo geograficamente com o Gerenciador de Tráfego


##Conecte seu *Mobile App* à API de SaaS
O *Serviço de Aplicativo do Azure* torna fácil conectar seu Aplicativo Móvel a APIs de SaaS, incluindo Office 365, Dynamics, Salesforce, SAP e muito mais. O *Azure App Service* oferece autenticação completa em nome do usuário e permite executar verdadeiro logon único entre todas as APIs de SaaS que você usa associando tokens a APIs de SaaS individuais à sua identidade primária

##Acessar dados locais usando VNet
Com os serviços móveis hoje, você já pode usar conexões híbridas para acessar os recursos locais.  No entanto, há situações em que uma solução VPN é preferencial.  Com *Azure App Service*, você pode usar o Azure VNet para seu código de back-end do Aplicativo Móvel.

##Use sua linguagem favorita de back-end
O *Serviço de Aplicativo do Azure* dá suporte mais amplo e mais avançado para plataformas de desenvolvimento, incluindo Java, PHP e Python, além de .NET e Node. js, que têm suporte nos Serviços Móveis.

##Configurar a escala automática
Com os serviços móveis, todas as instâncias do seu código de back-end estavam executando em pequenas VMs. O *Azure App Service* permite que você selecione o tamanho das VMs de um conjunto muito mais avançado de opções.  Você também pode escalar verticalmente ou horizontalmente rapidamente para lidar com qualquer carga de cliente de entrada com base em várias métricas de desempenho. 

##Fique "por dentro"
Reaja aos problemas em tempo real com monitoramento e alertas para notificar automaticamente você e sua equipe.  Integre a funcionalidade avançada de análise e monitoramento de aplicativos do New Relic e AppInsights para compreender ainda melhor o desempenho do seu aplicativo móvel.  Com o *Azure App Service*, você agora pode configurar programaticamente e por meio de alertas de configuração do portal baseados em uma várias métricas de desempenho

##Proteja seus ativos
Faça backup automático seu back-end e banco de dados.  Seu código e dados estão protegidos contra desastres e podem ser facilmente restaurados, permitindo que você administre seus negócios com confiança.

##Preparar, Apontar, Fogo!
Com *Azure App Service* agora você pode criar vários ambientes de teste e preparação privados para seus aplicativos móveis.  Use-os para executar testes antes de implantar.  Alterne para a produção sem tempo de inatividade.  Aplicativos Web são pré-carregados, garantindo a melhor experiência do cliente.



No momento em que o *Azure App Service Mobile App* recurso fica disponível, fornecemos a experiência de migração perfeita para os serviços móveis existentes para o Serviço de Aplicativo se você optar por migrar.  Enquanto isso, você pode começar a explorar *App Service* e tirar proveito *App Service* para seu serviço móvel existente seguindo este [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->