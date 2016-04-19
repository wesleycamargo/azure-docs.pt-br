



Dependendo de seu ambiente e opções, o script pode criar toda a infraestrutura de cluster, incluindo a rede virtual do Azure, as contas de armazenamento, os serviços de nuvem, o controlador de domínio, os bancos de dados SQL locais ou remotos, o nó principal, os nós do agente, os nós de computação e os nós do serviço de nuvem do Azure ("intermitentes" ou PaaS). Como alternativa, o script pode usar a infraestrutura do Azure já existente e criar o nó principal do cluster HPC, os nós agentes, os nós de computação e os nós intermitentes do Azure.


Para obter informações detalhadas sobre o planejamento de um cluster Pacote HPC, consulte o conteúdo de [Avaliação e planejamento de produto](https://technet.microsoft.com/library/jj899596.aspx) e de [Introdução](https://technet.microsoft.com/library/jj899590.aspx) na biblioteca de Pacote HPC do TechNet.



## Pré-requisitos

* **Assinatura do Azure**: você pode usar a assinatura no serviço Azure Global ou no Azure China. Seus limites de assinatura afetarão o número e o tipo de nós de cluster que você pode implantar. Para obter informações, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../articles/azure-subscription-service-limits.md).


* **Computador cliente do Windows com o Azure PowerShell 0.8.7 ou posterior instalado e configurado** – Veja [Instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md). O script é executado no modo ASM (Gerenciamento de Serviços do Azure).


* **Script de implantação do Pacote HPC IaaS**: baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.0 do script.

* **Arquivo de configuração do script**: você precisará criar um arquivo XML que usa o script para configurar o cluster HPC. Para obter informações e exemplos, consulte as seções neste artigo.


## Sintaxe

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Você deve executar o script como um administrador.

### Parâmetros

* **ConfigFile**: especifica o caminho do arquivo de configuração para descrever o cluster HPC. Para saber mais, consulte [Arquivo de configuração](#Configuration-file) neste tópico ou o arquivo Manual.rtf na pasta que contém o script.

* **AdminUserName** - especifica o nome de usuário. Se a floresta de domínio for criada pelo script, isso se tornará o nome de usuário de administrador local para todas as VMs, bem como o nome do administrador do domínio. Se já existir a floresta de domínio, o usuário de domínio é especificado como o nome de usuário de administrador local para instalar o Pacote HPC.

* **AdminPassword**: especifica a senha do administrador. Se não estiver especificado na linha de comando, o script solicitará que você digite a senha.

* **HPCImageName** (opcional): especifica o nome de imagem da VM do Pacote HPC usada para implantar o cluster HPC. Ele deve ser uma imagem de Pacote HPC fornecida pela Microsoft no Azure Marketplace. Se não estiver especificado (recomendado na maioria dos casos), o script escolhe a imagem de Pacote HPC publicada mais recentemente.

    >[AZURE.NOTE] A implantação falhará se você não especificar uma imagem válida do Pacote HPC.

* **LogFile** (opcional): especifica o caminho de arquivo de log de implantação. Se não for especificado, o script criará um arquivo de log no diretório temporário do computador que executa o script.

* **Force** (opcional): suprime todas as solicitações de confirmação.

* **NoCleanOnFailure** (opcional): especifica que as VMs do Azure que não forem implantadas com êxito não serão removidas. Você deve remover essas VMs manualmente antes de executar novamente o script para continuar a implantação ou esta poderá falhar.

* **PSSessionSkipCACheck** (opcional) - para cada serviço de nuvem com VMs implantadas por esse script, um certificado autoassinado é automaticamente gerado pelo Azure e todas as VMs no serviço de nuvem usam esse certificado como o certificado do WinRM (Gerenciamento Remoto do Windows) padrão. Para implantar os recursos de HPC nessas VMs do Azure, o script por padrão instala temporariamente esses certificados no repositório de Autoridades de certificação confiáveis/Computador local do computador cliente para suprimir o erro de segurança "AC não confiável" durante a execução do script; os certificados são removidos quando o script termina. Se esse parâmetro for especificado, os certificados não serão instalados no computador cliente e o aviso de segurança será suprimido.

    >[AZURE.IMPORTANT] Esse parâmetro não é recomendado para implantações de produção.

### Exemplo

O exemplo a seguir cria um novo cluster HPC Pack usando o arquivo de configuração *MyConfigFile.xml* e especifica as credenciais administrativas para instalação do cluster.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Considerações adicionais

* O script usa a imagem de VM do Pacote HPC no Azure Marketplace para criar o nó principal do cluster. A imagem mais recente se baseia no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Atualização 3 instalado.

* O script opcionalmente pode habilitar o envio de trabalho por meio do portal da Web Pacote HPC ou da API REST do Pacote HPC.

* O script pode opcionalmente executar scripts de pré e pós-configuração personalizados no nó principal se você quiser instalar o software adicional ou definir outras configurações.


## Arquivo de configuração

O arquivo de configuração para o script de implantação é um arquivo XML. O arquivo de esquema HPCIaaSClusterConfig.xsd está na pasta de script de implantação do Pacote HPC IaaS. **IaaSClusterConfig** é o elemento raiz do arquivo de configuração, que contém os elementos filhos descritos em detalhes no arquivo Manual.rtf na pasta de script de implantação. Para exemplos de arquivos para diferentes cenários, consulte [Arquivos de configuração de exemplo](#Example-configuration-files) neste artigo.

<!---HONumber=AcomDC_0406_2016-->