---
title: Conectividade SSL do Banco de Dados do Azure para MariaDB
description: Informações para configurar o Banco de Dados do Azure para MariaDB e aplicativos associados a fim de usar as conexões SSL adequadamente
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dda5df58a83ddd3ce42fa887c3c32a3e23954920
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946639"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Conectividade SSL no Banco de Dados do Azure para MariaDB
O Banco de Dados do Azure para MariaDB dá suporte à conexão de seu servidor de banco de dados com aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="default-settings"></a>Configurações padrão
Por padrão, o serviço de banco de dados deve ser configurado para exigir conexões SSL ao se conectar ao MariaDB.  Recomendamos evitar desabilitar a opção SSL sempre que possível.

Ao provisionar um novo servidor de Banco de Dados do Azure para MariaDB por meio do portal do Azure e da CLI do Azure, a imposição de conexões SSL está habilitada por padrão.

Cadeias de conexão para várias linguagens de programação são mostradas no Portal do Azure. Essas cadeias de conexão incluem os parâmetros SSL necessários para conectar-se ao banco de dados. No Portal do Azure, selecione seu servidor. No cabeçalho **Configurações**, selecione as **Cadeias de conexão**. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

<!-- To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).-->

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [regras de firewall de servidor](concepts-firewall-rules.md)
- Saiba como [configurar o SSL](howto-configure-ssl.md).
