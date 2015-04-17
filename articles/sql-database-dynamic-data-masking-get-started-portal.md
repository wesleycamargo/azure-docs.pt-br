<properties 
   pageTitle="Introdução ao Mascaramento de dados dinâmicos no banco de dados SQL (Portal do Azure)" 
   description="Como introdução ao Mascaramento de dados dinâmicos no banco de dados SQL no Portal do Azure" 
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
   ms.date="03/25/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Introdução ao Mascaramento de dados dinâmicos no banco de dados SQL (Portal do Azure)

> [AZURE.SELECTOR]
- [Mascaramento de dados dinâmicos - portal de visualização do Azure](sql-database-dynamic-data-masking-get-started.md)

## Visão geral

A Máscara de dados dinâmica no banco de dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios.  A Máscara de dados dinâmica está na visualização para as camadas de serviço Basic, Standard e Premium na versão V12 do banco de dados do SQL Azure.

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo.  É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um profissional de suporte do Centro de chamada pode identificar os chamadores por vários dígitos do seu número da previdência ou o número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o analista de suporte.  Um desenvolvedor pode definir uma regra de mascaramento a ser aplicada a cada resultado de consulta que mascaram todos os dados, exceto os últimos quatro dígitos de qualquer número de previdência social ou número de cartão de crédito no resultado definidos.  Outro exemplo, usando a máscara de dados apropriados para proteger os dados de informações de identificação pessoal (PII), um desenvolvedor pode consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## Noções básicas sobre Máscara de dados dinâmica em banco de dados SQL

Você configura a política de mascaramento de dados dinâmicos no portal do Azure e conclui a configuração usando a cadeia de conexão habilitada para segurança usada pelo aplicativo ou outros clientes que acessam o banco de dados.

> [AZURE.NOTE] Para configurar o mascaramento de dados dinâmicos no portal de Visualização do Azure, consulte [Introdução ao Mascaramento de dados dinâmico do banco de dados SQL (portal de visualização do Azure)](sql-database-dynamic-data-masking-get-started.md). 

### Permissões de mascaramento de dados dinâmico

A Máscara de dados dinâmica pode ser configurada através de funções do administração do banco de dados do Azure, administrador do servidor ou responsável pela segurança.

### Política de mascaramento de dados dinâmico

* **Logons privilegiados** - um conjunto de logons que receberá os dados revelados nos resultados das consultas SQL.
  
* **Regras de mascaramento** -um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada.  Os campos correspondentes podem ser definidos usando um nome de tabela de banco de dados e o nome da coluna ou usando um nome de alias.

* **Mascaramento por** -pode ser feito na origem ou destino.  O mascaramento pode ser configurado no nível do código-fonte, identificando o nome da **tabela** e o nome da **coluna**, ou no nível dos resultados identificando o **Alias** que é usado na consulta.  Se você estiver familiarizado com a arquitetura de dados do banco de dados e deseja limitar a exposição de todos os resultados da consulta, você pode preferir uma regra de máscara de origem.  Você pode adicionar uma regra de máscara de resultados quando você deseja limitar a exposição aos resultados da consulta sem analisar a arquitetura de dados do banco de dados ou para um campo que pode chegar de origens diferentes.  
  
* **Restrição estendida** - limita a exposição de dados confidenciais, mas pode afetar negativamente a funcionalidade de alguns aplicativos.

>[AZURE.Dica] use a opção **restrição estendida** para restringir o acesso para os desenvolvedores que usam uma conexão direta de acesso ao banco de dados e executam consultas SQL para fins de solução de problemas.

* **Funções de mascaramento** -um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
|----------|---------------|
| **Padrão** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXXXXXX ou menos Xs se o tamanho do campo tiver menos de 8 caracteres para tipos de dados de cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Use um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numérico, smallint, smallmoney, tinyint, float, real).<br/>• Use a hora atual para tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para SQL variant, o valor padrão do tipo atual é usado.<br/>• Para XML o documento <masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (tabela de carimbo de carimbo de data/hora, hierarchyid, GUID, binary, imagem, tipos espaciais varbinary).
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número do seguro social** |**Método de mascaramento que expõe os dois últimos dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo no formulário de um número de seguro social americano.<br/><br/>XXX-XX-XX12 |
| **Email** | **Método de mascaramento que expõe a primeira letra e o domínio** usando uma cadeia de caracteres constante como um prefixo no formulário de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** | **Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais.  Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação][Image1] |
| **Texto personalizado** | **Método de mascaramento que expõe as primeiras e as últimas letras** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio.<br/>prefixo[preenchimento]sufixo<br/><br/>![Painel de navegação][Image2] |

  
<a name="Anchor1"></a>
### Cadeia de conexão segura

