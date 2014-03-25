Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Clique na guia **Dados** e, em seguida, clique em **+Criar**.
	
   	![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Em **Nome da tabela** digite _TodoItem_ e, em seguida, clique no botão de seleção.

 	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png)

  Isso cria a nova tabela de armazenamento **TodoItem** com as permissões padrão definidas. Isso significa que qualquer pessoa com a chave de aplicativo, que é distribuída com seu aplicativo, pode acessar e alterar dados na tabela. 

> [WACOM.NOTE] 
> O mesmo nome de tabela é usado no início rápido de Serviços Móveis. No entanto, cada tabela é criada em um esquema específico para um determinado serviço móvel. Isso é para evitar colisões de dados quando vários serviços móveis usam o mesmo banco de dados.

4. Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

5. Clique na guia **Colunas**. Verifique se as seguintes colunas padrão são criadas automaticamente para você: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nome da coluna</th>
 	<th>Tipo</th>
 	<th>Índice</th>
 	</tr>
 	<tr>
 	<td>ID</td>
 	<td>string</td>
 	<td>Indexada</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>data</td>
 	<td>Indexada</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>data</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>carimbo de data/hora (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 	
		

  	Este é o requisito mínimo para uma tabela nos Serviços Móveis. 

    <div class="dev-callout"><b>Observação</b>
	<p>Quando o esquema dinâmico estiver ativado no seu serviço móvel, novas colunas serão criadas automaticamente quando objetos JSON forem enviados para o serviço móvel por uma operação de inserção ou atualização.</p>
    </div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.


