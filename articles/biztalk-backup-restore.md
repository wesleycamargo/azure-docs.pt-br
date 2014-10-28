<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="mandia"  solutions="" writer="mandia" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Serviços do BizTalk: Backup e restauração

Os Serviços BizTalk do Azure incluem recursos de backup e restauração. Este tópico descreve como fazer backup e restaurar os Serviços do BizTalk usando o Portal de Gerenciamento do Azure, incluindo:

-   [Antes de começar][Antes de começar]
-   [Criar um backup][Criar um backup]
-   [Restaurar um backup][Restaurar um backup]
-   [Do que é feito backup][Do que é feito backup]

Você também pode fazer backup dos Serviços do BizTalk usando os [API REST dos Serviços do BizTalk][API REST dos Serviços do BizTalk].

## <a name="beforebackup"></a>Antes de começar

-   É possível que o backup e a restauração não estejam disponíveis para todas as edições. Consulte [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Tabela de edições].

    **Observação** As conexões híbridas não feitas backup, independentemente da Edição.

-   Usando o Portal de Gerenciamento do Azure, você pode criar um backup sob demanda ou criar um backup agendado.

-   O conteúdo de backup pode ser restaurado para o mesmo Serviço do BizTalk ou para um novo Serviço do BizTalk. Para restaurar o Serviço do BizTalk usando o mesmo nome, o Serviço do BizTalk existente precisa ser excluído. Caso contrário, haverá falha na restauração.

-   Os Serviços do BizTalk podem ser restaurados para a mesma edição ou para uma edição superior. Não há suporte para a restauração dos Serviços do BizTalk para uma edição inferior, de quando foi feito um backup.

    Por exemplo, um backup usando a Basic Edition pode ser restaurado para a Premium Edition. Um backup usando a Premium Edition não pode ser restaurado para a Standard Edition.

-   O backup dos números de controle de EDI são feitos para manter a continuidade dos números de controle. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.

-   Se um lote tiver mensagens ativas, processe o lote **antes** de executar um backup. Ao criar um backup (conforme necessário ou agendado), as mensagens em lotes nunca são armazenadas.

    **Se um backup for feito com mensagens ativas em um lote, o backup dessas mensagens não será feito e, portanto, serão perdidas.**

-   Opcional: no Portal dos Serviços do BizTalk, interrompa todas as operações de gerenciamento.

## <a name="createbu"></a>Criar um backup

Um backup pode ser obtido a qualquer momento e é totalmente controlado por você. Esta seção lista as etapas para criar backups usando o Portal de Gerenciamento do Azure, incluindo:

[Backup sob demanda][Backup sob demanda]

[Agendar um backup][Agendar um backup]

#### <a name="backupnow"></a>Backup sob demanda

1.  No Portal de Gerenciamento do Azure, selecione **Serviços de BizTalk** e, em seguida, selecione o Serviço de BizTalk para fazer backup.
2.  Na guia **Painel**, selecione **Backup** na parte inferior da página.
3.  Insira um nome de backup. Por exemplo, insira *myBizTalkService*BU*Data*.
4.  Escolha uma conta de armazenamento de blob e selecione a marca de seleção para iniciar o backup.

Quando o backup for concluído, um contêiner com o nome do backup inserido será criado na conta de armazenamento. Esse contêiner contém a configuração de backup do Serviço do BizTalk.

#### <a name="backupschedule"></a>Agendar um backup

1.  No Portal de Gerenciamento do Azure, selecione **Serviços de BizTalk**, selecione o nome do Serviço de BizTalk que você deseja fazer backup e, em seguida, selecione a guia **Configurar**.
2.  Defina o **Status de Backup** para **Automático**.
3.  Selecione a **Conta de Armazenamento** para armazenar o backup, insira a **Frequência** para criar o backup, e quanto tempo manter os backups (**Dias de retenção**):

    ![][]

    **Observações**

-   Em **Dias de retenção**, o período de retenção deve ser maior do que a frequência de backup.
-   Selecione **Sempre manter pelo menos um backup**, mesmo que o período de retenção tenha passado.

1.  Selecione em **Salvar**.

