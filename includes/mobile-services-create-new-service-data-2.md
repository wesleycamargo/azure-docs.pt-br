Para ser capaz de armazenar dados de aplicativo no novo serviço móvel, primeiro você deve criar uma nova tabela na instância do banco de dados SQL associada.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Clique na guia **Dados** e, em seguida, clique em **+Criar**.

    ![mobile-data-tab-empty][mobile-data-tab-empty]

    Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3.  No **Nome da tabela**, digite *TodoItem* e, em seguida, clique no botão de verificação.

    ![mobile-create-todoitem-table][mobile-create-todoitem-table]

  Isso cria a nova tabela de armazenamento **TodoItem** com as permissões padrão definidas. Isso significa que qualquer pessoa com a chave de aplicativo, que é distribuída com seu aplicativo, pode acessar e alterar dados na tabela.

> [WACOM.NOTE]
> O mesmo nome de tabela é usado no início rápido de Serviços Móveis. No entanto, cada tabela é criada em um esquema específico para um determinado serviço móvel. Isso é para evitar colisões de dados quando vários serviços móveis usam o mesmo banco de dados.

1.  Clique na nova tabela **TodoItem** e verifique se não há linhas de dados.

2.  Clique na guia **Colunas**. Verifique se as seguintes colunas padrão são criadas automaticamente para você:

    | Nome da coluna | Tipo                         | Índice                             |
    |----------------|------------------------------|------------------------------------|
    | ID             | string                       | Indexada                           |
    | \_\_createdAt  | data                         | Indexada                           |
    | \_\_updatedAt  | data                         | <font color="transparent">-</font> |
    | \_\_version    | carimbo de data/hora (MSSQL) | <font color="transparent">-</font> |

    Este é o requisito mínimo para uma tabela nos Serviços Móveis.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando o esquema din&acirc;mico estiver ativado no seu servi&ccedil;o m&oacute;vel, novas colunas ser&atilde;o criadas automaticamente quando objetos JSON forem enviados para o servi&ccedil;o m&oacute;vel por uma opera&ccedil;&atilde;o de inser&ccedil;&atilde;o ou atualiza&ccedil;&atilde;o.</p>
</div>

Agora você está pronto para usar o novo serviço móvel como armazenamento de dados para o aplicativo.

  [mobile-data-tab-empty]: ./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png
  [mobile-create-todoitem-table]: ./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png
