---
title: Introdução ao Mascaramento de dados dinâmicos no banco de dados SQL (Portal do Azure)
description: Como começar a usar a Máscara de Dados Dinâmicos do Banco de Dados SQL no Portal do Azure
services: sql-database
documentationcenter: ''
author: ronitr
manager: jhubbard
editor: v-romcal

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronitr; ronmat; v-romcal; sstein

---
# Introdução ao Mascaramento de dados dinâmicos no banco de dados SQL (Portal do Azure)
> [!div class="op_single_selector"]
> * [Mascaramento de dados dinâmicos - Portal Clássico do Azure](sql-database-dynamic-data-masking-get-started-portal.md)
> 
> 

## Visão geral
A Máscara de dados dinâmica no banco de dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos tem suporte para a versão V12 do Banco de Dados SQL do Azure.

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um representante de serviço em um centro de chamada pode identificar os chamadores por vários dígitos do seu número do seguro social ou o número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o representante de serviço. Uma regra de mascaramento pode ser definida para mascarar tudo menos os quatro últimos dígitos de qualquer número do seguro social ou o número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger os dados de informações de identificação pessoal (PII), de forma que um desenvolvedor possa consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## Noções básicas sobre Máscara de dados dinâmica em banco de dados SQL
É possível configurar uma política de máscara de dados dinâmicos no Portal do Azure selecionando a operação Máscara de Dados Dinâmicos na folha de configuração do Banco de Dados SQL ou na folha de configurações.

### Permissões de mascaramento de dados dinâmico
A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

### Política de mascaramento de dados dinâmico
* **Usuários SQL excluídos do mascaramento**: um conjunto de usuários do SQL ou de identidades do AAD que obterá dados não mascarados nos resultados da consulta SQL. Observe que os usuários com privilégios de administrador sempre serão excluídos do mascaramento e verão os dados originais sem qualquer máscara.
* **Regras de mascaramento**: um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome da coluna.
* **Funções de mascaramento**: um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Padrão** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos Xs se o tamanho do campo tiver menos de 4 caracteres para tipos de dados de cadeia de caracteres (nchar , ntext, nvarchar).<br/>• Use um valor de zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para tipos de dados de data/hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variante do SQL, é usado o valor padrão do tipo atual.<br/>• Para XML, o documento <masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **CPF** |**Método de mascaramento que expõe os dois últimos dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um número do seguro social.<br/><br/>XXX-XX-1234 |
| **Email** |**Método de mascaramento que expõe a primeira letra e substitui o domínio com XXX.com** usando um prefixo de cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada.<br/>sufixo[preenchimento]prefixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### Campos recomendados para mascarar
O mecanismo de recomendações DDM sinaliza determinados campos do banco de dados como potencialmente confidenciais, que podem ser bons candidatos para mascaramento. Na folha Mascaramento de Dados Dinâmicos no portal, você verá as colunas recomendadas para seu banco de dados. Tudo o que você precisa fazer é clicar em **Adicionar Máscara** para uma ou mais colunas e em **Salvar** para aplicar uma máscara a esses campos.

## Configurar dados dinâmicos de mascaramento para o banco de dados usando o Portal do Azure
1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a folha de configurações do banco de dados que contém os dados confidenciais que você deseja mascarar.
3. Clique no bloco **Máscara de Dados Dinâmicos** que inicia a folha de configuração **Máscara de Dados Dinâmicos**.
   
   * Se preferir, você pode rolar para baixo até a seção **Operações** e clicar em **Máscara de Dados Dinâmicos**.
     
     ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. Na folha de configuração **Mascaramento de Dados Dinâmicos**, você poderá ver algumas colunas de banco de dados que o mecanismo de recomendações sinalizou para mascaramento. Para aceitar as recomendações, basta clicar em **Adicionar Máscara** para uma ou mais colunas e uma máscara será criada com base no tipo padrão para essa coluna. Você pode alterar a função de mascaramento clicando na regra de mascaramento e editando o formato do campo de mascaramento para um formato diferente à sua escolha. Clique em **Salvar** para salvar suas configurações.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Para adicionar uma máscara a qualquer coluna do seu banco de dados, na parte superior da folha de configuração **Máscara de Dados Dinâmicos**, clique em **Adicionar Máscara** para abrir a folha de configuração **Adicionar Regra de Mascaramento**
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Selecione o **Esquema**, a **Tabela** e a **Coluna** para definir os campos designados que serão mascarados.
7. Escolha um **Formato do Campo de Máscara** na lista de categorias de mascaramento dos dados confidenciais.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>
8. Clique em **Salvar** na folha da regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de máscara de dados dinâmicos.
9. Digite os usuários do SQL ou as identidades AAD que devem ser excluídos da máscara e têm acesso aos dados confidenciais sem máscara. Deve ser uma lista de usuários separada por vírgulas. Observe que os usuários com privilégios de administrador sempre terão acesso aos dados sem máscara originais.
   
    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Para fazer isso de forma que a camada de aplicativo possa exibir dados confidenciais para usuários com privilégios de aplicativo, adicione o usuário do SQL ou a identidade do AAD usados pelo aplicativo para consultar o banco de dados. É altamente recomendável que essa lista contenha um número mínimo de usuários privilegiados para minimizar a exposição de dados confidenciais.
   > 
   > 
10. Clique em **Salvar** na folha de configuração de mascaramento de dados para salvar a política de mascaramento nova ou atualizada.

## Configurar a máscara de dados dinâmica para o banco de dados usando cmdlets do Powershell
Confira [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## Configurar a máscara de dados dinâmica para o banco de dados usando a API REST
Confira [Operações para Bancos de Dados SQL do Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0713_2016-->