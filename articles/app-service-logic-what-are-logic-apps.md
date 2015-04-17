<properties 
	pageTitle="O que são aplicativos lógicos?" 
	description="Saiba mais sobre os aplicativos lógicos do Serviço de Aplicativo" 
	authors="joshtwist" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#O que são aplicativos lógicos?

O Serviço de Aplicativo do Azure é uma oferta de plataforma como serviço (PaaS) totalmente gerenciada para desenvolvedores profissionais que traz um conjunto rico de recursos para cenários Web, móvel e de integração.  Os aplicativos lógicos são uma parte do pacote Serviço de Aplicativo e permitem que qualquer usuário técnico ou desenvolvedor automatize a execução de processos de negócios por meio de um designer visual fácil de usar.

O melhor de tudo é que os aplicativos lógicos podem ser combinados com aplicativos de API e conectores do nosso Marketplace para ajudar a solucionar inclusive cenários de integração complicados com facilidade.

![Flow app designer](./media/app-service-learn-about-flows-preview/Designer.png)

##A explosão de SaaS, PaaS e Híbrida

A era da nuvem traz uma explosão do uso de sistemas SaaS e PaaS, colocando cada vez maior tensão aos desenvolvedores em todos os lugares.  Enquanto isso, pendências de aplicativo da TI estão aumentando, e essas soluções heterogêneas distribuídas impõem novos desafios de integração.  Além disso, as empresas precisam utilizar seus dados e serviços locais em suas soluções, e precisam fazer isso com segurança.

Criar soluções que abranjam esses sistemas representa um desafio incomum que é demorado e sujeito a erros para as equipes de desenvolvimento.

##Por que aplicativos lógicos?

Aplicativos lógicos permitem que os desenvolvedores projetem fluxos de trabalho que articulem a intenção por meio de um gatilho e uma série de etapas, cada uma chamando um aplicativo de API do Serviço de Aplicativo ao mesmo tempo em que cuida da autenticação e práticas recomendadas, como execução durável, com segurança.

- **Ferramentas de design fáceis de usar** - aplicativos lógicos podem ser projetados de ponta a ponta no navegador.  Inicie com um gatilho - de um agendamento simples a sempre que aparecer um tweet exibida sobre a sua empresa.  Então orquestre qualquer número de ações usando a galeria avançada de conectores.

- **Compor SaaS facilmente** - tarefas de composição uniformes fáceis de descrever são difíceis de implementar no código.  Aplicativos lógicos tornam muito fácil se conectar a sistemas diferentes.  Deseja criar uma tarefa no seu software CRM baseada na atividade de suas contas do Facebook ou do Twitter?  Deseja conectar sua solução de marketing na nuvem ao seu sistema de cobrança local?  Aplicativos lógicos são a maneira mais rápida e confiável de fornecer soluções para esses problemas.

- **Começo rápido com modelos** - para ajudá-lo a começar, fornecemos uma galeria de modelos que permitem que você crie rapidamente algumas soluções comuns.  De soluções avançadas do BizTalk a conectividade de SaaS simples, e até mesmo algumas que são apenas 'for fun' - a Galeria é a maneira mais rápida de compreender o poder dos aplicativos lógicos.

- **Extensibilidade embutida** - não encontra o conector de que precisa?  Aplicativos lógicos fazem parte do pacote do Serviço de Aplicativo e são projetados para funcionar com aplicativos de API; você pode facilmente criar seu próprio aplicativo de API a ser usado como um conector.  Compile um novo aplicativo para você ou compartilhe e monetize no marketplace.

- **Potência real de integração** - inicie com facilidade e cresça conforme necessário.  Aplicativos lógicos podem facilmente aproveitar o poder do BizTalk, a solução de integração da Microsoft líder do setor para habilitar os profissionais de integração a compilar as soluções de que precisam.  Saiba mais sobre os [recursos do BizTalk fornecidos com os Serviços de Aplicativos][biztalk].

## Conceitos de aplicativo lógico

A seguir estão algumas das principais partes que compõem a experiência de aplicativos lógicos. 

- **Fluxo de trabalho** - aplicativos lógicos proporcionam uma maneira gráfica de modelar seus processos de negócios como uma série de etapas ou um fluxo de trabalho.
- **Conectores** - seus aplicativos lógicos precisam de acesso a dados e serviços.  Um conector é um tipo especial de aplicativo de API.  É criado especificamente para ajudá-lo a se conectar a trabalhar com seus dados.  Saiba mais em [O que são Conectores][biztalk].
- **Gatilhos** - alguns conectores também podem atuar como um gatilho.  Um gatilho inicia uma nova instância de um fluxo de trabalho com base em um evento específico, como a chegada de um email ou uma alteração em sua conta de Armazenamento do Azure.
- **BizTalk** - para cenários mais avançados de integração, Serviços de Aplicativos do Azure incluem recursos do Biztalk.  O BizTalk é a plataforma de integração da Microsoft líder do setor.  Os aplicativos de API do BizTalk permitem que você inclua facilmente validação, transformação, regras e mais em seus fluxos de trabalho de aplicativo lógico.  Saiba mais em [O que são Aplicativos de API do BizTalk][biztalk].

## Introdução

Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico][create].

Para obter mais informações sobre a plataforma de serviços de aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md




<!--HONumber=49-->