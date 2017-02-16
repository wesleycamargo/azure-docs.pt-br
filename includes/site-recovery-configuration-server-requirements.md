| **Hardware** | |
| --- |---|
| Número de núcleos de CPU| 8 núcleos |
| RAM| 12 GB|
| Número de discos | **3 discos** <br> - Disco do sistema operacional<br> - Disco de Cache do Servidor de Processo<br> - Unidade de Retenção (para Failback)|
| Espaço Livre em Disco (Cache do Servidor de Processo) | 600 GB
| Espaço Livre em Disco (Disco de Retenção) | 600 GB|
| **Software** | |
| Versão do sistema operacional | Windows Server 2012 R2 |
| Localidade do Sistema Operacional | Inglês (en-us)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0 "PowerCLI 6.0")|
| Funções do Windows Server | Não habilite as funções a seguir <br> **Active Directory Domain Services** <br>**Serviços de Informações da Internet** <br> **Hyper-V** |
| **Rede** | |
| Tipo de placa de interface de rede | VMXNET3 |
| Tipo de Endereço IP | Estático |
| Acesso à Internet | O servidor deve ser capaz de acessar a URL a seguir diretamente ou por meio de um Servidor Proxy <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Orquestração do Canal de Controle)<br>9443 (Transporte de Dados)|


<!--HONumber=Jan17_HO3-->


