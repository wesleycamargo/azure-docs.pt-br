---
title: Alias do DNS para Banco de Dados SQL do Azure | Microsoft Docs
description: "Seus aplicativos podem se conectar a um alias do nome do servidor de Banco de Dados SQL do Azure. Ao mesmo tempo, você pode alterar o Banco de Dados SQL para o qual o alias aponta a qualquer momento no intuito de facilitar testes e assim por diante."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: DNS alias
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 01/31/2018
ms.reviewer: dmalik;ayolubek
ms.author: genemi
ms.openlocfilehash: ef888763918447b9e5f97288d90e287441a11cc6
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias do DNS para Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure tem um servidor de DNS (Sistema de Nomes de Domínio). As APIs REST e o PowerShell aceitam [chamadas para criar e gerenciar aliases de DNS](#anchor-powershell-code-62x) do nome do servidor de Banco de Dados SQL.

Um *alias do DNS* pode ser usado no lugar do nome do servidor de Banco de Dados SQL do Azure. Os programas cliente podem usar o alias em suas cadeias de conexão. O alias do DNS oferece uma camada de conversão que pode redirecionar os programas cliente a diferentes servidores. Essa camada poupa as dificuldades de ter que localizar e editar todos os clientes e suas cadeias de conexão.

Alguns usos comuns para um alias do DNS incluem os seguintes casos:

- Criar um nome fácil de lembrar para um servidor do SQL Server do Azure.
- Durante o desenvolvimento inicial, seu alias pode se referir a um servidor de teste de Banco de Dados SQL. Quando o aplicativo for lançado, você poderá modificar o alias para se referir ao servidor de produção. A transição de teste para a produção não exige nenhuma modificação nas configurações dos vários clientes que se conectam ao servidor de banco de dados.
- Suponha que único banco de dados em seu aplicativo seja movido para outro servidor de Banco de Dados SQL. Aqui você pode modificar o alias sem a necessidade de modificar as configurações dos vários clientes.

#### <a name="domain-name-system-dns-of-the-internet"></a>DNS (Sistema de Nomes de Domínio) da Internet

A Internet depende do DNS. O DNS converte seus nomes amigáveis no nome do servidor de Banco de Dados SQL do Azure.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um alias do DNS

Suponha que você tenha que mudar o sistema para um novo servidor de Banco de Dados SQL do Azure. No passado, você precisava localizar e atualizar cada cadeia de conexão em cada programa cliente. Mas, agora, se as cadeias de conexão usam um alias do DNS, apenas uma propriedade do alias deve ser atualizada.

O recurso alias do DNS do Banco de Dados SQL do Azure pode ajudar nas seguintes situações:

#### <a name="test-to-production"></a>Teste para a produção

Quando você começar a desenvolver os programas cliente, faça com que eles usem um alias do DNS nas cadeias de conexão. Você fazer com que as propriedades do alias apontem para uma versão de teste do seu servidor de Banco de Dados SQL do Azure.

Depois, quando o novo sistema for lançado em produção, você poderá atualizar as propriedades do alias para apontarem para o servidor de Banco de Dados SQL de produção. Não haverá necessidade de alteração nos programas cliente.

#### <a name="cross-region-support"></a>Suporte entre regiões

Uma recuperação de desastre pode mudar seu servidor de Banco de Dados SQL para uma região geográfica diferente. Um sistema que estava usando um alias do DNS pode evitar a necessidade de localizar e atualizar as cadeias de conexão de todos os clientes. Em vez disso, você pode atualizar um alias para se referir ao novo servidor de Banco de Dados SQL que agora hospeda o seu banco de dados.




## <a name="properties-of-a-dns-alias"></a>Propriedades de um alias do DNS

As propriedades a seguir se aplicam a cada alias de DNS do seu servidor de Banco de Dados SQL:

- *Nome exclusivo:* cada nome de alias que você cria é exclusivo em todos os servidores do Banco de Dados SQL do Azure, assim como os nomes dos servidores.

- *Servidor é necessário:* um alias de DNS não pode ser criado se não faz referência a exatamente um servidor e esse servidor já deve existir. Um alias atualizado sempre deve fazer referência a exatamente um servidor existente.
    - Quando você remove um servidor de Banco de Dados SQL, o sistema do Azure também remove todos os aliases de DNS que fazem referência ao servidor.

- *Não associado a nenhuma região:* aliases de DNS não são associados a uma região. Todos os alias de DNS podem ser atualizados para fazer referência a um servidor de Banco de Dados SQL do Azure que resida em qualquer região geográfica.
    - No entanto, ao atualizar um alias para se referir a outro servidor, ambos os servidores devem existir na mesma *assinatura* do Azure.

- *Permissões:* para gerenciar um alias de DNS, o usuário deve ter permissões de *Colaborador do Server* ou superior. Para obter mais informações, consulte [Introdução ao Controle de Acesso Baseado em Função no portal do Azure](../active-directory/role-based-access-control-what-is.md).





## <a name="manage-your-dns-aliases"></a>Gerenciar seus aliases de DNS

Os cmdlets do PowerShell e das APIs REST estão disponíveis para que você possa gerenciar seus aliases de DNS de forma programática.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>APIs REST para gerenciar seus aliases de DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

A documentação das APIs REST está disponível perto do seguinte local:
- [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

Além disso, as APIs REST podem ser vistas no GitHub em:
- [Servidor de Banco de Dados SQL do Azure, APIs REST de alias do DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerenciar seus aliases de DNS

Os cmdlets do PowerShell que chamam as APIs REST estão disponíveis.

Há um exemplo de código de cmdlets do PowerShell usados para gerenciar aliases de DNS documentado em:
- [PowerShell para alias do DNS para o Banco de Dados SQL do Azure](dns-alias-powershell.md)


Os cmdlets usados no exemplo de código são os seguintes:
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): cria um novo alias de DNS no sistema de serviço do Banco de Dados SQL do Azure. O alias refere-se ao servidor de Banco de Dados SQL do Azure 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): obtém e lista todos os aliases de DNS que são atribuídos ao servidor do Banco de Dados SQL 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): modifica o nome do servidor ao qual o alias está configurado para fazer referência, de servidor 1 para servidor do Banco de Dados SQL 2.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): remove o alias do DNS do servidor do Banco de Dados SQL 2, usando o nome do alias.