Quando um trabalho de backup agendado é executado, ele cria um contêiner (para armazenar os dados de backup) na conta de armazenamento especificada. O nome do contêiner é nomeado *Nome-data-hora do Serviço do BizTalk*.

Se o painel do Serviço do BizTalk mostra um status **Falhou**:

![Último status do backup agendado][Último status do backup agendado]

O link abre os Logs de Operação de Serviços de Gerenciamento para ajudar solucionar os problemas. Consulte [Serviços do BizTalk: Solução de problemas usando os logs de operação][Serviços do BizTalk: Solução de problemas usando os logs de operação].

## <a name="restore"></a>Restaurar

Você pode fazer backups no Portal de Gerenciamento do Azure ou na [Restaurar API REST do Serviço do BizTalk][Restaurar API REST do Serviço do BizTalk]. Esta seção lista as etapas para restaurar usando o Portal de Gerenciamento.

#### Antes de restaurar um backup

-   Os novos armazenamentos de acompanhamento, arquivamento e monitoramento podem ser especificados ao restaurar um Serviço do BizTalk.

-   Para restaurar o Serviço do BizTalk usando o mesmo nome, exclua o Serviço do BizTalk existente antes de iniciar a restauração. Caso contrário, haverá falha na restauração.

-   Os mesmos dados de tempo de execução do EDI são restaurados. O backup do tempo de execução do EDI restaura os números de controle. Os números de controle restaurados estão em sequência da hora do backup. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.

#### Restaurar um backup

1.  No Portal de Gerenciamento do Azure, selecione **Novo** \> **Serviços do aplicativo** \> **Serviço do BizTalk** \> **Restaurar**:

    ![Restaurar um backup][1]

2.  Na **URL de backup**, selecione o ícone da pasta e expanda a conta de armazenamento do Azure que armazena o backup de configuração do Serviço de BizTalk. Expanda o contêiner e no painel esquerdo, selecione o backup correspondente .txt file.
    Selecione **Abrir**.

3.  Na página **Restaurar o Serviço do BizTalk**, insira um **Nome de Serviço do BizTalk** e verifique a **URL do domínio**, **Edição**, e **Região** para o Serviço de BizTalk restaurado. **Criar uma nova instância de banco de dados do SQL** para o banco de dados de acompanhamento:

    ![][2]

    Selecione a seta de avanço.

4.  Verificar o nome do banco de dados SQL, insira o servidor físico onde será criado o banco de dados SQL, e um nome de usuário/senha para aquele servidor.

    Se você quiser configurar a edição, tamanho, e outras propriedades do banco de dados SQL, selecione **Definir as configurações avançadas do banco de dados**.

    Selecione a seta de avanço.

5.  Criar uma nova conta de armazenamento ou especificar uma conta de armazenamento existente para o Serviço do BizTalk.

6.  Clique na marca de seleção para começar a restauração.

Quando a restauração for concluída com êxito, um novo Serviço do BizTalk será listado em um estado suspenso na página Serviços do BizTalk no Portal de Gerenciamento do Azure.

### <a name="postrestore"></a>Depois de restaurar um backup

O Serviço do BizTalk sempre é restaurado em um estado **Suspenso**. Neste estado, você pode fazer qualquer alteração de configuração antes que o novo ambiente esteja funcional, incluindo:

-   Se você tiver criado aplicativos do Serviço do BizTalk usando o SDK dos Serviços do BizTalk do Azure, você precisará atualizar as credenciais do ACS nesses aplicativos para trabalhar com o ambiente restaurado.

-   Você restaura um Serviço do BizTalk para replicar um ambiente de Serviço do BizTalk existente. Nesta situação, se houver contratos configurados no portal original de Serviços do BizTalk que usam uma pasta FTP de origem, é possível que precise atualizar os contratos no ambiente recentemente restaurado para usar uma pasta FTP de origem diferente. De outra forma, pode haver dois contratos diferentes tentando puxar a mesma mensagem.

-   Se você fez a restauração para ter vários ambientes do Serviço do BizTalk, certifique-se de ter como destino o ambiente certo nos aplicativos do Visual Studio, cmdlets do PowerShell, APIs REST ou APIs de OM de Gerenciamento de Parceiros Comerciais.

