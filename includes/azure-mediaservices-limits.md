>[!NOTE]
>Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um tíquete de suporte. **Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

| Recurso | Limite padrão | 
| --- | --- | 
| AMS (Contas de Serviços de Mídia) do Azure em uma única assinatura | 25 (fixo) |
| RUs (Unidades Reservadas) de Mídia por conta AMS |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Trabalhos por conta AMS | 50,000<sup>(2)</sup> |
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por conta AMS | 1.000.000|
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>(4)</sup> |
| Canais ao vivo por conta AMS  |5|
| Programas no estado interrompido por canal  |50|
| Programa em estado de execução por canal  |3|
| Pontos de extremidade de streaming no estado “executando” por conta AMS|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| Contas de armazenamento | 1,000<sup>(5)</sup> (fixo) |
| Políticas | 1,000,000<sup>(6)</sup> |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>7</sup> |
  
<sup>1</sup> S3 RUs não estão disponíveis na Índia Ocidental. Se você alterar o tipo (por exemplo, de S2 a S1), os limites de RU máximos serão redefinidos.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando **IJob.Delete** ou a solicitação HTTP **DELETE**.

A partir de 1º de abril de 2017, qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, junto com seus registros de Tarefas associados, mesmo que o número total de registros esteja abaixo da cota máxima. Se você precisar arquivar as informações de trabalho/tarefa, poderá usar o código descrito [aqui](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Ao fazer uma solicitação para listar as Entidades de trabalho, serão retornados no máximo 1.000 trabalhos por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM). Para saber mais, consulte [esta](../articles/media-services/media-services-content-protection-overview.md) seção.

<sup>5</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup> há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Você deverá usar a mesma ID de política se estiver sempre usando os mesmos dias/permissões de acesso/etc. Para obter informações e um exemplo, veja [esta](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) seção.

<sup>7</sup>Se você estiver carregando conteúdo para um Ativo nos Serviços de Mídia do Azure para processá-lo com um dos processadores de mídia no serviço (ou seja, codificadores como o Media Encoder Standard e o Media Encoder Premium Workflow ou mecanismos de análise como Detecção Facial), esteja ciente das restrições de tamanhos máximos de arquivo com suporte. 

O tamanho máximo com suporte de um único blob atualmente é até 5 TB de Armazenamento de Blobs do Azure. No entanto, os limites adicionais aplicam-se nos Serviços de Mídia do Azure com base nos tamanhos de VM usados pelo serviço. A tabela a seguir mostra os limites em cada uma das Unidades Reservadas de Mídia (S1, S2, S3). Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você estiver codificando fontes de resolução 4K de longa duração, deverá usar Unidades Reservadas de Mídia S3 para alcançar o desempenho necessário. Se você tiver um conteúdo de 4K maior do que o limite de 260 GB das Unidades Reservadas de Mídia S3, entre em contato conosco em amshelp@microsoft.com para se informar sobre atenuantes possíveis que darão suporte ao seu cenário.

| Tipo de Unidade Reservada de Mídia | Tamanho máximo de entrada (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
