## <a name="what-is"> </a>O que é Armazenamento de Filas?

O Armazenamento de filas do Azure é um serviço de armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB em tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Para contas de armazenamento criadas após 8 de junho de 2012, a capacidade total é de 200TB; para contas de armazenamento criadas antes dessa data, a capacidade total é de 100 TB. Consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

Usos comuns de Armazenamento de filas incluem:

-   <span>Crie uma lista de pendências de trabalho para processar de maneira assíncrona</span>
-   Passar mensagens de uma função da Web do Azure para uma função de trabalho do Azure

## <a name="concepts"> </a>Conceitos

O serviço Fila contém os seguintes componentes:

![Fila1](./media/howto-queue-storage/queue1.png)


- **Formato de URL:** filas são acessadas usando o seguinte formato de URL:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 

O URL a seguir atende a uma das filas no diagrama:  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

- **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila.

- **Mensagem:** uma mensagem, em qualquer formato, de até 64KB.



