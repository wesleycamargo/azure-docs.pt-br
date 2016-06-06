<properties
	pageTitle="O que fazer em caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure | Microsoft Azure"
	description="Saiba o que fazer em caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="danpl"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="sumedhb;aglick"/>
	

# Redundância e disponibilidade de Cofre de Chaves do Azure

O Cofre de Chaves do Azure tem várias camadas internas de redundância, para garantir que suas chaves e segredos permaneçam disponíveis para seu aplicativo até mesmo quando os componentes individuais do serviço falharem.

O conteúdo de seu cofre de chaves é replicado na região, bem como para uma região secundária a pelo menos 150 milhas de distância na mesma região geográfica. Isso garante uma durabilidade muito alta para suas chaves e segredos.

Se componentes individuais dentro do serviço de Cofre de Chaves falharem, componentes alternados dentro da região atuarão para atender à sua solicitação. Nesse caso, não há nenhuma degradação de funcionalidade. Você não precisa realizar nenhuma ação, pois isso acontece de modo automático e transparente para você.

No caso raro de toda a região do Azure estar indisponível, as solicitações feitas do Cofre de Chaves nessa região são roteadas automaticamente ("failover") para uma região secundária. Quando a região primária estiver disponível novamente, as solicitações serão roteadas de volta ("failback") para a região primária. Novamente, você não precisa realizar nenhuma ação, pois isso acontece de modo automático. Há algumas advertências às quais você deve estar atento:

  * no caso de um failover de região, pode levar alguns minutos para o serviço executar failover. Solicitações feitas durante esse tempo podem falhar até que o failover seja concluído.

  * Após um failover, o Cofre de Chaves está no modo ___somente leitura___. As solicitações às quais esse modo dá suporte são:
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

  * Após o failback de um failover ser realizado, todos os tipos de solicitação (ou seja, solicitações de leitura ___e___ gravação) ficam disponíveis.

<!---HONumber=AcomDC_0525_2016-->