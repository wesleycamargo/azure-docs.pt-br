>[!NOTE]
>Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um tíquete de suporte. **Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

| Recurso | Limite padrão | 
| --- | --- | 
| AMS (Contas de Serviços de Mídia) do Azure em uma única assinatura | 25 (fixo) |
| Ativos por conta AMS | 1.000.000|
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Trabalhos por conta AMS | 50.000<sup>2</sup> |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>4</sup> |
| Canais ao vivo por conta AMS  |5|
| Programas no estado interrompido por canal  |50|
| Programa em estado de execução por canal  |3|
| Pontos de extremidade de streaming no estado “executando” por conta AMS|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| RUs (Unidades Reservadas) de Mídia por conta AMS |25 (S1, S2)<br/>10 (S3) <sup>1</sup> | 
| Contas de armazenamento | 1,000<sup>5</sup> (fixo) |
| Políticas | |1,000,000<sup>6</sup> |
 
<sup>1</sup> S3 RUs não estão disponíveis na Índia Ocidental.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando **IJob.Delete** ou a solicitação HTTP **DELETE**.

<sup>3</sup> Ao fazer uma solicitação para listar as Entidades de trabalho, será retornado um máximo de 1.000 por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM). Para saber mais, consulte [esta](../articles/media-services/media-services-content-protection-overview.md) seção.

<sup>5</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup> há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Você deverá usar a mesma ID de política se estiver sempre usando os mesmos dias/permissões de acesso/etc.



<!--HONumber=Jan17_HO2-->