Ao configurar o mascaramento dos dados dinâmico, o Azure fornece uma cadeia de conexão de segurança habilitada para o banco de dados.  Somente os clientes que usam essa cadeia de caracteres de conexão têm seus dados confidenciais mascarados de acordo com a política de mascaramento de dados dinâmicos.  Você também precisa atualizar os clientes existentes (exemplo: aplicativos) para usar o novo formato de cadeia de caracteres de conexão.

* Formato de cadeia de caracteres de conexão original:  <*server name*>.database.windows.net
* Cadeia de conexão segura:  <*server name*>.database.**secure**.windows.net

Você também pode alterar a configuração **SECURITY ENABLED ACCESS** de **OPTIONAL** para **REQUIRED**, o que garante que não há nenhuma opção para acessar o banco de dados com a cadeia de conexão original e ignorar a política de mascaramento de dados dinâmicos.  Enquanto você estiver experimentando o mascaramento de dados dinâmico usando clientes específicos (exemplo, um aplicativo de fase de desenvolvimento ou SSMS), escolha **OPTIONAL**.  Para produção, escolha **REQUIRED**.<br/><br/>

![Painel de navegação][Image3]<br/><br/>

## Configurar dados dinâmicos de mascaramento para o banco de dados usando o portal do Azure

1. Iniciar o portal do Azure em [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Clique no banco de dados que você deseja mascarar e, em seguida, clique na guia **AUDITORIA E SEGURANÇA**.

3. Em **mascaramento de dados dinâmicos**, clique em **ENABLED** para habilitar o recurso de mascaramento dos dados dinâmico.  

4. Digite os logons privilegiados que devem ter acesso aos dados confidenciais sem máscara.

	>[AZURE.TIP] Para fazer isso de forma que a camada de aplicativo possa exibir dados confidenciais para usuários do aplicativo com privilégios, adicione o logon do aplicativo que estiver sendo usado para consulta e o banco de dados.  É altamente recomendável que essa lista deva incluir um número mínimo de logons para minimizar a exposição de dados confidenciais.

	![Painel de navegação][Image4]

5. Na parte inferior da página na barra de menus, clique em **Adicionar MÁSCARA** para abrir a janela de configuração da regra de mascaramento.

6. Escolha **Mascarar por** para indicar se o mascaramento é feito na origem ou destino.  O mascaramento pode ser configurado no nível do código-fonte, identificando o nome da **tabela** e o nome da **coluna**, ou no nível dos resultados identificando o **Alias** que é usado na consulta.  Se você estiver familiarizado com a arquitetura de dados do banco de dados e deseja limitar a exposição de todos os resultados da consulta, você pode preferir uma regra de máscara de origem.  Você pode adicionar uma regra de máscara de resultados quando você deseja limitar a exposição aos resultados da consulta sem analisar a arquitetura de dados do banco de dados ou para um campo que pode chegar de origens diferentes.

7. Digite o nome da **Tabela** e o nome da **Coluna**, ou o nome do **Alias**, para definir os campos designados que serão mascarados.

8. Escolha uma **FUNÇÃO DE MASCARAMENTO** a partir da lista de categorias de mascaramento dos dados confidenciais.

	![Painel de navegação][Imagem 5] 
 	
9. Clique em **Atualizar** na janela da regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de mascaramento de dados dinâmicos.

10. Considere selecionar **USE EXTENDED RESTRICTIONS** que limita a exposição de dados confidenciais por meio de consultas ad hoc.

11. Clique em **Salvar** para salvar a regra de mascaramento nova ou atualizada.

## Configurar a máscara de dados dinâmica para o banco de dados usando a API REST

Consulte [Operações para bancos de dados SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

[Imagem 1]: ./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png
[Imagem 2]: ./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png
[Imagem 3]: ./media/sql-database-dynamic-data-masking-get-started-portal/3_DDM_Current_Preview.png
[Imagem 4]: ./media/sql-database-dynamic-data-masking-get-started-portal/4_DMM_Policy_Classic_Portal.png
[Imagem 5]: ./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png

<!--HONumber=49-->