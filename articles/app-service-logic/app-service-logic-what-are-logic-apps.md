<properties 
	pageTitle="O que são aplicativos lógicos?" 
	description="Saiba mais sobre os aplicativos lógicos do Serviço de Aplicativo" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/12/2016"
	ms.author="klam"/>

#O que são aplicativos lógicos?

| Referência rápida |
| --------------- |
| [Linguagem de definição de aplicativos lógicos](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentação do conector de aplicativos lógicos](../connectors/apis-list.md) |
| [Fórum de aplicativos lógicos](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurelogicapps) |

Os Aplicativos Lógicos fornecem uma maneira de simplificar e implementar processos de negócios complexos. Fornece um designer visual para modelar e automatizar o processo de negócios como uma série de etapas conhecidas como um fluxo de trabalho. Você pode projetar fluxos de trabalho para que eles sejam iniciados por meio de um gatilho e, em seguida, executem cada uma de suas etapas. Cada etapa invoca uma API enquanto cuida com segurança da autenticação e das práticas recomendadas, como ponto de verificação e execução durável.

As vantagens de usar Aplicativos Lógicos incluem o seguinte:

- Economizar tempo na criação de processos complexos usando ferramentas de design fáceis de entender
- Implementar facilmente tarefas que, de outro modo, seriam difíceis de implementar no código
- Introdução rápida por meio de modelos
- Personalizar seu Aplicativo Lógico usando APIs
- Conectar sistemas diferentes
- Monetizar seu Aplicativo Lógico no mercado
- Iniciar com facilidade e crescer conforme necessário

Os Aplicativos Lógicos são uma iPaaS (Plataforma de integração como um Serviço) totalmente gerenciada que permite que os desenvolvedores não precisem se preocupar com trabalho extra para lidar com hospedagem, escalabilidade, disponibilidade e gerenciamento.

O mais importante é que os Aplicativos Lógicos podem ser combinados a [Conectores Gerenciados][managedapis] internos para ajudar a solucionar com facilidade até mesmo cenários de integração complicados:

![Designer de aplicativo de fluxo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Conforme mencionado, com Aplicativos Lógicos, você pode automatizar processos de negócios. Aqui estão alguns exemplos:
 
* Você pode replicar automaticamente novos registros em seu banco de dados SQL e enviar mensagens de email para a recepção.
* Ou localizar automaticamente tweets negativos e enviá-los para um canal de margem de atraso.
* Quando um arquivo for criado em um servidor FTP, analise o arquivo, adicione novos registros ao Dynamics CRM e crie um item em uma lista do SharePoint.

Cenários como esses podem ser configurados inteiramente por meio do designer visual e sem escrever uma única linha de código sequer. Introdução [criação de seu aplicativo lógico agora][create].

## Por que aplicativos lógicos?

Se você quer automatizar qualquer processo de negócios (por exemplo, localizar tweets negativos e postar no canal interno do slack ou replicar novos registros de clientes do SQL assim que chegarem em seu sistema CRM), os Aplicativos Lógicos facilitam a integração de fontes de dados diferentes da nuvem para o local. Confira nosso [conectores gerenciados][managedapis] para obter mais inspiração e [comece][create] a usá-los agora para ver o que você pode fazer.

Além disso, com a [conta do Enterprise Integration][biztalk], você pode dimensionar para cenários de integração desenvolvidos com a capacidade de [mensagens XML][xml], [gerenciamento de parceiros comerciais][tpm] e muito mais.

- **Ferramentas de design fáceis de usar** - aplicativos lógicos podem ser projetados de ponta a ponta no navegador. Inicie com um gatilho - de um agendamento simples a sempre que aparecer um tweet exibida sobre a sua empresa. Então orquestre qualquer número de ações usando a galeria avançada de conectores.

- **Compor SaaS facilmente** - tarefas de composição uniformes fáceis de descrever são difíceis de implementar no código. Aplicativos Lógicos tornam muito fácil se conectar a sistemas diferentes. Deseja criar uma tarefa no seu software CRM baseada na atividade de suas contas do Facebook ou do Twitter? Deseja conectar sua solução de marketing na nuvem ao seu sistema de cobrança local? Aplicativos lógicos são a maneira mais rápida e confiável de fornecer soluções para esses problemas.

- **Começo rápido com modelos** - para ajudá-lo a começar, fornecemos uma [galeria de modelos][templates] que permitem que você crie rapidamente algumas soluções comuns. De soluções avançadas do BizTalk a conectividade de SaaS simples, e até mesmo algumas que são apenas “para diversão” - a Galeria é a maneira mais rápida de compreender o poder dos aplicativos lógicos.

- **Extensibilidade embutida** - não encontra o conector de que precisa? Os Aplicativos Lógicos são projetados para funcionar com aplicativos de API; você pode facilmente criar seu próprio aplicativo de API a ser usado como uma API personalizada. Crie um novo aplicativo apenas para você ou compartilhe e monetize no marketplace.

- **Potência real de integração** - inicie com facilidade e cresça conforme necessário. Aplicativos lógicos podem facilmente aproveitar o poder do BizTalk, a solução de integração da Microsoft líder do setor para habilitar os profissionais de integração a compilar as soluções de que precisam. Saiba mais sobre o [Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Conceitos de aplicativo lógico

A seguir estão algumas das principais partes que compõem a experiência de aplicativos lógicos.

- **Fluxo de trabalho** - aplicativos lógicos proporcionam uma maneira gráfica de modelar seus processos de negócios como uma série de etapas ou um fluxo de trabalho.
- **Conectores Gerenciados** - seus aplicativos lógicos precisam de acesso a dados e serviços. Conectores gerenciados são criadas especificamente para ajudá-lo a se conectar e a trabalhar com seus dados. Confira a lista de conectores disponíveis agora em [conectores gerenciados][managedapis].
- **Gatilhos** - alguns conectores gerenciados também podem atuar como um gatilho. Um gatilho inicia uma nova instância de um fluxo de trabalho com base em um evento específico, como a chegada de um email ou uma alteração em sua conta de Armazenamento do Azure.
-  **Ações** - cada etapa após o gatilho em um fluxo de trabalho é chamada de uma ação. Cada ação normalmente é mapeada para uma operação em seu conector gerenciado ou aplicativos de API personalizados.
- **Enterprise Integration Pack** - para cenários de integração mais avançados, os Aplicativos Lógicos incluem recursos do BizTalk. O BizTalk é a plataforma de integração da Microsoft líder do setor. Os conectores do Enterprise Integration Pack permitem que você inclua facilmente validação, transformação e muito mais em seus fluxos de trabalho de Aplicativo Lógico.

## Introdução  

 - Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico][create].
 - [Veja exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
 - [Você pode automatizar processos de negócios com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/T694)
 - [Aprenda a integrar seus sistemas com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/P462)
- Para obter mais informações a respeito da plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][appservice].

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0713_2016-->