Os cmdlets anteriores foram adicionados ao módulo **AzureRM.Sql** a partir da versão 5.1.1 do módulo.




## <a name="limitations-during-preview"></a>Limitações durante a versão prévia

Atualmente, um alias de DNS tem as seguintes limitações:

- *Atraso de até 2 minutos:* leva até 2 minutos para que um alias de DNS seja atualizado ou removido.
    - Independentemente de qualquer breve atraso, o alias interrompe imediatamente as conexões de cliente que fazem referência ao servidor herdado.

- *Pesquisa de DNS:* por enquanto, a única maneira oficial de verificar a qual servidor um alias de DNS específico faz referência é por meio da execução de uma [pesquisa de DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[A auditoria de tabela não é compatível](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* você não pode usar um alias de DNS em um servidor de Banco de Dados SQL do Azure que tenha *auditoria de tabela* habilitada em um banco de dados.
    - A auditoria de tabela foi preterida.
    - É recomendável que você mude para a [Auditoria de Blob](sql-database-auditing.md).




## <a name="related-resources"></a>Recursos relacionados

- [Visão geral da continuidade de negócios com o Banco de Dados SQL do Azure](sql-database-business-continuity.md), incluindo recuperação de desastre.



## <a name="next-steps"></a>Próximas etapas

- [PowerShell para alias do DNS para o Banco de Dados SQL do Azure](dns-alias-powershell.md)

