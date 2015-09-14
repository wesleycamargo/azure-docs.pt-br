Recurso|Limite padrão|Limite máximo
---|---|---
AMS (Contas de Serviços de Mídia) do Azure em uma única assinatura||25
Ativos por conta AMS||1\.000.000
Tarefas encadeadas por trabalho||30
Ativos por tarefa||50
Ativos por trabalho||100
Trabalhos por conta AMS ||50\.000<sup>2</sup>
Localizadores exclusivos associados a um ativo simultaneamente||5<sup>4</sup>
Canais ao vivo por conta AMS</p></td>|5</p></td>|N/A<sup>1</sup>
Programas no estado “interrompido” por canal</p></td>|50</p></td>|N/A<sup>1</sup>
Programa no estado “executando” por canal </p></td>|3</p></td>|N/A<sup>1</sup>
Pontos de extremidade de streaming no estado “executando” por conta AMS</p></td>|2</p></td>|N/A<sup>1</sup>
Unidades de streaming por ponto de extremidade de streaming</p></td>|10 </p></td>|N/A<sup>1</sup>
Unidades de codificação por conta AMS</p></td>|25</p></td>|N/A<sup>1</sup>
Contas de armazenamento | |1,000<sup>5</sup>

<sup>1</sup> Você pode solicitar para atualizar os limites para essa cota abrindo um tíquete de suporte. Não crie mais contas AMS para aumentar os limites; em vez disso, envie um tíquete de suporte.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando solicitação HTTP de **IJob.Delete** ou **DELETE**.

<sup>3</sup> Ao fazer uma solicitação para listar as Entidades de trabalho, será retornado um máximo de 1.000 por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

<sup>5</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<!---HONumber=September15_HO1-->