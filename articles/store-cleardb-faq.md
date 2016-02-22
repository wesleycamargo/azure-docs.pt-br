<properties
	pageTitle="Perguntas frequentes sobre bancos de dados MySql ClearDB com o Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Respostas a perguntas comuns sobre o uso de bancos de dados MySQL ClearDB com o Serviço de Aplicativo do Azure."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Perguntas frequentes sobre bancos de dados MySql do ClearDB com o Serviço de Aplicativo do Azure

Estas perguntas frequentes respondem a dúvidas comuns sobre como usar e adquirir os bancos de dados MySQL do ClearDB para aplicativos Web do Azure.

## Quais são as minhas opções para o MySQL no Azure?

Você tem várias opções:

* [Banco de dados MySQL compartilhado do ClearDB](/marketplace/partners/cleardb/databases/)

* [Clusters MySQL Premium do ClearDB](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster MySQL em execução em uma VM do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Instância única do MySQL em execução em uma VM do Azure](virtual-machines/virtual-machines-mysql-windows-server-2008r2.md)

O ClearDB é um serviço de hospedagem de MySQL e gerencia a infraestrutura do MySQL para você. Quando você executa seu próprio cluster ou banco de dados do MySQL em uma máquina virtual do Azure, precisa configurar o servidor MySQL e mantê-lo atualizado com patches.

## Preciso de um cartão de crédito para o modelo aplicativo Web + MySQL no Azure Marketplace?

Isso depende do tipo de assinatura que você está usando. Veja a seguir alguns tipos de assinatura normalmente usadas:

* [Pré-pago](/offers/ms-azr-0003p/): exige um cartão de crédito, e quando você comprar um banco de dados MySQL pago seu cartão de crédito será cobrado.

* [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui créditos para uso com os serviços do Microsoft Azure, mas não permite a compra de recursos de terceiros. Para comprar serviços de terceiros ou um banco de dados MySQL pago, você precisa usar uma assinatura habilitada para cartão de crédito. Para aplicativos Web, você pode criar um banco de dados MySQL do ClearDB GRATUITO.

* [Assinatura do MSDN](/pricing/member-offers/msdn-benefits/) e **Desenvolvimento/Teste MSDN Pré-Pago**: semelhante à Avaliação gratuita, uma assinatura do MSDN exige que você tenha um cartão de crédito para comprar uma solução paga do MySQL da ClearDB.

* [Enterprise Agreement (EA)](/pricing/enterprise-agreement/): os clientes de EA recebem em uma fatura consolidada e separada uma cobrança por seu EA a cada trimestre referente a todas as suas compras (de terceiros) no Azure Marketplace. Você será cobrado fora do compromisso monetário por qualquer compra no marketplace. Observe que, no momento, o Armazenamento do Azure não está disponível para clientes inscritos no Azerbaijão, na Croácia, na Noruega e em Porto Rico.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): você só pode criar bancos de dados ClearDB GRATUITOS para Aplicativos Web. Não há limite para o número de bancos de dados MySQL ClearDB Gratuitos que você pode criar. Observe que os bancos de dados Gratuitos não devem ser usados para aplicativos Web de produção, pois esse serviço é destinado apenas para avaliação.

## Por que recebi uma cobrança de US$ 3,50 por um aplicativo Web + MySQL no Azure Marketplace?

A opção de banco de dados padrão é Titan, que custa US$ 3,50. Não mostramos o custo durante a criação do banco de dados e talvez você compre um banco de dados que não pretendia por engano. Estamos tentando encontrar uma maneira de melhorar a experiência, mas até lá, você deverá verificar todos os tipos de preços selecionados para o aplicativo Web e o banco de dados antes de clicar em **Criar** e iniciar a implantação dos recursos.

## Estou executando o MySQL em minha própria máquina virtual do Azure. Posso conectar meu aplicativo Web do Azure ao meu banco de dados?

Sim. Você pode conectar seu aplicativo Web ao seu banco de dados desde que sua VM do Azure tenha acesso remoto ao seu aplicativo Web. Clique aqui para obter detalhes.

## Em quais países os clusters MySQL do ClearDB Premium têm suporte?

[Os clusters MySQL do ClearDB Premium](/marketplace/partners/cleardb-clusters/cluster/) estão disponíveis em todas as regiões do Azure no mundo, com exceção da Índia, da Austrália, do Sul do Brasil e da China.

## Posso criar um novo cluster antes de criar um banco de dados com a solução de cluster ClearDB Premium?

Não, não há suporte para a criação de clusters ClearDB vazios. O Portal do Azure permite que você crie bancos de dados em um cluster, o que permite a criação de um novo cluster ao mesmo tempo.

## Serei avisado se tentar excluir um banco de dados MySQL do ClearDB que esteja sendo usado por um de meus aplicativos?