-   É uma boa prática configurar backups automáticos no ambiente do Serviço do BizTalk Service recém-restaurado.

Para iniciar o Serviço do BizTalk no Portal de Gerenciamento do Azure, selecione o Serviço de BizTalk e selecione **Reiniciar** na barra de tarefas.

## <a name="budata"></a>Do que é feito backup

Quando um backup é criado, os seguintes itens são copiados:

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Itens do backup

</th>
</tr>
<tr>
<td colspan="2">
**Portal dos Serviços BizTalk do Azure**

</td>
</tr>
<tr>
<td>
Configuração e tempo de execução

</td>
<td>
-   Detalhes e perfil do parceiro
-   Contratos de parceiro
-   Assemblies personalizados implantados
-   Pontes implantadas
-   Certificados
-   Transformações implantadas
-   Pipelines
-   Modelos criados e salvos no Portal de Serviços BizTalk
-   Mapeamentos de X12 ST01 e GS01
-   Números de controle (EDI)
-   Valores de MIC de mensagens AS2

</td>
</tr>
<tr>
<td colspan="2">
**Serviço do BizTalk do Azure**

</td>
</tr>
<tr>
<td>
Certificado SSL

</td>
<td>
-   Dados do certificado SSL
-   Senha do certificado SSL

</td>
</tr>
<tr>
<td>
Configurações do Serviço do BizTalk

</td>
<td>
-   Contagem de unidades da escala
-   Edição
-   Versão do produto
-   Região/Datacenter
-   O namespace e a chave do ACS (Serviço de Controle de Acesso)
-   Cadeia de conexão do banco de dados de acompanhamento
-   Cadeia de conexão da conta de armazenamento de arquivamento
-   Cadeia de conexão da conta de armazenamento de monitoramento

</td>
</tr>
<tr>
<td colspan="2">
**Itens adicionais**

</td>
</tr>
<tr>
<td>
Banco de Dados de Acompanhamento

</td>
<td>
Quando o Serviço do BizTalk é criado, os detalhes do Banco de Dados de Acompanhamento são inseridos, incluindo o Servidor do Banco de Dados SQL do Azure e o nome do Banco de Dados de Acompanhamento. O backup do Banco de Dados de Acompanhamento não é feito automaticamente.
 **Importante**
 Se o Banco de Dados de Acompanhamento for excluído e o banco de dados precisar ser recuperado, deverá existir um backup anterior. Se não existir um backup, o Banco de Dados de Acompanhamento e seus dados não poderão ser recuperados. Nessa situação, crie um novo Banco de Dados de Acompanhamento com o mesmo nome do banco de dados. A Replicação Geográfica é recomendada.

</td>
</tr>
</table>
## Avançar

Para criar Serviços do BizTalk do Azure no Portal de Gerenciamento do Azure, consulte [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]. Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)][Serviços BizTalk do Azure (a página pode estar em inglês)].

## Consulte também

-   [Fazer o backup do Serviço BizTalk][API REST dos Serviços do BizTalk]
-   [Restaurar o Serviço BizTalk do backup][Restaurar API REST do Serviço do BizTalk]
-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]
-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: Gráfico de status do provisionamento][Serviços do BizTalk: Gráfico de status do provisionamento]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]
-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]

  [Antes de começar]: #beforebackup
  [Criar um backup]: #createbu
  [Restaurar um backup]: #restore
  [Do que é feito backup]: #budata
  [API REST dos Serviços do BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=325584
  [Serviços do BizTalk: Tabela de edições]: http://azure.microsoft.com/pt-BR/documentation/articles/biztalk-editions-feature-chart/
  [Backup sob demanda]: #backupnow
  [Agendar um backup]: #backupschedule
  []: ./media/biztalk-backup-restore/AutomaticBU.png
  [Último status do backup agendado]: ./media/biztalk-backup-restore/status-last-backup.png
  [Serviços do BizTalk: Solução de problemas usando os logs de operação]: http://go.microsoft.com/fwlink/?LinkId=391211
  [Restaurar API REST do Serviço do BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=325582
  [1]: ./media/biztalk-backup-restore/restore-ui.png
  [2]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
  [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: Gráfico de status do provisionamento]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=302335
