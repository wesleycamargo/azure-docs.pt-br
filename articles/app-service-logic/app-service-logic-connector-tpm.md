<properties 
   pageTitle="Usando o Conector de Gerenciamento de Parceiros Comerciais do BizTalk em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure" 
   description="Como criar e configurar o Conector de Gerenciamento de Parceiros Comerciais do BizTalk ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure" 
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
   ms.date="08/23/2015"
   ms.author="rajram"/>

# Introdução ao Gerenciamento de Parceiros Comerciais do BizTalk e à adição dele ao seu Aplicativo Lógico
O serviço de Gerenciamento de Parceiros Comerciais (TPM) do BizTalk permite definir e manter relações entre empresas, como parceiros e contratos, junto com artefatos associados, como esquemas e certificados. Essas relações então podem ser aplicadas por serviços de API relacionados, como X12, EDIFACT e AS2.

O Aplicativo de API TPM é o requisito básico para o conector de AS2 e os Aplicativos de API X12 ou EDIFACT. Você pode adicionar o Gerenciamento de Parceiros Comerciais do BizTalk a seu fluxo de trabalho comercial e processar dados como parte de um fluxo de trabalho comercial dentro de um Aplicativo Lógico.

## Pré-requisitos
- Banco de dados vazio do SQL Azure - você precisa criar um banco de dados do SQL Azure vazio para depois criar um novo aplicativo de API do TPM.

## Entendendo parceiros, contratos e perfis
Para saber mais sobre o contrato de parceiro comercial, clique [aqui][1].

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=Oct15_HO3-->