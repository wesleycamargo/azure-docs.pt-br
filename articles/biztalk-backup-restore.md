<properties linkid="biztalk-backup-restore" urlDisplayName="Serviços BizTalk: backup e restauração" pageTitle="Serviços BizTalk: backup e restauração | Azure" metaKeywords="" description="Os Serviços BizTalk incluem recursos de backup e restauração. Ao criar um backup, é feito um instantâneo da configuração dos Serviços BizTalk." metaCanonical="" services="" documentationCenter="" title="Serviços BizTalk: backup e restauração" authors=""  solutions="" writer="mandia" manager="paulettm" editor="cgronlun"  />


# Serviços BizTalk: backup e restauração

Os Serviços BizTalk do Azure incluem recursos de backup e restauração. Ao criar um backup, é feito um instantâneo da configuração dos Serviços BizTalk do Azure.

Considere os seguintes cenários:


- Você pode fazer o backup da configuração do Serviço do BizTalk de duas maneiras usando o Portal de Gerenciamento do Azure - criando backups conforme necessário (backups ad-hoc) e criando backups agendados.

- O conteúdo de backup pode ser restaurado para o mesmo Serviço do BizTalk ou para um novo Serviço do BizTalk. Para restaurar o Serviço do BizTalk usando o mesmo nome, o Serviço do BizTalk existente precisa ser excluído. Caso contrário, haverá falha na restauração.

- Os Serviços BizTalk podem ser restaurados para a mesma edição ou para uma edição superior. Não há suporte para a restauração do Serviço do BizTalk para uma edição inferior, de quando foi feito um backup.

	Por exemplo, um backup usando a Basic Edition pode ser restaurado para a Premium Edition. Um backup usando a Premium Edition não pode ser restaurado para a Standard Edition.

- O backup dos números de controle de EDI são feitos para manter a continuidade dos números de controle. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.

- O backup e a restauração não estão disponíveis como parte dos Serviços BizTalk Developer Edition. 

