<properties urlDisplayName="Using Soft Delete" pageTitle="Usando exclus&atilde;o revers&iacute;vel nos Servi&ccedil;os M&oacute;veis (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como usar o recurso de exclus&atilde;o revers&iacute;vel dos Servi&ccedil;os M&oacute;veis do Azure no seu aplicativo" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Usando exclus&atilde;o revers&iacute;vel nos Servi&ccedil;os M&oacute;veis" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Usando exclusão reversível nos Serviços Móveis

Tabelas criadas com o JavaScript ou o back-end do .NET podem, opcionalmente, ter exclusão reversível habilitada. Ao usar a exclusão reversível, uma nova coluna chamada \*\_\_deleted\* do [tipo de bit SQL][tipo de bit SQL] é adicionada ao banco de dados. Com a exclusão reversível habilitada, uma operação de excluir não excluir fisicamente as linhas do banco de dados, mas, em vez disso, define o valor da coluna excluída para TRUE.

Ao consultar registros em uma tabela com exclusão reversível habilitada, as linhas excluídas não são retornadas na consulta por padrão. Para solicitar essas linhas, é preciso passar um parâmetro de consulta \*\_\_includeDeleted=true\* na sua [Operação de Consulta REST][Operação de Consulta REST]. No SDK do cliente .NET, também é possível usar o método auxiliar `IMobileServiceTable.IncludeDeleted()`.

O suporte a exclusão reversível para o back-end do .NET foi lançado pela primeira vez com a versão 1.0.402 do Back-end do .NET de Serviços Móveis do Microsoft Azure. Os pacotes NuGet mais recentes estão disponíveis aqui, [Back-end do .NET de Serviços Móveis do Microsoft Azure][Back-end do .NET de Serviços Móveis do Microsoft Azure].

Alguns dos benefícios em potencial de usar a exclusão reversível:

-   Ao usar o recurso [Sincronização de dados offline para Serviços Móveis][Sincronização de dados offline para Serviços Móveis], o SDK do cliente automaticamente consulta registros excluídos e remove-os do banco de dados local. Sem a exclusão reversível habilitada, é preciso gravar código adicional no back-end para que o SDK do cliente saiba que registros remover do armazenamento local. Caso contrário, o armazenamento local e o back-end do cliente serão inconsistentes com relação a esses registros excluídos e o método do cliente `PurgeAsync()` deve ser chamado para limpar o armazenamento local.
-   Alguns aplicativos têm uma necessidade de negócios de nunca excluir dados fisicamente, ou excluir dados apenas depois de terem sido auditados. O recurso de exclusão reversível pode ser útil nesse cenário.
-   A exclusão reversível pode ser usada para implantar um recurso de “cancelamento de exclusão”, de modo que dados excluídos por engano possam ser recuperados.
    Porém, registros de exclusão reversível ocupam espaço no banco de dados, assim, considere criar um trabalho agendado para periodicamente excluir de modo irreversível os registros da exclusão reversível. Para um exemplo disso, consulte [Usando exclusão reversível com o back-end do .NET][Usando exclusão reversível com o back-end do .NET] e [Usando exclusão reversível com o back-end do JavaScript][Usando exclusão reversível com o back-end do JavaScript]. Seu código de cliente também deve periodicamente chamar `PurgeAsync()`, de modo que esses registros de exclusão irreversível não permaneçam no armazenamento de dados local do dispositivo.

Visão geral deste tópico:

1.  [Habilitando exclusão reversível para o back-end do .NET][Habilitando exclusão reversível para o back-end do .NET]
2.  [Habilitando exclusão reversível para o back-end do JavaScript][Habilitando exclusão reversível para o back-end do JavaScript]
3.  [Usando exclusão reversível com o back-end do .NET][Usando exclusão reversível com o back-end do .NET]
4.  [Usando exclusão reversível com o back-end do JavaScript][Usando exclusão reversível com o back-end do JavaScript]

## <a name="enable-for-dotnet"></a>Habilitando exclusão reversível para o back-end do .NET

O suporte a exclusão reversível para o back-end do .NET foi lançado pela primeira vez com a versão 1.0.402 do Back-end do .NET de Serviços Móveis do Microsoft Azure. Os pacotes NuGet mais recentes estão disponíveis aqui, [Back-end do .NET de Serviços Móveis do Microsoft Azure][Back-end do .NET de Serviços Móveis do Microsoft Azure].

