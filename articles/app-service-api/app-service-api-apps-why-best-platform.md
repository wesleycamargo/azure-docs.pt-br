<properties 
	pageTitle="O que são aplicativos de API?" 
	description="Saiba por que o Serviço de Aplicativo do Azure é a melhor plataforma para desenvolver, publicar e hospedar APIs RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# O que são aplicativos de API?

Aplicativos de API fazem parte do pacote [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) que também inclui aplicativos Web, móveis e lógicos.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

A parte de aplicativos da API desse pacote fornece uma rica plataforma e ecossistema para compilar, consumir e distribuir APIs na nuvem e nos locais.

>[AZURE.NOTE]Aplicativos de API estão atualmente em visualização pública. Ele é compilado na parte superior dos [aplicativos Web do Serviço de Aplicativo](../app-service-web/app-service-web-overview.md), que é um serviço GA (Geralmente Disponível) destinado à compilação e hospedagem segura de aplicativos críticos em escala global. Se você estiver procurando por um serviço GA para compilação de sua API hoje, os aplicativos Web são uma ótima opção. Quando os aplicativos de API ficarem disponíveis, forneceremos um caminho para usar os aplicativos Web existentes e aproveitar os recursos dos aplicativos de API.

## Por que aplicativos de API?

Um aplicativo de API é um [aplicativo Web do Serviço de Aplicativo](../app-service-web/app-service-web-overview.md) com recursos adicionais que melhoram a experiência de desenvolvimento, implantação, publicação, consumo, gerenciamento e monetização de APIs Web RESTful.

Isso significa que aplicativos de API compartilham com os aplicativos Web todos os recursos de hospedagem da Web da plataforma do Serviço de Aplicativo do Azure:

- Aplicação de patch automática do sistema operacional
- Segurança de nível corporativo
- Alta disponibilidade
- Dimensionamento automático e balanceamento de carga
- [WebJobs](../app-service-web/websites-webjobs-resources.md) para processamento em segundo plano
- Implantação rápida e fácil e várias opções de entrega contínua - Para obter informações sobre a variedade de opções de implantação disponíveis para aplicativos da API, consulte [Implantar um aplicativo Web no Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md). 

Os recursos adicionais que a plataforma de aplicativos de API fornece facilitam o desenvolvimento, a hospedagem e o consumo de APIs:

- **Traga sua API como ela é** - Use ASP.NET, Java, PHP, Node.js ou Python para suas APIs. Suas APIs podem aproveitar a plataforma de aplicativos da API sem alterações.

- **Controle de acesso simples** -Configure suas APIs para autenticação do Active Directory do Azure ou serviços de terceiros, como o Facebook e Twitter sem alterações ao seu código. Use uma sintaxe simplificada como código de autorização. Para obter mais informações, consulte [Proteger um aplicativo API](app-service-api-dotnet-add-authentication.md).

- **Conexão fácil com plataformas SaaS** - [Aplicativos de API do conector](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) no Azure Marketplace são fornecidos pela Microsoft e terceiros para simplificar o código que você escreve para interagir com SalesForce, Office 365, Twitter, Facebook, Dropbox e muitos outros.

- **Acessar dados locais** - Aplicativos de API podem expor APIs que consomem dados de seu próprio centro de dados usando [conexões híbridas](../integration-hybrid-connection-overview.md) e [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

- **Consumo fácil** - Use o suporte integrado [Swagger](http://swagger.io/) para tornar suas APIs facilmente consumíveis por uma variedade de clientes. Ou use o SDK de aplicativos de API para gerar o código do cliente para suas APIs em uma variedade de linguagens incluindo C#, Java e Javascript.

- **Integração com aplicativos lógicos** -Aplicativos de API criados por você podem ser consumidos por aplicativos lógicos do Serviço de Aplicativo.

- **Integração do visual Studio** -Ferramentas dedicadas no Visual Studio simplificam o trabalho de [criar](app-service-dotnet-create-api-app.md), [implantar](app-service-dotnet-deploy-api-app.md), [depurar](app-service-dotnet-remotely-debug-api-app) e gerenciar aplicativos de API.

Em breve, a plataforma de aplicativos da API também criará um rico ecossistema de APIs, facilitando o compartilhamento do código:

- **Marketplaces públicos e privados** - O [Azure Marketplace](http://azure.microsoft.com/marketplace/) facilitará a localização e implantação de seus aplicativos de API pré-empacotados de assinatura do Azure desenvolvidos pela Microsoft e terceiros. E você poderá empacotar e publicar seus próprios aplicativos de API que desenvolve, para que outros desenvolvedores possam implantá-los em suas assinaturas do Azure. Quando você publicar suas APIs no Azure Marketplace, poderá torná-las visíveis apenas para outros membros de sua organização. 

- **Implantação automática de dependência** - Sempre que você implantar um aplicativo de API do Marketplace na assinatura do Azure, o Azure implantará automaticamente aplicativos de API dependentes e criará os recursos necessários. Um pacote do aplicativo de API especificará os aplicativos de API do quais ele depende e os recursos do Azure que ele necessita.

- **Atualizações automáticas** - Quando você atualizar o código para um de seus pacotes de aplicativo de API que compartilhou, poderá enviar a atualização por push para qualquer pessoa que tenha instalado e esteja executando seu aplicativo de API. Isso funcionará para alterações de espaço contínuo e usuários que optaram por receber atualizações.

Muitos desses recursos, como marketplace público e atualizações automáticas, já estão disponíveis para aplicativos de API fornecidos pela Microsoft.

## Conceitos de aplicativos de API ##

- **Gateway** - Um aplicativo Web que lida com funções de administração de API e autenticação para todos os aplicativos de API em um grupo de recursos. 
- **Swagger** - Uma estrutura para a documentação interativa e descoberta de uma API RESTful, usada por padrão em aplicativos de API. Para obter mais informações, consulte [http://swagger.io/](http://swagger.io/).
- **Conector** - Um tipo de aplicativo de API que facilita a conexão com plataformas de SaaS como Salesforce e Office 365. Para obter mais informações, consulte [O que são conectores e aplicativos de API do BizTalk](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md).
- **Gatilho** - A API REST que os [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para iniciar um processo de fluxo de trabalho quando determinada condição é atendida. Por exemplo, um aplicativo de API pode fornecer um método que o aplicativo lógico chama periodicamente para procurar uma frase determinada em um feed do Twitter. Para obter mais informações, consulte [Gatilhos de aplicativo de API](app-service-api-dotnet-triggers.md).
- **Ação** - Uma API REST que [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para processar dados depois que um fluxo de trabalho foi iniciado por um gatilho. Por exemplo, um aplicativo de API pode fornecer um método que chama o aplicativo lógico para responder a um tweet encontrada pelo gatilho Twitter. Ações são métodos de API expostos por uma definição de API de Swagger.

## Introdução

Para começar a usar os aplicativos de API, siga [Criar um tutorial de aplicativo de API](app-service-dotnet-create-api-app.md).

Para ver uma lista de problemas conhecidos com aplicativos de API, consulte [este artigo no fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).


<!--HONumber=54--> 