## O que é armazenamento de fila?

O Armazenamento de Fila do Azure é um serviço para armazenar grandes quantidades de
mensagens que podem ser acessadas de qualquer lugar do mundo por meio de
chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de fila única pode ter
até 64 KB de tamanho, e uma fila pode conter milhões de mensagens, até o
limite de capacidade total de uma conta de armazenamento. Uma conta de armazenamento pode conter até 500 TB de dados de blob, tabela e fila. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

Usos comuns de Armazenamento de filas incluem:

-   <span>Criar uma lista de pendências de trabalho para processar de maneira assíncrona</span>
-   Transmitir mensagens de uma função web do Azure para uma função
    de trabalho do Azure

## Conceitos do serviço Fila

O Serviço de fila contém os seguintes componentes:

![Fila1](./media/storage-queue-concepts-include/queue1.png)


- **Formato de URL:** os blobs são endereçáveis usando o seguinte formato de URL:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
A URL a seguir endereça uma das filas no diagrama:  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**Conta de armazenamento:** Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

- **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila.

- **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB.



<!--HONumber=49-->