As seguintes etapas o conduzem sobre como habilitar a exclusão reversível para um serviço móvel do back-end do .NET.

1.  Abra seu projeto de serviço móvel de back-end do .NET no Visual Studio.
2.  Clique com o botão direito no projeto de back-end .NET e clique em **Gerenciar pacotes NuGet**.
3.  No diálogo do gerenciador de pacote, clique em **Nuget.org** sob as atualizações e instale a versão 1.0.402 ou posterior dos pacotes NuGet do [Back-end do .NET de Serviços Móveis do Microsoft Azure][Back-end do .NET de Serviços Móveis do Microsoft Azure].
4.  No Gerenciador de Soluções para o Visual Studio, expanda o nó **Controladores** sob seu projeto de back-end do .NET e abra a origem do controlador. Por exemplo, *TodoItemController.cs*.
5.  No método `Initialize()` do seu controlador, passe o parâmetro `enableSoftDelete: true` para o construtor EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }

## <a name="enable-for-javascript"></a>Habilitando exclusão reversível para o back-end do JavaScript

Se estiver criando uma nova tabela para seu serviço móvel, é possível habilitar a exclusão reversível na página de criação da tabela.

![][0]

Para habilitar a exclusão reversível em uma tabela existente no back-end do JavaScript:

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique no seu serviço móvel. Então clique na guia Dados.
2.  Na página de dados, clique para selecionar a tabela desejada. Então clique no botão **Habilitar Exclusão Reversível** na barra de comando. Se a tabela já tiver exclusão reversível habilitada, esse botão não aparecerá, mas você poderá ver a coluna \*\_\_deleted\* ao clicar na guia **Procurar** ou **Colunas** para a tabela.

    ![][1]

    Para desabilitar a exclusão reversível para sua tabela, clique na guia **Colunas** e depois clique na coluna \*\_\_deleted\* e no botão **Excluir**.

    ![][2]

## <a name="using-with-dotnet"></a>Usando exclusão reversível com o back-end do .NET

O seguinte trabalho agendado limpa registros de exclusão reversível com mais de um mês de idade:

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

Para saber mais sobre os trabalhos agendados com Serviços Móveis do back-end do .NET, consulte: [Agendar trabalhos recorrentes com Serviços Móveis do back-end do JavaScript][Agendar trabalhos recorrentes com Serviços Móveis do back-end do JavaScript]

## <a name="using-with-javascript"></a>Usando exclusão reversível com o back-end do JavaScript

Você usa scripts de tabela para adicionar lógica em torno do recurso de exclusão reversível com os serviços móveis do back-end do JavaScript.

Para detectar uma solicitação de cancelamento de exclusão, use a propriedade “restaurar” no script de atualizar tabela:

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }

Para incluir registros excluídos no resultado da consulta em um script, defina o "includeDeleted" para true:

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Para recuperar registros excluídos usando uma solicitação HTTP, inclua o parâmetro de consulta "\_\_includedeleted=true":

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Trabalho agendado de amostra para limpar registros da exclusão reversível.

Esse é um trabalho de amostra que exclui registros atualizados antes de uma data em particular:

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Para saber mais sobre os trabalhos agendados com Serviços Móveis do back-end do JavaScript, consulte: [Agendar trabalhos recorrentes com Serviços Móveis do back-end do JavaScript][3].

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [tipo de bit SQL]: http://msdn.microsoft.com/pt-br/library/ms177603.aspx
  [Operação de Consulta REST]: http://msdn.microsoft.com/pt-br/library/azure/jj677199.aspx
  [Back-end do .NET de Serviços Móveis do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=513165
  [Sincronização de dados offline para Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/
  [Usando exclusão reversível com o back-end do .NET]: #using-with-dotnet
  [Usando exclusão reversível com o back-end do JavaScript]: #using-with-javascript
  [Habilitando exclusão reversível para o back-end do .NET]: #enable-for-dotnet
  [Habilitando exclusão reversível para o back-end do JavaScript]: #enable-for-javascript
  [0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
  [2]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
  [Agendar trabalhos recorrentes com Serviços Móveis do back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/
  [3]: /pt-br/documentation/articles/mobile-services-schedule-recurring-tasks/
