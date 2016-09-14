<properties
	pageTitle="O que fazer no caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure | Microsoft Azure"
	description="Saiba o que fazer no caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="sumedhb;aglick"/>


# Redundância e disponibilidade de Cofre de Chaves do Azure

O Cofre de Chaves do Azure tem várias camadas de redundância, a fim de garantir que seus segredos e chaves permaneçam disponíveis para seu aplicativo até mesmo se os componentes individuais do serviço falharem.

O conteúdo de seu cofre de chaves é replicado na região, bem como em uma região secundária a pelo menos 150 milhas de distância, mas na mesma região geográfica. Isso mantém a alta durabilidade de seus segredos e chaves.

Se os componentes individuais dentro do serviço de Cofre de Chaves falharem, componentes alternativos dentro da região interferem para atender à sua solicitação, de modo a garantir que não haja degradação da funcionalidade. Você não precisa executar qualquer ação para disparar esse recurso. Ele ocorrerá automaticamente de modo transparente para você.

No eventual caso de uma região inteira do Azure ficar indisponível, as solicitações que você faz do Cofre de Chaves do Azure nessa região são roteadas automaticamente ("failover") para uma região secundária. Quando a região primária estiver disponível novamente, as solicitações serão roteadas de volta ("failback") para a região primária. Novamente, você não precisa executar qualquer ação, pois isso acontecerá de modo automático.

Há algumas advertências às quais você deve estar atento:

* No caso de um failover de região, pode levar alguns minutos para o serviço executar failover. Solicitações feitas durante esse período podem falhar até que o failover seja concluído.
* Após a conclusão de um failover, o cofre de chaves estará no modo ___somente leitura___. As solicitações permitidas nesse modo são:
 * listar cofres de chave
 * obter propriedades de cofres de chave
 * listar segredos
 * obter segredos
 * listar chaves
 * obter (propriedades das) chaves
 * encrypt
 * descriptografar
 * encapsular
 * desencapsular
 * verificar
 * assinar
 * backup
* Após o failback de um failover, todos os tipos de solicitação (ou seja, solicitações de leitura ___e___ de gravação) são disponibilizados.

<!---HONumber=AcomDC_0831_2016-->