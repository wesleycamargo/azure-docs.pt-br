<properties
	pageTitle="Como migrar aplicativos lógicos para a versão de esquema 2015-08-01-preview | Serviço de Aplicativo do Microsoft Azure"
	description="Você pode facilmente migrar aplicativos lógicos para a última versão do esquema. Basta seguir estas etapas."
	services="logic-apps"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="deonhe"/>

# Como migrar aplicativos lógicos para a versão de esquema 2015-08-01-preview

Para mover aplicativos lógicos existentes para o novo esquema, faça o seguinte:  
1. Abra o aplicativo lógico no portal do Azure  
2. Clique em Atualizar Esquema:

![Ícone de API][step1]

A página Atualizar Esquema exibe e fornece um link para um documento que fornece detalhes sobre os aperfeiçoamentos no novo esquema:

![Ícone de API][step2]

>[AZURE.NOTE] Quando você seleciona **Atualizar Esquema**, executamos automaticamente as etapas de migração e lhe fornecemos o código de saída. Você pode usar isso para atualizar sua definição. No entanto, siga as práticas recomendadas de codificação, como as descritas na seção **Práticas recomendadas** abaixo.

## Práticas recomendadas ao migrar aplicativos lógicos para a última versão do esquema:  

- Copie o script migrado para um novo aplicativo lógico - não substitua o antigo até concluir o teste e confirmar que o aplicativo migrado funciona conforme o esperado.
- Testar o aplicativo lógico **antes** de colocá-lo em produção
- Após a conclusão da migração, comece a atualizar os aplicativos lógicos para usar as [APIs gerenciadas](./apis-list.md) sempre que possível. Por exemplo, você pode começar a usar o Dropbox v2 em todos os casos em que usa o DropBox v1.


## O que vem a seguir
-  [Saiba como migrar manualmente os aplicativos lógicos](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!----HONumber=AcomDC_0824_2016-->
