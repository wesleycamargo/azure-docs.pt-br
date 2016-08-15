<properties 
	pageTitle="Visão geral de parceiros e do Enterprise Integration Pack | Serviço de Aplicativo do Microsoft Azure" 
	description="Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# Saiba mais sobre contratos e o Enterprise Integration Pack

## Visão geral
Os contratos são a base das comunicações de B2B (business-to-business), permitindo que as entidades de negócios se comuniquem diretamente usando protocolos padrão da indústria.

## O que é um contrato?

Um contrato, no que diz respeito ao Enterprise Integration Pack, é um acordo de comunicação entre parceiros comerciais B2B. Um contrato se baseia na comunicação que os dois parceiros desejam atingir e é específico com relação a transporte ou protocolo.

A integração corporativa dá suporte a três padrões de protocolo/transporte:

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) (em breve)

## Por que usar contratos
Alguns dos benefícios comuns do uso de contratos são:
- Permite que diferentes organizações e empresas troquem informações em um formato conhecido.
- Melhora a eficiência ao conduzir transações B2B
- Fácil de criar, gerenciar e usar durante a criação de aplicativos de integração corporativa

## Como criar contratos
- [Criar um contrato AS2](./app-service-logic-enterprise-integration-as2.md)
- [Criar um contrato X12](./app-service-logic-enterprise-integration-x12.md)

## Como usar um contrato
Depois de criar um contrato, você pode usá-lo por meio do Portal do Azure para criar [Aplicativos lógicos](./app-service-logic-what-are-logic-apps.md "Saiba mais sobre os Aplicativos lógicos") com recursos de B2B.

## Como editar um contrato
Você pode editar qualquer contrato seguindo essas etapas:
1. Selecione a conta de Integração que contém o contrato que você deseja modificar.
2. Selecione o bloco **Contratos**
3. Selecione o contrato que você deseja modificar na folha **Contratos**
4. Selecione **Editar** no menu acima
5. No menu Editar que é aberto, faça suas alterações e selecione o botão **OK** para salvá-las

## Como excluir um contrato
Você pode excluir qualquer contrato seguindo estas etapas de dentro da conta de integração que contém o contrato que você deseja excluir:
1. Selecione o bloco **Contratos**
2. Selecione o contrato que você deseja excluir na folha **Contratos**
3. Selecione **Excluir** no menu acima
4. Confirme que você realmente deseja excluir o contrato
5. Observe que o contrato não está mais listado na folha Contratos
 

## Próximas etapas
- [Criar um contrato AS2](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0803_2016-->