<properties 
   pageTitle="Gerenciamento de Parceiros Comerciais do BizTalk" 
   description="Gerenciamento de Parceiros Comerciais do BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/19/2015"
   ms.author="rajram"/>

#Gerenciamento de Parceiros Comerciais do BizTalk
O serviço de Gerenciamento de Parceiros Comerciais (TPM) do Microsoft Azure permite definir e manter relações entre empresas, como parceiros e contratos, junto com artefatos associados, como esquemas e certificados. Essas relações então podem ser aplicadas por serviços de API relacionados, como X12, EDIFACT e AS2.

O Aplicativo de API TPM é o requisito básico para o conector de AS2 e os aplicativos de API X12 ou EDIFACT.

##Pré-requisitos
- Banco de dados vazio do SQL Azure - você precisa criar um banco de dados do SQL Azure vazio para depois criar um novo aplicativo de API do TPM.

##Entendendo parceiros, contratos e perfis
Para saber mais sobre o contrato de parceiro comercial, clique [aqui][1]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO8-->