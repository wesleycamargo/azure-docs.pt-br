Nesta etapa, você deve testar ao ouvinte do grupo de disponibilidade usando um aplicativo cliente em execução na mesma rede.

Para a conectividade do cliente, observe os seguintes requisitos:

* As conexões de cliente para o ouvinte devem vir de máquinas que residem em um serviço de nuvem diferente daquele que hospeda as réplicas de disponibilidade do AlwaysOn.
* Se as réplicas do AlwaysOn estiverem em sub-redes diferentes, os clientes deverão especificar “MultisubnetFailover=True” na cadeia de conexão. Isso resulta em tentativas de conexão em paralelo para réplicas nas sub-redes diferentes. Observe que esse cenário inclui uma implantação de grupo de disponibilidade do AlwaysOn entre regiões.

Um exemplo seria conectar-se ao ouvinte de uma das VMs no mesmo VNet do Azure (mas não um que hospeda uma réplica). Uma maneira fácil para concluir este teste é tentar conectar SSMS ao ouvinte do grupo de disponibilidade. Outro método simples é executar[SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)da seguinte maneira:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Se o valor de EndpointPort é 1433, não é requerido especificá-lo na chamada. A chamada anterior também pressupõe que o computador do cliente está conectada ao mesmo domínio e que o chamador tem permissões no banco de dados usando a autenticação do Windows.
> 
> 

Ao testar o ouvinte, certifique-se de realizar failover ao grupo de disponibilidade para garantir que os clientes possam se conectar ao ouvinte através de failovers.

<!---HONumber=Oct15_HO3-->