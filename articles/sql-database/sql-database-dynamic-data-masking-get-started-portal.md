<properties
   pageTitle="Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (Portal clássico do Azure)"
   description="Como começar a usar a Máscara de Dados Dinâmicos do Banco de Dados SQL no Portal Clássico do Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/11/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (Portal clássico do Azure)

> [AZURE.SELECTOR]
- [Mascaramento de dados dinâmicos - Portal do Azure](sql-database-dynamic-data-masking-get-started.md)

## Visão geral

A Máscara de dados dinâmica no banco de dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos tem suporte para a versão V12 do Banco de Dados SQL do Azure.

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um representante de serviço em um centro de chamada pode identificar os chamadores por vários dígitos do seu número do seguro social ou o número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o representante de serviço. Uma regra de mascaramento pode ser definida para mascarar tudo menos os quatro últimos dígitos de qualquer número do seguro social ou o número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger os dados de informações de identificação pessoal (PII), de forma que um desenvolvedor possa consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## Noções básicas sobre Máscara de dados dinâmica em banco de dados SQL

É possível configurar a política de máscara de dados dinâmicos no portal clássico do Azure na guia Auditoria e Segurança do seu banco de dados.


> [AZURE.NOTE] Para configurar a máscara de dados dinâmicos no Portal do Azure, confira [Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (portal do Azure)](sql-database-dynamic-data-masking-get-started.md).


### Permissões de mascaramento de dados dinâmico

A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

### Política de mascaramento de dados dinâmico

* **Usuários SQL excluídos do mascaramento**: um conjunto de usuários do SQL ou de identidades do AAD que obterá dados não mascarados nos resultados da consulta SQL. Observe que os usuários com privilégios de administrador sempre serão excluídos do mascaramento e verão os dados originais sem qualquer máscara.

* **Regras de mascaramento**: um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome da coluna.

* **Funções de mascaramento**: um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
|----------|---------------|
| **Padrão** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos Xs se o tamanho do campo tiver menos de 4 caracteres para tipos de dados de cadeia de caracteres (nchar , ntext, nvarchar).<br/>• Use um valor de zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para tipos de dados de data/hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variante do SQL, é usado o valor padrão do tipo atual.<br/>• Para XML, o documento <masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types).
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **CPF** |**Método de mascaramento que expõe os dois últimos dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um número do seguro social.<br/><br/>XXX-XX-1234 |
| **Email** | **Método de mascaramento que expõe a primeira letra e substitui o domínio com XXX.com** usando um prefixo de cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** | **Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada.<br/>sufixo de prefixo [preenchimento]<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## Configurar a máscara de dados dinâmicos para seu banco de dados usando o Portal Clássico do Azure

1. Iniciar o Portal clássico do Azure em [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Clique no banco de dados que você deseja mascarar e clique na guia **AUDITORIA E SEGURANÇA**.

3. Em **máscara de dados dinâmicos**, clique em **HABILITADO** para habilitar o recurso de mascaramento de dados dinâmicos.

4. Digite os usuários do SQL ou as identidades AAD que devem ser excluídos da máscara e têm acesso aos dados confidenciais sem máscara. Deve ser uma lista de usuários separada por vírgulas. Observe que os usuários com privilégios de administrador sempre terão acesso aos dados sem máscara originais.

	>[AZURE.TIP] Para fazer isso de forma que a camada de aplicativo possa exibir dados confidenciais para usuários com privilégios de aplicativo, adicione o usuário do SQL ou a identidade do AAD usados pelo aplicativo para consultar o banco de dados. É altamente recomendável que essa lista contenha um número mínimo de usuários privilegiados para minimizar a exposição de dados confidenciais.

	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Na parte inferior da página na barra de menus, clique em **Adicionar MÁSCARA** para abrir a janela de configuração da regra de mascaramento.

6. Selecione o **Esquema**, a **Tabela** e a **Coluna** das listas suspensas para definir os campos designados que serão mascarados.

7. Escolha uma **FUNÇÃO DE MASCARAMENTO** na lista de categorias de mascaramento dos dados confidenciais.

	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Clique em **OK** na janela da regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de mascaramento de dados dinâmicos.

9. Clique em **SALVAR** para salvar a política de mascaramento nova ou atualizada.


## Configurar dados dinâmicos de mascaramento para o banco de dados usando instruções Transact-SQL

Confira [Mascaramento de dados dinâmicos](https://msdn.microsoft.com/library/mt130841.aspx)

## Configurar a máscara de dados dinâmica para o banco de dados usando cmdlets do Powershell

Confira [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## Configurar a máscara de dados dinâmica para o banco de dados usando a API REST

Confira [Operações para Bancos de Dados SQL do Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0413_2016-->