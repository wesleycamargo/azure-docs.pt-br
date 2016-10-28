<properties 
	pageTitle="Saiba mais sobre o Conector de Mensagem de Codificação de AS2 do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure | Microsoft Azure" 
	description="Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Introdução para Codificar Mensagem AS2

Conecte-se à opção Codificar Mensagem AS2 para estabelecer confiabilidade e segurança ao transmitir mensagens. Ele fornece a assinatura digital, criptografia e confirmações por meio de MDN (notificações de disposição de mensagem), o que também leva a suporte para Não Repúdio.

## Criar a conexão

### Pré-requisitos

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* Uma Conta de Integração é necessária para usar o conector de Codificar Mensagem AS2. Consulte detalhes sobre como criar uma [Conta de Integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e um [contrato AS2](./app-service-logic-enterprise-integration-as2.md)

### Conecte-se à opção Codificar Mensagem AS2 usando as seguintes etapas:

1. [Criar um Aplicativo Lógico](./app-service-logic-create-a-logic-app.md) fornece um exemplo

2. Esse conector não tem gatilhos. Use outros gatilhos para iniciar o Aplicativo Lógico, como um gatilho de solicitação. No designer do Aplicativo Lógico, adicione um gatilho e uma ação. Selecione Mostrar APIs gerenciadas da Microsoft na lista suspensa e digite “AS2” na caixa de pesquisa. Selecione AS2 – Codificar Mensagem AS2

	![pesquisar AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Se ainda não tiver criado conexões com a Conta de Integração, você deverá fornecer os detalhes de conexão
	
	![criar conexão com a conta de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)

4. Insira os detalhes da Conta de Integração. As propriedades com um asterisco são obrigatórias

	| Propriedade | Detalhes |
	| --------   | ------- |
	| Nome da Conexão * | Digite um nome para a conexão |
	| Conta de Integração * | Insira o nome da Conta de Integração. Certifique-se de que sua Conta de Integração e que o Aplicativo Lógico estejam no mesmo local do Azure |

  	Uma vez concluída, os detalhes da conexão se parecerão com estes

  	![conexão de integração estabelecida](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)

5. Selecione **Criar**

6. Observe que a conexão foi criada. Forneça os identificadores AS2-From, AS2-To (conforme configurado no contrato) e detalhes do Corpo (o conteúdo da mensagem).

	![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## A opção Codificar AS2 faz o seguinte

* Aplica cabeçalhos HTTP/AS2
* Sinaliza mensagens de saída (se configurado)
* Criptografa mensagens de saída (se configurado)
* Compacta as mensagens (se configurado)

##Experimente

Por que não experimentá-lo? Clique [aqui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implantar um aplicativo lógico seu totalmente operacional usando os recursos AS2 dos Aplicativos Lógicos

## Próximas etapas

[Saiba mais sobre o Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

<!---HONumber=AcomDC_0914_2016-->