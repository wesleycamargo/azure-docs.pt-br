<properties
	pageTitle="Eu uso Serviços Móveis, como o Serviço de Aplicativo ajuda?"
	description="Saiba quais são as vantagens que o Serviço de Aplicativo traz para os projetos de serviços móveis existentes."
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
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Eu uso os serviços móveis, como o serviço de aplicativo ajuda?

##Visão geral
Seu serviço móvel existente é seguro e permanecerá com suporte. No entanto, há uma série de vantagens que a plataforma *Serviço de Aplicativo do Azure* oferece para seu aplicativo móvel que não estão disponíveis hoje com os serviços móveis:

- Oferta mais simples, mais fácil e mais econômica para aplicativos que incluem tanto Web quanto clientes móveis
- Novos recursos de host, incluindo Trabalhos da Web, CNames personalizados, melhor monitoramento
- Integração pronta para uso com o Office 365, Dynamics CRM, Salesforce e outras APIs SaaS vitais.
- Suporte a código de back-end Java e PHP, além do Node.js e .NET
- Integração pronta para uso com o Gerenciador de Tráfego
- Conectividade com seus recursos locais e VPNs usando VNet, além de conexões híbridas
- Monitoramento e solução de problemas do seu aplicativo usando NewRelic ou AppInsights, além de alertas
- Espectro mais avançado de recursos de computação subjacentes, por exemplo, tamanhos de VM
- Dimensionamento automático interno, balanceamento de carga e monitoramento de desempenho.
- Recursos de preparação integrada, backup, reversão e testes em produção

## Novos recursos de hospedagem
Em *Serviço de Aplicativo do Azure*, o código de back-end *Aplicativo Móvel* é executado no mesmo contêiner como Aplicativo Web e Aplicativo de API. Desse modo você pode tirar proveito de todos os recursos nesse contêiner, incluindo alguns que não estão atualmente presentes nos serviços móveis:

- adicionar lógica de back-end em execução contínua por meio de trabalhos da Web
- assegurar que seu código de back-end esteja sempre em execução
- usar CNames personalizado para fornecer nomes amigáveis e estáveis para seus pontos de extremidade móveis de back-end
- realizar o geodimensionamento de seu aplicativo com o Gerenciador de Tráfego


##Conectar seu *aplicativo móvel* à API SaaS
O *Serviço de Aplicativo do Azure* torna mais fácil conectar seu aplicativo móvel a APIs de SaaS, incluindo o Office 365, Dynamics, Salesforce, SAP e mais. O *Serviço de Aplicativo do Azure* oferece autenticação pronta para uso em nome do usuário e permite que você execute logon único verdadeiro entre todas as APIs de SaaS usadas por você, associando tokens para APIs de SaaS individuais à sua identidade primária

##Acessar dados locais usando VNet
Com os serviços móveis, hoje, você já pode usar conexões híbridas para acessar os recursos locais. No entanto, há situações em que uma solução VPN é preferível. Com o *Serviço de Aplicativo do Azure* você pode usar o Azure VNet para seu código de back-end de aplicativo móvel.

##Use sua linguagem de back-end favorita
O *Serviço de Aplicativo do Azure* oferece suporte mais amplo e mais avançado para plataformas de desenvolvimento incluindo Java, PHP e Python, além de .NET e Node.js, com suporte nos Serviços Móveis.

##Configurar o dimensionamento automático
Com os serviços móveis, todas as instâncias do seu código de back-end estavam sendo executados em VMs pequenas. O *Serviço de Aplicativo do Azure* permite que você selecione o tamanho das VMs de um conjunto muito mais rico de opções. Você também pode dimensionar rapidamente para mais ou menos para lidar com qualquer carga de cliente de entrada, com base em várias métricas de desempenho.

##Estar no "saber"
Reaja aos problemas em tempo real com monitoramento e alertas para notificar automaticamente você e sua equipe. Integre a análise avançada de aplicativos e monitoramento de funcionalidades a partir do New Relic e AppInsights para obter uma compreensão ainda melhor do desempenho do seu aplicativo móvel. Com o *Serviço de Aplicativo do Azure*, você pode agora configurar alertas programaticamente e por meio do portal com base em várias métricas de desempenho

##Mantenha seus ativos seguros
Faça backup automático de seu back-end e banco de dados. O seu código e dados são protegidos contra desastres e facilmente restaurados, permitindo a você gerir seus negócios com confiança.

##Preparar, apontar, fogo!
Com o *Serviço de Aplicativo do Azure*, agora você pode criar vários ambientes particulares de teste e preparo para seus aplicativos móveis. Use-os para executar testes antes de implantar. Alterne para a produção sem tempo de inatividade. Aplicativos Web são pré-carregados, garantindo a melhor experiência do cliente.



Até que o recurso *Aplicativo Móvel do Serviço de Aplicativo do Azure* fique disponível, forneceremos uma experiência de migração perfeita para seus serviços móveis existentes para o serviço de aplicativo, se você optar por migrar. Enquanto isso, você pode começar a explorar o *Serviço de Aplicativo* e tirar proveito do *Serviço de Aplicativo* para seu serviço móvel existente seguindo este [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).
 

<!---HONumber=July15_HO4-->