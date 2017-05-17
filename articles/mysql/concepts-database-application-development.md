---
title: "Visão geral de desenvolvimento de aplicativo de banco de dados para o Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Apresenta considerações de design que um desenvolvedor deve seguir ao escrever o código de um aplicativo para se conectar ao Banco de Dados do Azure para MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5f8bb1631ccd6dcfe1354e3b4c0897c587149b1d
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="application-development-overview-for-azure-database-for-mysql"></a>Visão geral de desenvolvimento de aplicativo para o Banco de Dados do Azure para MySQL 
Este artigo discute considerações de design que um desenvolvedor deve seguir ao escrever o código de um aplicativo para se conectar ao Banco de Dados do Azure para MySQL 

> [!TIP]
> Para ver um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, criar um banco de dados, conectar e consultar usando o Workbench e mysql.exe, confira [Criar seu primeiro banco de dados MySQL do Azure](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Linguagem e plataforma
Há exemplos de código disponíveis para uma variedade de plataformas e linguagens de programação. Encontre links para exemplos de código em: [Bibliotecas de conectividade usadas para se conectar ao Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Ferramentas
O Banco de Dados do Azure para MySQL usa a versão de comunidade do MySQL, que é compatível com ferramentas de gerenciamento comuns do MySQL, como o Workbench, ou utilitários do MySQL, como o mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql) e outros. Você também pode usar o Portal do Azure, a CLI do Azure e APIs REST para interagir com o serviço de banco de dados.

## <a name="resource-limitations"></a>Limitações de recursos
O Banco de Dados MySQL do Azure gerencia os recursos disponíveis para um servidor usando dois mecanismos diferentes: 
- Governança de recursos 
- Imposição de limites.

## <a name="security"></a>Segurança
O Banco de Dados MySQL do Azure fornece recursos para limitar o acesso, proteger os dados, configurar usuários e funções e monitorar atividades em um Banco de Dados MySQL.

## <a name="authentication"></a>Autenticação
O Banco de Dados MySQL do Azure oferece suporte à autenticação de usuários e logons do servidor.

## <a name="resiliency"></a>Resiliência
Quando ocorre um erro transitório ao se conectar ao Banco de Dados MySQL, seu código deverá repetir a chamada. Recomendamos que a lógica de repetição use a lógica de retirada, de modo que ela não sobrecarregue o Banco de Dados SQL com vários clientes realizando novas tentativas ao mesmo tempo.

- Exemplos de código: para obter exemplos de código que ilustram a lógica de repetição, confira os exemplos para o idioma de sua preferência em: [Bibliotecas de conexão usadas para se conectar ao Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Gerenciando conexões
Conexões de banco de dados são um recurso limitado, portanto, recomendamos o uso adequado de conexões ao acessar o Banco de Dados MySQL para melhorar o desempenho.
- Acesse o banco de dados usando o pooling de conexão ou conexões persistentes.
- Acesse o banco de dados usando uma conexão de curta duração. 
- Use a lógica de repetição em seu aplicativo no ponto da tentativa de conexão, para detectar falhas causadas pelas conexões simultâneas atingirem o máximo permitido. Na lógica de repetição, defina um pequeno atraso e aguarde um período aleatório antes das tentativas de conexão adicionais.
