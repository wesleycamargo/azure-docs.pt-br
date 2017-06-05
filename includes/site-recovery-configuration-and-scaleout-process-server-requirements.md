| **Hardware** | |
| --- |---|
| Número de núcleos de CPU| 8 |
| RAM| 12 GB|
| Número de discos | 3 <br><br> - Disco do SO<br> - Disco de cache do servidor de processo<br> - Unidade de retenção (para failback)|
| Espaço livre em disco (cache do servidor de processo) | 600 GB
| Espaço livre em disco (disco de retenção) | 600 GB|
| **Software** | |
| Versão do sistema operacional | Windows Server 2012 R2 |
| Localidade do sistema operacional | Inglês (en-us)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Funções do Windows Server | Não habilite estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V |
| **Rede** | |
| Tipo de placa de interface de rede | VMXNET3 |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor deve ser capaz de acessar as URLs a seguir diretamente ou por meio de um servidor proxy: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (não é necessário para Servidores de Processo de Expansão) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)|
