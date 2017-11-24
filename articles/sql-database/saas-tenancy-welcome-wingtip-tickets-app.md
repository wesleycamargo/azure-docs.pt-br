---
title: Bem-vindo ao aplicativo Wingtips - Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre modelos de aluguel de banco de dados e sobre o aplicativo de exemplo Wingtips SaaS para o Banco de Dados SQL do Azure no ambiente de nuvem.
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Bem-vindo ao aplicativo de aluguel de Banco de Dados SQL do Azure do SaaS de exemplo do Wingtip Tickets

Bem-vindo ao aplicativo de aluguel de Banco de Dados SQL do Azure do SaaS de exemplo do Wingtip Tickets e seus tutoriais. Aluguel de banco de dados refere-se ao modo de isolamento de dados que seu aplicativo fornece aos clientes que pagam para serem hospedados em seu aplicativo. Para simplificar por enquanto, ou cliente tem um banco de dados inteiro para si mesmo ou compartilha um banco de dados com outro cliente.

## <a name="wingtip-tickets-app"></a>Aplicativo Wingtip Tickets

O aplicativo de exemplo do Wingtip Tickets ilustra os efeitos de diferentes modelos de aluguel de banco de dados no design e gerenciamento de aplicativos de SaaS multilocatários. Os tutoriais a seguir descrevem diretamente esses mesmos efeitos. O Wingtip Tickets baseia-se no Banco de Dados SQL do Azure.

O Wingtip Tickets foi projetado para lidar com vários cenários de design e gerenciamento que são usados por clientes reais de SaaS. Os padrões de uso que surgiram são tratados pelo Wingtip Tickets.

Você pode instalar o aplicativo Wingtip Tickets em sua própria assinatura do Azure em cinco minutos. A instalação inclui a inserção de dados de exemplo para vários locatários. Você pode instalar com segurança o aplicativo e os scripts de gerenciamento para todos os modelos, porque as instalações não interagem ou interferem umas com as outras.

#### <a name="code-in-github"></a>Código no Github

O código do aplicativo e os scripts de gerenciamento estão disponíveis no GitHub:

- Modelo de **aplicativo autônomo**: [Repositório WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- Modelo de **banco de dados por locatário**: [Repositório WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- Modelo de **multilocatário compartilhado**: [Repositório WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

O mesmo código básico para o aplicativo Wingtip Tickets é reutilizado para todos os modelos anteriores listados. Você pode usar o código do Github para seus próprios projetos de SaaS.



## <a name="major-database-tenancy-models"></a>Principais modelos de aluguel de banco de dados

O Wingtip Tickets é um aplicativo SaaS de listagem de eventos e de tíquetes. O Wingtip fornece serviços que são necessários por local. Todos os itens a seguir aplicam-se a cada local:

- Paga para ser hospedado no seu aplicativo.
- É um *locatário* no Wingtip.
- Eventos de hosts. Os seguintes eventos ocorrem:
    - Preços de tíquete.
    - Preços de venda.
    - Clientes que compram tíquetes.

O aplicativo, junto com os scripts de gerenciamento e os tutoriais, apresenta um cenário completo de SaaS. O cenário inclui as seguintes atividades:

- Provisionamento de locatários.
- Monitoramento e ajuste de desempenho.
- Gerenciamento do esquema.
- Análise e relatório entre locatários.

Todas essas atividades são fornecidas em qualquer escala que elas forem necessárias.



## <a name="code-samples-for-each-tenancy-model"></a>Exemplos de código para cada modelo de aluguel

Um conjunto de modelos de aplicativo é enfatizado. No entanto, outras implementações podem misturar elementos de dois ou mais modelos.

#### <a name="standalone-app-model"></a>Modelo de aplicativo autônomo

![Modelo de aplicativo autônomo][standalone-app-model-62s]

Esse modelo usa um aplicativo de locatário único. Portanto, esse modelo precisa de apenas um banco de dados e armazena dados somente para esse único locatário. O locatário beneficia-se de um isolamento total de outros locatários no banco de dados.

Você pode usar este modelo quando vende instâncias do seu aplicativo para muitos clientes diferentes, para que cada cliente use-o por conta própria. O cliente é, portanto, o único locatário. Apesar do banco de dados armazenar dados para apenas um cliente, o banco de dados armazena dados para muitos clientes do cliente.

#### <a name="database-per-tenant"></a>Banco de dados por locatário

![Modelo de banco de dados por locatário][database-per-tenant-model-35d]

Esse modelo tem vários locatários na instância do aplicativo. Ainda assim, para cada novo locatário, outro banco de dados é alocado para uso somente pelo novo locatário.

Esse modelo fornece isolamento total do banco de dados para cada locatário. O serviço do Banco de Dados SQL do Azure tem a sofisticação para tornar esse modelo plausível.

- [Introdução a um exemplo de aplicativo SaaS multilocatário do Banco de Dados SQL][saas-dbpertenant-wingtip-app-overview-15d] - possui mais informações sobre este modelo.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Bancos de dados multilocatários compartilhados, o híbrido

![Bancos de dados multilocatários compartilhados, o híbrido][sharded-multitenantdb-model-hybrid-79m]

Esse modelo tem vários locatários na instância do aplicativo. Esse modelo também possui vários locatários em alguns ou todos os seus bancos de dados. Esse modelo é bom para oferecer diferentes camadas de serviço para que os clientes possam pagar mais se eles valorizam isolamento total do banco de dados.

O esquema de cada banco de dados inclui um identificador de locatário. O identificador do locatário está presente até nos bancos de dados que armazenam apenas um locatário.

- [Introdução a um exemplo de aplicativo SaaS multilocatário do Banco de Dados SQL][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>Tutoriais para cada modelo de aluguel

Cada modelo de aluguel é documentado pelo seguinte:

- Um conjunto de artigos de tutorial.
- O código-fonte é armazenado em um repositório Github dedicado para o modelo:
    - O código para o aplicativo Wingtip Tickets.
    - O código do script para cenários de gerenciamento.

#### <a name="tutorials-for-management-scenarios"></a>Tutoriais para cenários de gerenciamento

Os artigos tutoriais para cada modelo abordam os seguintes cenários de gerenciamento:

- Provisionamento de locatário.
- Monitoramento e gerenciamento de desempenho.
- Gerenciamento do esquema.
- Análise e relatório entre locatários.
- Restauração de um locatário para um ponto anterior no tempo.
- Recuperação de desastre.



## <a name="next-steps"></a>Próximas etapas

- [Introdução a um exemplo de aplicativo SaaS multilocatário do Banco de Dados SQL][saas-dbpertenant-wingtip-app-overview-15d] - possui mais informações sobre este modelo.

- [Padrões de locatário de banco de dados de SaaS multilocatários][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Modelo de aplicativo autônomo"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Modelo de banco de dados por locatário"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Modelo de bancos de dados multilocatários compartilhados, o híbrido"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


