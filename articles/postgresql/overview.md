---
title: Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 06/15/2018
ms.openlocfilehash: 86a8b824479173024b1a14bb8612073ffbfac468
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092150"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é o Banco de Dados do Azure para PostgreSQL?

O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional no Microsoft Cloud, projetado para desenvolvedores com base na versão de comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre. O Banco de Dados do Azure para PostgreSQL fornece:

- Alta disponibilidade interna sem nenhum custo adicional
- Desempenho previsível, com preços pré-pagos inclusivos
- Escale conforme o necessário em segundos
- Seguro para proteger dados confidenciais em repouso e em movimento
- Backups automáticos e restauração pontual por até 35 dias
- Segurança e conformidade de nível empresarial

Todos esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Esses recursos permitem que você se concentre no método RAD e acelere seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos para gerenciamento de máquinas virtuais e infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, sem precisar aprender novas habilidades. 

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados do Azure para PostgreSQL relacionados a desempenho, escalabilidade e gerenciabilidade. Veja estes inícios rápidos para começar:

- [Criar um servidor de Banco de Dados do Azure para PostgreSQL usando o portal do Azure](quickstart-create-server-database-portal.md)
- [Criar um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI Azure](quickstart-create-server-database-azure-cli.md)

Para ver diversos exemplos da CLI do Azure, consulte:

- [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e a escala em segundos
O serviço Banco de dados do Azure para PostgreSQL oferece três tipos de preço: Básico, Uso Geral e Otimizado para Memória. Cada tipo oferece recursos diferentes para dar suporte a suas cargas de trabalho do banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Como você decide quando aumentar ou reduzir? Use os recursos internos de monitoramento e alerta do Azure. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho ou armazenamento atuais ou previstas. Veja [Alertas](howto-alert-on-metric.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
O SLA (Contrato de Nível de Serviço) de disponibilidade de 99,99% do Azure, que é líder do setor e é alimentado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução de forma ininterrupta. Com cada servidor do Banco de Dados do Azure para PostgreSQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e proteção de dados que, em outras situações, seria necessário comprar ou projetar, criar e gerenciar. Com o Banco de Dados do Azure para PostgreSQL, cada tipo de preço oferece um conjunto abrangente de opções e recursos de continuidade de negócios que você pode usar para voltar à execução e permanecer assim. Você pode usar a [restauração para um ponto específico](howto-restore-server-portal.md) para retornar um banco de dados para um estado anterior, até 35 dias. Além disso, se o datacenter que hospeda seus bancos de dados sofrer uma interrupção, você poderá restaurar os bancos de dados de cópias de backups recentes com redundância geográfica.

## <a name="secure-your-data"></a>Proteja seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de dados que o Banco de Dados do Azure para PostgreSQL mantém, com recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar atividades. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/en-us/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure.

O serviço Banco de Dados do Azure para PostgreSQL usa a criptografia de armazenamento para dados em repouso. Os dados, incluindo backups, são criptografados no disco (com exceção dos arquivos temporários criados pelo mecanismo durante a execução de consultas). O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Por padrão, o serviço Banco de Dados do Azure para PostgreSQL está configurado para exigir a [segurança da conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento em toda a rede. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="contacts"></a>Contatos
Para perguntas ou sugestões sobre como trabalhar com o Banco de Dados do Azure para PostgreSQL, envie um email para a equipe do Banco de Dados do Azure para PostgreSQL ([@Ask Banco de Dados do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Observe que isso não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contato, conforme apropriado:
- Para entrar em contato com o Suporte do Azure, [crie um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Próximas etapas
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras.
- Comece pela [criação do seu primeiro Banco de Dados do Azure para PostgreSQL](./quickstart-create-server-database-portal.md).
- Crie seu primeiro aplicativo no Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexão](./concepts-connection-libraries.md)
