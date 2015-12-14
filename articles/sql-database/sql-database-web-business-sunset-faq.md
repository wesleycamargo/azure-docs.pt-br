<properties 
   pageTitle="Últimas perguntas frequentes do Banco de Dados do Azure Web e Business Edition | Microsoft Azure"
   description="Descubra quando os bancos SQL Web e Business do Azure e Business serão desativados e saiba mais sobre os recursos e funcionalidades das novas camadas de serviço."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="sstein" />

# Últimas perguntas frequentes de Web e Business Edition

Os bancos de dados SQL Business e Web do Azure foram desativados. As camadas Basic, Standard, Premium e Elástica substituem os bancos de dados Web e Business obsoletos.

Para ajudá-lo com a atualização de bancos de dados Web e Business, o serviço Banco de Dados SQL recomenda um nível de desempenho e uma camada de serviço adequados (tipo de preço) para cada banco de dados baseados na sua carga de trabalho histórica.

**Para obter recomendações de tipo de preço:**

- [Atualização para o Banco de Dados SQL V12 usando o Portal do Azure](sql-database-upgrade-server-portal.md)
- [Atualização para o Banco de Dados SQL V12 com o PowerShell](sql-database-upgrade-server-powershell.md)
- [Alterar o tipo de preço de um banco de dados Web ou Business](sql-database-service-tier-advisor.md)
 


## Por que o Portal do Azure mostra os meus bancos de dados das versões Web e Business como obsoletos?

Porque os bancos de dados das versões Web e Business não estarão disponíveis depois de setembro de 2015; o portal rotulou os bancos de dados comerciais e da Web como Desativados. Os bancos de dados comerciais e da Web ainda podem ser provisionados e gerenciados como de costume, mas o rótulo Desativado é um lembrete de que é melhor usar as camadas de serviço Basic, Standard ou Premium para novos bancos de dados. O rótulo desativado também fornece um lembrete de que qualquer banco de dados Web e Business deve ser atualizado para Premium, Basic ou Standard. Para obter informações detalhadas sobre como atualizar bancos de dados Web ou Business existentes para novas camadas de serviço, consulte [Atualizar banco de dados SQL Web/Business para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md).

## Que nova camada de serviço é a melhor opção para atualizar meu banco de dados existente Web ou Business?

A seleção de uma nova camada de serviço e de um nível de desempenho apropriados para seu banco de dados Web ou Business existente depende dos requisitos específicos de desempenho e de recursos para seu aplicativo.

Use as recomendações de tipo de preço descritas acima ou, para obter informações detalhadas para ajudá-lo a selecionar uma nova camada de serviço apropriado, consulte [Atualizar banco de dados SQL Web/Business para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md).

## Por que a Microsoft está introduzindo novas camadas de serviço?

Com base nos comentários dos clientes, o banco de dados SQL do Azure está introduzindo novas camadas de serviço para ajudar os clientes a dar suporte a cargas de trabalho de banco de dados relacional mais facilmente. As novas camadas foram projetadas para oferecer desempenho previsível entre sete níveis, de demandas de aplicativo leves às pesadas. Além disso, as novas camadas oferecem uma variedade de recursos da continuidade dos negócios, um SLA de tempo de ativação mais sólido, tamanhos maiores de banco de dados por um menor preço e uma experiência de cobrança aprimorada.

## Onde posso saber mais sobre as novas camadas de serviço?

Para obter informações detalhadas sobre os novos níveis de serviço e o modelo de desempenho, consulte [Camadas de serviço](sql-database-service-tiers.md). Para obter informações de preço para novas camadas de serviço, consulte [Preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Quais recursos ou funcionalidades não estarão disponível em Basic, Standard e Premium?

O recurso Federações será desativado com as versões Web e Business. Os clientes que precisam escalar seus bancos de dados horizontalmente são incentivados, em vez disso, a usar ou a migrar para [ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) para [Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md), que simplifica a criação e o gerenciamento de um aplicativo que use fragmentação. Uma biblioteca de cliente .NET permite que aplicativos definam como os dados são mapeados para os fragmentos e direcionam solicitações OLTP para os bancos de dados apropriados. Para dar suporte a operações de gerenciamento que reconfiguram como os dados são distribuídos entre fragmentos, um modelo de serviço de nuvem do Azure foi incluído para que você possa hospedar em sua própria assinatura do Azure. Além das [ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md), a Microsoft continuará a criar e publicar [orientações de padrões e práticas de fragmentação personalizada](https://msdn.microsoft.com/library/azure/dn764977.aspx) baseadas em lições aprendidas com clientes. Novos clientes que precisem escalar horizontalmente devem conferir as [ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) e/ou contatar o suporte da Microsoft para avaliar suas opções.

A Microsoft também está trocando a experiência de cópia de banco de dados por bancos de dados Premium. Antigamente, como a cota de banco de dados premium era limitada, CRIAR BANCO DE DADOS ... COMO CÓPIA DE no T-SQL criava um banco de dados Suspended Premium sem recursos reservados, que era cobrado pela mesma taxa de um banco de dados Business. Como a cota premium agora tem maior disponibilidade, não há mais suporte para Suspended Premium. As cópias de banco de dados agora serão criadas com o mesmo nível de desempenho e de edição da origem e serão cobradas de acordo. Os clientes podem escolher fazer o downgrade de bancos de dados copiados para uma camada de serviço ou nível de desempenho diferente para reduzir o custo, se necessário Os bancos de dados Suspended Premium existentes serão convertidos para a versão Business como parte desta versão. Espera-se que a introdução da recuperação pontual reduza a necessidade de se fazer cópias de backup dos bancos de dados.

## Como Basic, Standard e Premium melhoram a experiência de cobrança?

Os bancos de dados SQL Basic, Standard e Premium do Azure são cobrados por hora, e você tem a capacidade de dimensionar cada banco de dados para cima ou para baixo quatro vezes dentro de um período de 24 horas. Você será cobrado por uma taxa fixa baseada na maior camada de serviço e nível de desempenho escolhidos para cada hora. Além disso, os níveis de desempenho (exemplo: Basic, S1 e P2) são desmembrados na cobrança para facilitar a visualização do número de dias de banco de dados/horas incorridas em um único mês para cada nível de desempenho. Os bancos de dados Web e Business continuam a ser cobrados usando Unidades de Banco de Dados com base no tamanho do banco de dados. Visite a [página de preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/) para saber mais sobre os preços e as diferenças entre as novas camadas de serviço.


## Consulte também

[Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Preços de web e negócios](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Camadas de serviço](sql-database-service-tiers.md)

[Atualizar os bancos de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md)

<!---HONumber=AcomDC_1203_2015-->