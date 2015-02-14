## <a name="what-is"> </a>O que é Armazenamento de Filas?

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Uma conta de armazenamento pode conter até 200 TB de dados de blob, tabela e fila. Consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

Usos comuns de Armazenamento de filas incluem:

-   <span>Criar uma lista de pendências de trabalho para processar de maneira assíncrona</span>
-   Transmitir mensagens de uma função web do Azure para uma função
    de trabalho do Azure

## <a name="concepts"> </a>Conceitos

O serviço Fila contém os seguintes componentes:

![Fila1](./media/howto-queue-storage/queue1.png)


- **Formato de URL:** os blobs são endereçáveis usando o seguinte formato de URL:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
A URL a seguir endereça uma das filas no diagrama:  
	http://myaccount.queue.core.windows.net/imagesToDownload 

-**Conta de armazenamento:** todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

- **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila.

- **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB.


<!--HONumber=42-->
