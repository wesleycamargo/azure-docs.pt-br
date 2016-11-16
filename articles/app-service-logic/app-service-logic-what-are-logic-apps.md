---
title: "O que são Aplicativos Lógicos?"
description: "Saiba mais sobre os Aplicativos Lógicos do Serviço de Aplicativo"
author: kevinlam1
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07765c05-72a6-4169-a8ab-f6420bfbaf07
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/12/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 44941876c976fdad0303b3c638ed6a3811136634


---
# <a name="what-are-logic-apps"></a>O que são Aplicativos Lógicos?
Os Aplicativos Lógicos fornecem uma maneira de simplificar e implementar integrações escalonáveis e fluxos de trabalho na nuvem. Eles fornecem um designer visual para modelar e automatizar o processo como uma série de etapas conhecidas como fluxo de trabalho.  Há [muitos conectores](../connectors/apis-list.md) locais e na nuvem para integrar rapidamente serviços e protocolos.  Um aplicativo lógico começa com um gatilho (como 'Quando uma conta é adicionada ao Dynamics CRM') e, após ser disparado, pode iniciar muitas ações de combinações, conversões e lógica de condição.

As vantagens de usar Aplicativos Lógicos incluem o seguinte:  

* Menos tempo usado para criação de processos complexos devido ao uso de ferramentas de design fáceis de entender
* Implementação perfeita de padrões e fluxos de trabalho, que seriam difíceis de implementar no código
* Introdução rápida devido aos modelos
* Personalização do seu aplicativo lógico com seus próprios códigos, APIs e ações personalizados
* Conexão e sincronização com sistemas distintos locais e na nuvem
* Criado com base no BizTalk Server, Gerenciamento de API, Azure Functions e Barramento de Serviço do Azure com excelente suporte à integração

Os Aplicativos Lógicos são uma iPaaS (Plataforma de integração como um Serviço) totalmente gerenciada, que permite aos desenvolvedores não se preocuparem com hospedagem, escalabilidade, disponibilidade e gerenciamento.  Os Aplicativos Lógicos serão escalados verticalmente de forma automática para atender à demanda.

![Designer de aplicativo de fluxo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Conforme mencionado, com Aplicativos Lógicos, você pode automatizar processos de negócios. Aqui estão alguns exemplos:  

* Mover arquivos carregados para um servidor FTP no Armazenamento do Azure
* Processar e rotear solicitações entre sistemas locais e na nuvem
* Monitorar todos os tweets sobre determinado tópico, analisar o sentimento e criar alertas e tarefas para itens que precisam de acompanhamento.

Cenários como esses podem ser configurados inteiramente por meio do designer visual e sem escrever uma única linha de código sequer. [Comece a criar seu aplicativo lógico agora][create].  Uma vez gravado, um aplicativo lógico pode ser [rapidamente implantado e reconfigurado](app-service-logic-create-deploy-template.md) em vários ambientes e regiões.

## <a name="why-logic-apps"></a>Por que Aplicativos Lógicos?
Os Aplicativos Lógicos proporcionam velocidade e escalabilidade ao espaço de integração empresarial.  A facilidade de uso do designer, a variedade de disparadores e ações disponíveis e as ferramentas de gerenciamento poderosas simplificam mais do que nunca a centralização das APIs.  À medida que as empresas adotam a digitalização, os Aplicativos Lógicos permitem a conexão entre sistemas herdados e inovadores.

Além disso, com a [Conta do Enterprise Integration][biztalk], você pode dimensionar para cenários de integração desenvolvidos com a potência de um [sistema de mensagens XML][xml], [gerenciamento de parceiros comerciais][tpm] e muito mais.

* **Ferramentas de design fáceis de usar** – os Aplicativos Lógicos podem ser projetados de ponta a ponta no navegador ou com ferramentas do Visual Studio. Inicie com um gatilho – desde um agendamento simples até quando uma questão do GitHub é criada. Então orquestre qualquer número de ações usando a galeria avançada de conectores.
* **Conecte APIs facilmente** – tarefas de composição uniformes fáceis de descrever são difíceis de implementar no código. Aplicativos Lógicos tornam muito fácil se conectar a sistemas diferentes. Deseja conectar sua solução de marketing na nuvem ao seu sistema de cobrança local? Quer centralizar as mensagens entre APIs e sistemas com um Barramento de Serviço Corporativo? Aplicativos lógicos são a maneira mais rápida e confiável de fornecer soluções para esses problemas.
* **Início rápido com modelos** – para ajudá-lo a começar, fornecemos uma [galeria de modelos][templates] que permitem que você crie rapidamente algumas soluções comuns. De soluções avançadas de B2B a conectividade de SaaS simples, e até mesmo algumas que são apenas “para diversão” – a Galeria é a maneira mais rápida de começar a usar a capacidade dos Aplicativos Lógicos.
* **Extensibilidade embutida** – não encontra o conector de que precisa? Os Aplicativos Lógicos são projetados para funcionar com suas próprias APIs e código. Você pode facilmente criar seu próprio aplicativo de API para usar como um conector personalizado ou chamar uma [Função do Azure](https://functions.azure.com) para executar trechos de código sob demanda. 
* **Potência real de integração** – inicie com facilidade e cresça conforme necessário. Aplicativos lógicos podem facilmente aproveitar o poder do BizTalk, a solução de integração da Microsoft líder do setor para habilitar os profissionais de integração a compilar as soluções de que precisam. Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceitos de Aplicativo Lógico
A seguir estão algumas das principais partes que compõem a experiência de Aplicativos Lógicos. 

* **Fluxo de trabalho** – os Aplicativos Lógicos proporcionam uma maneira gráfica de modelar seus processos de negócios como uma série de etapas ou um fluxo de trabalho.
* **Conectores Gerenciados** – seus Aplicativos Lógicos precisam de acesso a dados e serviços. Conectores gerenciados são criadas especificamente para ajudá-lo a se conectar e a trabalhar com seus dados. Confira a lista de conectores disponíveis agora em [conectores gerenciados][managedapis].
* **Gatilhos** – alguns conectores gerenciados também podem atuar como um gatilho. Um gatilho inicia uma nova instância de um fluxo de trabalho com base em um evento específico, como a chegada de um email ou uma alteração em sua conta de Armazenamento do Azure.
* **Ações** – cada etapa após o gatilho em um fluxo de trabalho é chamada de uma ação. Cada ação normalmente é mapeada para uma operação em seu conector gerenciado ou aplicativos de API personalizados.
* **Enterprise Integration Pack** – para cenários de integração mais avançados, os Aplicativos Lógicos incluem recursos do BizTalk. O BizTalk é a plataforma de integração da Microsoft líder do setor. Os conectores do Enterprise Integration Pack permitem que você inclua facilmente validação, transformação e muito mais em seus fluxos de trabalho de Aplicativo Lógico.

## <a name="getting-started"></a>Introdução
* Para começar com aplicativos lógicos, siga o tutorial [Criar um Aplicativo Lógico][create].  
* [Veja exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
* [Você pode automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Aprenda a integrar seus sistemas com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Nov16_HO2-->


