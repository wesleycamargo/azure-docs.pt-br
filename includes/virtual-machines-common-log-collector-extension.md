
O diagnóstico de problemas com um serviço de nuvem do Microsoft Azure exige a coleta de arquivos de log do serviço em máquinas virtuais à medida que os problemas ocorrem. É possível usar a extensão AzureLogCollector sob demanda para executar uma coleção avulsa de logs de uma ou mais VMs de Serviço de Nuvem (de funções Web e funções de trabalho) e transferir os arquivos coletados para uma conta de armazenamento do Azure – tudo sem precisar fazer logon remotamente em qualquer uma das VMs.

> [AZURE.NOTE]É possível encontrar descrições da maioria das informações registradas em log em http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Há dois modos de coleta, dependendo dos tipos de arquivos a ser coletados.
- Somente Logs de GA (Agente Convidado) do Azure. Esse modo de coleta inclui todos os logs relacionados a agentes convidados do Azure e a outros componentes do Azure.
- Todos os Logs (Completo). Esse modo de coleta obterá todos os arquivos do modo GA e:

  - logs de eventos do sistema e do aplicativo
  
  - logs de erros de HTTP
  
  - Logs IIS
  
  - Logs de instalação
  
  - outros logs do sistema

Em ambos os modos de coleta, é possível especificar pastas de coleta de dados adicionais usando uma coleta da seguinte estrutura:

- **Nome**: o nome da coleta, que será usado como o nome da subpasta no arquivo zip a ser coletado.

- **Local**: o caminho para a pasta na máquina virtual onde o arquivo será coletado.

- **SearchPattern**: o padrão dos nomes de arquivos a serem coletados. O padrão é “*”

- **Recursivo**: se os arquivos serão coletados recursivamente na pasta.

<!-----HONumber=AcomDC_0323_2016-->