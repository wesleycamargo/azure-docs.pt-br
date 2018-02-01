| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 12 GB|
| Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback |
| Espaço livre em disco (cache do servidor de processo) | 600 GB
| Espaço livre em disco (disco de retenção) | 600 GB|
| Sistema operacional  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Localidade do sistema operacional | Inglês (en-us)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V |
| Políticas de grupo| Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando <br> - Impedir o acesso às ferramentas de edição do registro <br> - Lógica de confiança para anexos de arquivo <br> - Ativar a execução do script <br> [Saiba mais](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | – Nenhum site da Web padrão já existente <br> - Habilitar [Autenticação anônima](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443<br>|
| Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware) |
| Tipo de endereço IP | estático |
| Acesso à Internet | O servidor precisa de acesso a estas URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (não é necessário para Servidores de Processo de Expansão) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)|
