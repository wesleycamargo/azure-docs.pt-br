---
title: Conceitos de servidor no Banco de Dados do Azure para MySQL | Microsoft Docs
description: "Este tópico fornece diretrizes e considerações para trabalhar com o Banco de Dados do Azure para servidores MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 33508edb1b1aee058bff4b186f76d172f11f272f
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor no Banco de Dados do Azure para MySQL

Este tópico fornece diretrizes e considerações para trabalhar com o Banco de Dados do Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é um Banco de Dados do Azure para servidor MySQL?

Um Banco de Dados do Azure para servidor MySQL é um ponto administrativo central para vários bancos de dados. É a mesma construção de servidor MySQL com a qual talvez você já esteja familiarizado no mundo local. Especificamente, o Banco de Dados do Azure para o serviço MySQL é gerenciado, oferece garantias de desempenho, expõe acesso e recursos no nível do servidor.

Um Banco de Dados do Azure para servidor MySQL:

- É criado dentro de uma assinatura do Azure.
- É o recurso pai para bancos de dados.
- Fornece um namespace para bancos de dados.
- É um contêiner com semântica de tempo de vida fortes – exclua um servidor e ele excluirá os bancos de dados contidos.
- Coloca recursos em uma região.
- Fornece um ponto de extremidade de conexão para acesso ao servidor e ao banco de dados.
- Fornece o escopo para políticas de gerenciamento que se aplicam a seus bancos de dados: logons, firewall, usuários, funções, configurações etc.
- Está disponível em várias versões. Para saber mais, confira [Banco de Dados do Azure com suporte para versões de banco de dados MySQL](./concepts-supported-versions.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como faço para me conectar e autenticar em um Banco de Dados do Azure para servidor MySQL?

Os elementos a seguir ajudam a garantir o acesso seguro ao seu banco de dados.

|||
| :-- | :-- |
| **Autenticação e autorização** | O Banco de Dados do Azure para servidor MySQL oferece suporte à autenticação de MySQL nativa. Você pode se conectar e autenticar no servidor com logon de administrador do servidor. |
| **Protocolo** | O serviço oferece suporte a um protocolo baseado em mensagem usado pelo MySQL. |
| **TCP/IP** | O protocolo tem suporte em TCP/IP e em soquetes de domínio do Unix. |
| **Firewall** | Para ajudar a proteger seus dados, uma regra de firewall impede todo acesso ao servidor de banco de dados, ou aos seus bancos de dados, até que você especifique quais computadores têm permissão. Confira [Regras de firewall do Banco de Dados do Azure para servidor MySQL](./concepts-firewall-rules.md). |
| **SSL** | O serviço oferece suporte à imposição de conexões SSL entre seus aplicativos e o servidor de banco de dados.  Confira [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md). |
|||

## <a name="how-do-i-manage-a-server"></a>Como posso gerenciar um servidor?
Você pode gerenciar o Banco de Dados do Azure para servidores MySQL usando o Portal do Azure ou a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para MySQL](./overview.md)
- Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-service-tiers.md)
- Para saber mais sobre como se conectar ao serviço, confira [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](./concepts-connection-libraries.md).