Este tópico descreve como fazer backup e restaurar os Serviços BizTalk usando o Portal de Gerenciamento do Azure. Você também pode fazer backup dos Serviços BizTalk usando APIs REST. Para obter mais informações a respeito, consulte [API REST dos Serviço do BizTalk (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx). 

[Do que é feito backup?](#budata)

[Criar um backup](#createbu)

[Restaurar](#restore)


##<a name="budata"></a>Do que é feito backup?

Quando um backup é criado, os seguintes itens são copiados:

<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>Itens do backup</TH> 
</TR> 
<TR>
<td colspan="2">
 <strong>Portal dos Serviços BizTalk do Azure</strong></td>
</TR> 
<TR>
<TD>Configuração e tempo de execução</TD> 
<TD><bl>
<li>Detalhes e perfil do parceiro</li>
<li>Contratos de parceiro</li>
<li>Assemblies personalizados implantados</li>
<li>Pontes implantadas</li>
<li>Certificados</li>
<li>Transformações implantadas</li>
<li>Pipelines</li>
<li>Modelos criados e salvos no Portal de Serviços BizTalk</li>
<li>Mapeamentos de X12 ST01 e GS01</li>
<li>Números de controle (EDI)</li>
<li>Valores de MIC de mensagens AS2</li>
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Serviço do BizTalk do Azure</strong></td>
</TR> 
<TR>
<TD>Certificado SSL</TD> 
<TD>
<bl>
<li>Dados do certificado SSL</li>
<li>Senha do certificado SSL</li>
</bl>
</TD>
</TR> 
<TR>
<TD>Configurações do Serviço do BizTalk</TD> 
<TD>
<bl>
<li>Contagem de unidades da escala</li>
<li>Edição</li>
<li>Versão do produto</li>
<li>Região/Datacenter</li>
<li>O namespace e a chave do ACS (Serviço de Controle de Acesso)</li>
<li>Cadeia de conexão do banco de dados de acompanhamento</li>
<li>Cadeia de conexão da conta de armazenamento de arquivamento</li>
<li>Cadeia de conexão da conta de armazenamento de monitoramento</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>Itens adicionais</strong></td>
</TR> 
<TR>
<TD>Banco de Dados de Acompanhamento</TD> 
<TD>Quando o Serviço do BizTalk é provisionado, os detalhes do Banco de Dados de Acompanhamento são inseridos, incluindo o Servidor do Banco de Dados SQL do Azure e o nome do Banco de Dados de Acompanhamento. O backup do Banco de Dados de Acompanhamento não é feito automaticamente.<br/><br/>
<strong>Importante</strong><br/>
Se o Banco de Dados de Acompanhamento for excluído acidentalmente, e o banco de dados precisar ser recuperado, deverá existir um backup anterior. Se não existir um backup, o Banco de Dados de Acompanhamento e seus dados não poderão ser recuperados. Nessa situação, crie um novo Banco de Dados de Acompanhamento com o mesmo nome do banco de dados. A Replicação Geográfica é recomendada.</TD>
</TR> 
</table>

##<a name="createbu"></a>Criar um backup

Um backup pode ser obtido a qualquer momento e é totalmente controlado por você. Você pode fazer backups no Portal de Gerenciamento do Azure ou na API REST do Serviço do BizTalk. Para criar um backup usando a API REST dos Serviços BizTalk, consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=325584">Backup do Serviço do BizTalk (a página pode estar em inglês)</a>

Esta seção fornece instruções sobre como fazer backups usando o Portal de Gerenciamento. Você pode usar o Portal de Gerenciamento para criar backups ad-hoc ou agendar backups em intervalos desejados.

##<a name="beforebackup"></a>Antes de criar um backup

Siga as seguintes considerações antes de criar um backup:

1. Antes de executar um backup ad hoc, para mensagens ativas em um lote, processe o lote de mensagens. Isso ajudará a evitar a perda de mensagens <i>se</i> esse backup for restaurado. As mensagens em lotes nunca são armazenadas ao fazer um backup. Com os backups agendados, talvez não seja possível garantir que não exista nenhuma mensagem no lote quando o backup for iniciado.
	<div class="dev-callout"><b>Observação</b>
<p>Se um backup for feito com mensagens ativas em um lote, o backup dessas mensagens não será feito e, portanto, serão perdidas.</p></div>
2. Opcional: no Portal dos Serviços BizTalk, interrompa todas as operações de gerenciamento.
4. Crie o backup na conta de Armazenamento usando os comandos de <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=325584">Backup do Serviço do BizTalk (a página pode estar em inglês)</a> disponíveis com a API REST.

[Backups ad-hoc](#backupnow)

[Agendar um backup](#backupschedule)

###<a name="backupnow"></a>Backups ad-hoc
1. No Portal de Gerenciamento do Azure, clique em Serviços BizTalk e clique no nome do Serviço do BizTalk do qual você deseja fazer backup.
2. No <b>Painel</b> do Serviço do BizTalk, clique em <b>Fazer Backup</b> na parte inferior da página.
3. Na caixa de diálogo <b>Fazer Backup do Serviço do BizTalk</b>, forneça um nome de backup.
4. Selecione uma conta de armazenamento de blob e clique na marca de seleção para iniciar o backup.


Quando o backup for concluído, um contêiner com o nome do backup especificado será criado sob a conta de armazenamento. Esse contêiner contém a configuração de backup do Serviço do BizTalk.


###<a name="backupschedule"></a>Agendar um backup

1. No Portal de Gerenciamento do Azure, clique em Serviços BizTalk, clique no nome do Serviço do BizTalk para o qual você deseja agendar backups automáticos e, em seguida, clique na guia **Configurar**.
2. Para **Status do Backup**, selecione **Nenhum**, se não desejar agendar backups automatizados. Para agendar backups automatizados, clique em **Automático**.
3. Em **Conta de Armazenamento**, selecione uma conta de armazenamento do Azure onde os backups serão criados.
4. Em **Frequência**, especifique a data e a hora de início do primeiro backup e o intervalo (em dias) no qual um backup é feito.
5. Em **Dias de Retenção**, especifique uma janela de tempo (em dias) na qual os backups são mantidos. O período de retenção deve ser maior do que a frequência de backup.
6. Selecione a caixa de seleção **Sempre manter pelo menos um backup** para certificar-se de que exista pelo menos um backup disponível, mesmo que o período de dias de retenção tenha passado.
7. Clique em **Salvar**.

Quando um trabalho de backup agendado é executado, ele cria um contêiner (para armazenar os dados de backup) na conta de armazenamento especificada. O nome do contêiner é no formato *Nome-data-hora do Serviço do BizTalk*. 

Se um backup falhar, a página do painel Serviço do BizTalk mostrará o status do backup como **Falhou**.

![Último status do backup agendado][BackupStatus] 

Você pode clicar no link para ir para a página Logs de operação de Serviços de Gerenciamento para obter mais informações sobre a falha. Para obter mais informações sobre logs de operação em relação aos Serviços BizTalk, consulte [Serviços BizTalk: solucionar problemas usando os logs de operação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=391211)

##<a name="restore"></a>Restaurar

Você pode restaurar backups no Portal de Gerenciamento do Azure ou na API REST dos Serviços BizTalk. Esta seção fornece instruções sobre como restaurar usando o Portal de Gerenciamento. Para restaurar usando a API REST, consulte [Restaurar o Serviço do BizTalk do Backup (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=325582)

###Antes de restaurar um backup

Ao restaurar um backup, considere o seguinte:

- Os novos armazenamentos de acompanhamento, arquivamento e monitoramento podem ser especificados ao restaurar um Serviço do BizTalk.

- Para restaurar o Serviço do BizTalk usando o mesmo nome, exclua o Serviço do BizTalk existente antes de iniciar a restauração. Caso contrário, haverá falha na restauração.

- Os mesmos dados de tempo de execução do EDI são restaurados. O backup do tempo de execução do EDI restaura os números de controle. Os números de controle restaurados estão em sequência da hora do backup. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.

####Para restaurar um backup

1. No Portal de Gerenciamento do Azure, clique em <b>Novo</b>, aponte para <b>Serviços de Aplicativo</b>, <b>Serviços BizTalk</b> e, em seguida, clique em <b>Restaurar</b>.

	![Restaurar um backup][Restore]

2. No assistente de Novo Serviço do BizTalk - Assistente de Restauração, na página <b>Restaurar Serviço do BizTalk</b>, clique no ícone de pasta na caixa de texto <b>URL do Backup</b> para abrir a caixa de diálogo <b>Procurar Armazenamento em Nuvem</b>. A caixa de diálogo lista as contas de armazenamento do Azure.

	Expanda a conta de armazenamento especificada durante a criação ou o agendamento do backup e selecione o nome do contêiner em que você precisa restaurar a configuração do Serviço do BizTalk.

	No painel à direita, selecione o arquivo .txt correspondente ao backup do qual você está restaurando e, em seguida, clique em <b>Abrir</b>.

3. Na página <b>Restaurar Serviço do BizTalk</b>, forneça as seguintes informações:
- Forneça o nome de um Serviço do BizTalk. Por padrão, o nome do Serviço do BizTalk com backup é usado.
- Verifique a URL do domínio, a edição e a região do Serviço do BizTalk restaurado.
- Escolha criar uma nova instância do banco de dados SQL para o banco de dados de acompanhamento e, em seguida, clique na seta à direita.

4. 	Na página <b>Especificar configurações de banco de dados</b>, verifique o nome do banco de dados SQL, especifique o servidor físico onde o banco de dados SQL será criado e um nome de usuário/senha desse servidor.

	Se desejar definir configurações avançadas para o banco de dados SQL, selecione a caixa de seleção <b>Definir Configurações Avançadas de Banco de Dados</b> e, em seguida, clique na seta à direita.

	Se não desejar definir as configurações avançadas de banco de dados, clique na seta à direita e, em seguida, vá para a etapa 6 abaixo.
5. Na página <b>Configurações avançadas de banco de dados</b>, selecione a edição do banco de dados que você deseja usar (<b>Web</b> ou <b>Business</b>), especifique o tamanho máximo do banco de dados e as regras de agrupamento. Clique na seta à direita.

6. Na página <b>Especificar configurações de monitoramento/arquivamento</b>, crie uma nova conta de armazenamento ou especifique uma conta de armazenamento existente onde as informações de monitoramento do Serviço do BizTalk serão armazenadas.

7. Clique na marca de seleção para iniciar o processo de restauração</b>.

8. Quando a restauração for concluída com êxito, um novo Serviço do BizTalk será listado em um estado suspenso na página Serviços BizTalk no Portal de Gerenciamento do Azure.

###<a name="postrestore"></a>Depois de restaurar um backup

O Serviço do BizTalk sempre é restaurado em um estado **Suspenso**. Isso permite que você faça alterações na configuração em seus aplicativos do Serviço do BizTalk, conforme necessário, antes que o novo ambiente esteja funcional. A seguir estão algumas das considerações que você deve fazer antes de iniciar o recém-restaurado Serviço do BizTalk:

- Se você tiver criado aplicativos do Serviço do BizTalk usando o SDK dos Serviços BizTalk do Azure, você precisará atualizar as credenciais do ACS nesses aplicativos para trabalhar com o ambiente restaurado.

- Você pode restaurar um Serviço do BizTalk para replicar um ambiente de Serviço do BizTalk já funcional. Em uma situação como essa, se você tiver contratos configurados no portal dos Serviços BizTalk original que usem compartilhamentos FTP como origens, convém atualizar os contratos no ambiente recém-restaurado para usar outras origens ou compartilhamentos FTP. Se não for possível fazer isso, você poderá ter dois contratos diferentes tentando efetuar pull da mesma mensagem.

- Se você usou a operação de restauração para ter vários ambientes do Serviço do BizTalk, certifique-se de ter como destino o ambiente certo usando aplicativos do Visual Studio, cmdlets do PowerShell, APIs REST ou APIs de OM de Gerenciamento de Parceiros Comerciais.

- Também é uma boa prática configurar backups automáticos no ambiente do Serviço do BizTalk Service recém-restaurado.

Depois de ter atendido essas ou outras considerações relacionadas, vá para a página Serviço do BizTalk no Portal de Gerenciamento do Azure, selecione o Serviço do BizTalk recém-restaurado em estado suspenso e, em seguida, clique em **Retomar** na parte inferior da página para iniciar o serviço.

## Avançar

Para provisionar os Serviços BizTalk do Azure no Portal de Gerenciamento do Azure, consulte [Serviços BizTalk: provisionando com o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Consulte também
- [Fazer o backup do Serviço do BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar o Serviço do BizTalk do backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços BizTalk: gráfico do status do provisionamento (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Serviços BizTalk: guias Painel, Monitor e Escala (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços BizTalk: limitação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Serviços BizTalk: nome e chave do emissor (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png

