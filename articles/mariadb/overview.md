---
title: Visão geral do banco de dados do Azure para o serviço de banco de dados relacional MariaDB
description: Visão geral do banco de dados do Azure para o serviço de banco de dados relacional MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249155"
---
# <a name="what-is-azure-database-for-mariadb"></a>O que é o Banco de Dados do Azure para o MariaDB?
O Banco de Dados do Azure para MariaDB é um serviço de banco de dados relacional na nuvem da Microsoft com base no mecanismo de banco de dados [edição da comunidade MariaDB](https://mariadb.org/download/). Esse serviço está na fase de visualização pública. Banco de dados do Azure para MariaDB oferece:

- Alta disponibilidade interna sem nenhum custo adicional.
- Desempenho previsível, com preços pré-pagos inclusivos.
- Escale conforme necessário em segundos.
- Seguro para proteger dados confidenciais em repouso e em movimento.
- Backups automáticos e restauração pontual por até 35 dias.
- Segurança e conformidade de nível empresarial.

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos ao gerenciamento de máquinas virtuais e de infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, tudo isso sem precisar aprender novas habilidades.

Este artigo é uma introdução ao Banco de Dados do Azure para os principais conceitos e recursos do MariaDB relacionados ao desempenho, escalabilidade e capacidade de gerenciamento, com links para explorar detalhes. Veja estes inícios rápidos para começar:
- [Criar um Banco de Dados do Azure para MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Criar um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e a escala em segundos
Na visualização, o serviço Banco de Dados do Azure para MariaDB oferece vários níveis de serviço: Básico, Uso Geral e Memória Otimizada. Cada camada oferece diferentes níveis de desempenho e recursos para dar suporte a cargas de trabalho de banco de dados leves e pesadas. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Como você decide quando aumentar e reduzir? Use os recursos internos de alerta e monitoramento de desempenho, em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução dos vCores com base nas suas necessidades de desempenho atuais ou previstas. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
O acordo de nível de serviço (SLA) de disponibilidade de 99,99% do setor líder do Azure (não oferecido durante a pré-visualização pública), impulsionado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo funcionando 24/7. Com cada Banco de Dados do Azure para o servidor MariaDB, você aproveita a segurança interna, a tolerância a falhas e a proteção de dados que, de outra forma, teria que comprar ou projetar, criar e gerenciar. Com o Banco de Dados do Azure para MariaDB, você pode usar a restauração point-in-time para recuperar um servidor para um estado anterior, em até 35 dias.

## <a name="secure-your-data"></a>Proteja seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de dados mantida pelo Banco de Dados do Azure para MariaDB, com recursos que limitam o acesso, protegem os dados em repouso e em movimento e ajudam a monitorar a atividade. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/en-us/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure.

O banco de dados do Azure para MariaDB serviço usa a criptografia de armazenamento para dados em repouso. Os dados incluindo backups são criptografados no disco (com exceção dos arquivos temporários que são criados pelo mecanismo durante a execução de consultas). O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Por padrão, o serviço Banco de Dados do Azure para MariaDB é configurado para exigir [segurança de conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento na rede. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="contacts"></a>Contatos
Para dúvidas ou sugestões que você possa ter sobre como trabalhar com o Banco de Dados do Azure para MariaDB, envie um email para o Banco de Dados do Azure para a Equipe do MariaDB ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Observe que isso não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contato, conforme apropriado:
- Para entrar em contato com o Suporte do Azure, [crie um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Próximas etapas
Agora que você leu uma introdução ao Banco de Dados do Azure para o MariaDB e respondeu à pergunta "O que é o Banco de Dados do Azure para o MariaDB?", Você está pronto para:
- Consultar a página de preços para ver comparações de custo e calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mariadb/)
- Comece com a criação do seu primeiro servidor. [Criar um Banco de Dados do Azure para MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