Não, o Azure não avisará se você excluir uma compra feita no Marketplace da qual seu aplicativo dependa.

## Em quais regiões eu posso criar os bancos de dados ClearDB?

O Azure Marketplace não está disponível para clientes inscritos no Azerbaijão, na Croácia, na Noruega e em Porto Rico. O ClearDB não está disponível nessas regiões.

## Qual tipo de preço eu devo escolher para um aplicativo Web de produção e banco de dados?

Use Basic ou um tipo de preço mais alto para aplicativos Web. Para o ClearDB, recomendamos um plano Saturn ou Jupiter. Examine os recursos e limitações de cada tipo de preço para os [aplicativos Web](/pricing/details/app-service/) e os [bancos de dados MySQL do ClearDB](/marketplace/partners/cleardb/databases/) e escolha aquele que atender às suas necessidades.

## Como atualizar meu banco de dados ClearDB de um plano para outro?

Você pode usar o [Assistente de Atualização do ClearDB](https://www.cleardb.com/store/azure/upgrade). Atualmente, não temos um caminho de atualização no portal do Azure.

## Quem devo contatar para obter suporte quando meu banco de dados ficar inativo?

Entre em contato com o [Suporte do ClearDB](https://www.cleardb.com/developers/help/support) para quaisquer problemas relacionados ao banco de dados. Esteja preparado para fornecer as informações de sua assinatura do Azure.

## Posso adquirir o WordPress Escalonável com uma assinatura do Enterprise Agreement (EA)?

O processo é o mesmo para qualquer assinatura. Acesse o Azure Marketplace no [portal do Azure](https://portal.azure.com/) e selecione [WordPress Escalonável](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress) para começar a criar o aplicativo. O WordPress Escalonável oferece suporte apenas aos tipos de preço Saturn e Jupiter do ClearDB, e seus créditos de EA irão para seu aplicativo Web em execução no tipo de preço Aplicativos Web Padrão e no banco de dados MySQL (compartilhado) ClearDB.[/marketplace/faq/](/marketplace/faq/) Você receberá uma cobrança em sua EA a cada trimestre com todas as compras na Store em uma fatura consolidada e separada.

## Posso transferir um banco de dados ClearDB de uma assinatura de cartão de crédito para uma assinatura de EA?

Os bancos de dados ClearDB existentes usarão o cartão de crédito associado às assinaturas existentes. Para usar uma assinatura de EA, você precisará migrar seus dados para um novo banco de dados:

* Compre um novo banco de dados ClearDB com sua assinatura do EA.
* Migre seus dados para o novo banco de dados.
* Atualize seu aplicativo para usar o novo banco de dados.
* Exclua o banco de dados ClearDB antigo.

Quando você cria um novo aplicativo Web com o MySQL (ClearDB) ou cria um banco de dados MySQL (ClearDB), a assinatura escolhida determina como você pagará pelo serviço. Observe que com uma assinatura de EA, não bloquearemos a aquisição dos serviços de terceiros, como ClearDB, no Portal do Azure. As assinaturas de EA são cobradas fora do Compromisso Monetário e são cobradas trimestralmente e mediante inadimplência. O cliente de EA precisa configurar um método de pagamento, como um cartão de crédito, a fim de pagar por quaisquer serviços de terceiros no marketplace.

## Onde posso ver os encargos para os recursos do ClearDB em uma assinatura de EA?

Para clientes diretos do EA, os encargos do Azure Marketplace ficam visíveis no Portal Empresarial. Observe que todas as compras e consumo no marketplace são faturadas fora do Compromisso monetário e são cobradas trimestralmente e mediante inadimplência. Os clientes de EA precisam pagar diretamente aos provedores de serviços de terceiros e podem fazer isso habilitando um método de pagamento, como um cartão de crédito, com suas contas EA.

Clientes indiretos do EA podem encontrar suas assinaturas do Azure Marketplace na página **Gerenciar Assinaturas** do Portal Empresarial, mas os preços ficam ocultos. Os clientes devem entrar em contato com seus LSPs para saber mais sobre encargos do Marketplace.

O acesso ao Azure Marketplace para serviços de terceiros pode ser gerenciado por seus administradores de registro do EA Azure. Eles podem desabilitar ou reabilitar o acesso a compras de terceiros no Armazenamento em Gerenciar Contas e Assinaturas na seção Contas no Portal Empresarial.

## Quem devo contatar para perguntas sobre minha fatura para serviços do ClearDB em minha assinatura do EA?

Contate o [Atendimento ao Cliente Corporativo](http://aka.ms/AzureEntSupport) sobre a cobrança em sua inscrição no EA. A Equipe de Suporte do Portal de EA responderá à sua pergunta ou ajudará a resolver o problema.

## Mais informações

[Perguntas frequentes sobre o Azure Marketplace](/marketplace/faq/)

<!---HONumber=AcomDC_0211_2016-->