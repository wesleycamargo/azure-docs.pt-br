---
title: Conceitos de servidor no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: "Este tópico fornece diretrizes e considerações para trabalhar com o Banco de Dados do Azure para servidores PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 46e109c25f8cbdba9b3769db7563259f7c6705d4
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="azure-database-for-postgresql-servers"></a>Banco de Dados do Azure para servidores PostgreSQL
Este artigo apresenta diretrizes e considerações para trabalhar com servidores do Banco de Dados do Azure para PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>O que é um Banco de Dados do Azure para servidor PostgreSQL?
Um Banco de Dados do Azure para servidor PostgreSQL é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor PostgreSQL com a qual talvez você já esteja familiarizado no mundo local. Especificamente, o serviço PostgreSQL é gerenciado, oferece garantias de desempenho, expõe acesso e recursos no nível do servidor.

Um Banco de Dados do Azure para servidor PostgreSQL:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso ao servidor e ao banco de dados (.postgresql.database.azure.com).
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Está disponível em várias versões. Para saber mais, confira [Versões do banco de dados PostgreSQL com suporte](concepts-supported-versions.md).
- É extensível pelos usuários. Para saber mais, confira [Extensões do PostgreSQL](concepts-extensions.md).

Dentro de um banco de dados do Azure para o servidor PostgreSQL, você pode criar um ou mais bancos de dados. Você pode optar por criar um banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Os preços são estruturados por servidor, com base na configuração do tipo de preço, unidades de computação e armazenamento (GB). Para obter mais informações, consulte [Tipos de preço](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Como faço para me conectar e autenticar em um Banco de Dados do Azure para servidor PostgreSQL?
Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|||
| :-- | :-- |
| **Autenticação e autorização** | O Banco de Dados do Azure para servidor PostgreSQL oferece suporte à autenticação de PostgreSQL nativa. Você pode se conectar e autenticar no servidor com logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo PostgreSQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor e seus bancos de dados até que você especifique quais computadores têm permissão. Confira [Regras de firewall do Banco de Dados do Azure para servidor PostgreSQL](concepts-firewall-rules.md). |
|||

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?
Você pode gerenciar o Banco de Dados do Azure para servidores PostgreSQL usando o [Portal do Azure](https://portal.azure.com) ou a [CLI do Azure](/cli/azure/postgres).

## <a name="server-parameters"></a>Parâmetros do Servidor
Os parâmetros de servidor PostgreSQL determinam a configuração do servidor. No banco de dados do Azure para PostgreSQL a lista de parâmetros pode ser exibida e editada por meio do portal do Azure ou a CLI do Azure. 

Como um serviço gerenciado para Postgres, os parâmetros configuráveis no banco de dados do Azure para PostgreSQL são um subconjunto dos parâmetros em uma instância local do Postgres (para obter mais informações sobre parâmetros Postgres, consulte o [PostgreSQL documentação](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). O banco de dados do Azure para servidor PostgreSQL está habilitado com valores padrão para cada parâmetro na criação. Parâmetros que necessitariam de um reinício de servidor ou acesso de superusuário para mudanças sentirem efeito não podem ser configurados pelo usuário.


## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para PostgreSQL](overview.md).
- Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](concepts-service-tiers.md).
- Para saber mais sobre como se conectar ao serviço, confira [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
- Exibir e editar os parâmetros de servidor por meio de [portal do Azure](howto-configure-server-parameters-using-portal.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md).
