<properties 
   pageTitle="Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (Portal de Visualização do Azure)" 
   description="Como começar a usar a Máscara de Dados Dinâmicos do Banco de Dados SQL no Portal de Visualização do Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (Portal de Visualização do Azure)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Visão geral

A Máscara de dados dinâmica no banco de dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. A Máscara de dados dinâmica está na visualização para as camadas de serviço Basic, Standard e Premium na versão V12 do banco de dados do SQL Azure.

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um profissional de suporte do Centro de chamada pode identificar os chamadores por vários dígitos do seu número da previdência ou o número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o analista de suporte. Um desenvolvedor pode definir uma regra de mascaramento a ser aplicada a cada resultado de consulta que mascaram todos os dados, exceto os últimos quatro dígitos de qualquer número de previdência social ou número de cartão de crédito no resultado definidos. Outro exemplo, usando a máscara de dados apropriados para proteger os dados de informações de identificação pessoal (PII), um desenvolvedor pode consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## Noções básicas sobre Máscara de dados dinâmica em banco de dados SQL

É possível configurar uma política de máscara de dados dinâmicos no portal de Visualização do Azure selecionando a operação Máscara de Dados Dinâmicos na folha de configuração do Banco de Dados SQL. Antes de configurar a máscara de dados dinâmicos, verifique se você está usando um ["Cliente de nível inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


### Permissões de mascaramento de dados dinâmico

A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

### Política de mascaramento de dados dinâmico

* **Logons privilegiados**: um conjunto de logons que receberá dados desmascarados nos resultados das consultas SQL.
  
* **Regras de mascaramento**: um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de tabela de banco de dados e um nome da coluna.

* **Funções de mascaramento**: um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
|----------|---------------|
| **Padrão** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXXXXXX ou menos Xs se o tamanho do campo tiver menos de 8 caracteres para tipos de dados de cadeia de caracteres (ncharomo , ntext, nvarchar).<br/>• Use um valor de zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para tipos de dados de data/hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variante do SQL, é usado o valor padrão do tipo atual.<br/>• Para XML, o documento <masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types).
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **CPF** |**Método de mascaramento que expõe os dois últimos dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um número de cadastro de pessoas físicas.<br/><br/>XXX-XX-XX12 |
| **Email** | **Método de mascaramento que expõe a primeira letra e substitui o domínio com XXX.com** usando um prefixo de cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** | **Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio.<br/>prefixo[preenchimento]sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### Cadeia de conexão segura

Se você estiver usando um ["Cliente de nível inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), deve atualizar os clientes existentes (exemplo: aplicativos) para usar um formato de cadeia de conexão padrão. Clique [aqui](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) para obter detalhes.

## Configurar a máscara de dados dinâmicos para seu banco de dados usando o Portal de Visualização do Azure

1. Inicie o portal de Visualização do Azure em [https://portal.azure.com](https://portal.azure.com).
	 
2. Navegue até a lâmina de configuração do banco de dados que contém os dados confidenciais que você deseja mascarar.
	
3. Clique no bloco **Máscara de Dados Dinâmicos** que inicia a folha de configuração **Máscara de Dados Dinâmicos**.

	* Se preferir, você pode rolar para baixo até a seção **Operações** e clicar em **Máscara de Dados Dinâmicos**.
	 
	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. Na folha de configuração **Máscara de Dados Dinâmicos**, clique em **Adicionar Máscara** para abrir a folha de configuração **Adicionar Regra de Mascaramento**.

	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. Selecione a **Tabela** e a **Coluna** para definir os campos designados que serão mascarados.

6. Escolha um **Formato do Campo de Máscara** na lista de categorias de mascaramento dos dados confidenciais.

	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. Clique em **Salvar** na folha da regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de máscara de dados dinâmicos.

8. Digite os logons privilegiados que devem ter acesso aos dados confidenciais sem máscara.
 
	![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]Para fazer isso de forma que a camada de aplicativo possa exibir dados confidenciais para usuários com privilégios de aplicativo, adicione o logon de SQL do aplicativo que está sendo usado para a consulta ao banco de dados. É altamente recomendável que essa lista contenha um número mínimo de logons para minimizar a exposição de dados confidenciais.

9. Clique em **Salvar** na folha de configuração de mascaramento de dados para salvar a política de mascaramento nova ou atualizada.

10. Se você estiver usando um ["Cliente de nível inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), deve atualizar os clientes existentes (exemplo: aplicativos) para usar um formato de cadeia de conexão padrão. Para obter mais informações, consulte [Clientes de nível inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

## Configurar a máscara de dados dinâmica para o banco de dados usando cmdlets do Powershell

Consulte [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx).


## Configurar a máscara de dados dinâmica para o banco de dados usando a API REST

Consulte [Operações para Bancos de Dados SQL do Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=06-->