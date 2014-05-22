<properties umbracoNaviHide="0" pageTitle="Conceitos da conta de armazenamento | Azure" metaKeywords="armazenamento do Azure, serviço de armazenamento, serviço, conta de armazenamento, conta, criar conta de armazenamento, criar conta" description="Saiba sobre os conceitos da conta de armazenamento." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="Como: contas de armazenamento" headerExpose="" footerExpose="" disqusComments="1" title="Conceitos da conta de armazenamento" authors="" />


<h1 id="storageaccountconcepts">Conceitos da conta de armazenamento</h1>


- **armazenamento com redundância geográfica (GRS)**   O armazenamento com redundância geográfica oferece o nível mais alto de durabilidade de armazenamento através da replicação direta de dados para um local secundário dentro da mesma região. Isso habilita o failover no caso de uma falha grave no local principal. O local secundário fica a centenas de quilômetros do local principal. A GRS é implementada por meio de um recurso chamado *replicação geográfica*, que é ativado para uma conta de armazenamento por padrão, mas ele pode ser desativado se você não quiser usá-lo (por exemplo, se as políticas da empresa impedem seu uso). Para obter mais informações, consulte [Apresentando a replicação geográfica para o Azure (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx). 

- **armazenamento com redundância local (LRS)**   O armazenamento com redundâncias local oferece um armazenamento altamente durável e disponível dentro de um único local. Para armazenamento com redundância local, os dados da conta são replicados três vezes no mesmo data center. Todo o armazenamento no Azure é localmente redundante. Para maior durabilidade, você pode ativar a replicação geográfica. O armazenamento redundante local é oferecido com um desconto. Para obter informações sobre preços, consulte [Visão geral de preços do Azure](http://www.windowsazure.com/pt-br/pricing/details/). 

- **grupo de afinidade**   Um *grupo de afinidade* é um agrupamento geográfico das implantações do serviço de nuvem e das contas de armazenamento no Azure. Um grupo de afinidade pode melhorar o desempenho do serviço localizando cargas de trabalho do computador no mesmo data center ou próximo do público-alvo de usuários. Além disso, não há cobranças para saída.

- **pontos de extremidade da conta de armazenamento**   Os *pontos de extremidade* de uma conta de armazenamento representam o nível mais alto de namespace para acessar blobs, tabelas ou filas. Os pontos de extremidade padrão de uma conta de armazenamento têm os seguintes formatos: 

    - Serviço Blob: http://*mystorageaccount*.blob.core.windows.net

    - Serviço Tabela: http://*mystorageaccount*.table.core.windows.net

    - Serviço Fila: http://*mystorageaccount*.queue.core.windows.net

- **URLs da conta de armazenamento**   A URL para acessar um objeto em uma conta de armazenamento é criada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

- **chaves de acesso de armazenamento**   Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso para armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

- **métricas mínimas x detalhadas**   Você pode configurar métricas mínimas ou detalhadas nas configurações de monitoramento de sua conta de armazenamento. *As métricas mínimas* coletam as métricas dos dados, como ingresso/egresso, disponibilidade, latência e porcentagens de êxitos, agregadas para os serviços Blob, Tabela e Fila. *As métricas detalhadas* coletam o detalhe no nível da operação além dos agregados do nível de serviço para as mesmas métricas. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo. Para obter uma lista completa das métricas disponíveis, consulte [o esquema da tabela de métricas da análise do armazenamento](http://msdn.microsoft.com/pt-br/library/windowsazure/hh343264.aspx) Para obter mais informações sobre o monitoramento de armazenamento, consulte [sobre métricas de análise de armazenamento](http://msdn.microsoft.com/pt-br/library/windowsazure/hh343258.aspx).

- **log**   O log é um recurso configurável de contas de armazenamento que habilita o log das solicitações para ler, gravar e excluir blobs, tabelas e filas. Você configura o log no Portal de Gerenciamento do Azure, mas não pode exibir os logs no Portal de Gerenciamento. Os logs são armazenados e acessados na conta de armazenamento, no recipiente $logs. Para obter mais informações, consulte [Visão geral da análise de armazenamento (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh343268.aspx).

