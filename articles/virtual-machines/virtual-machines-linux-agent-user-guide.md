<properties 
	pageTitle="Guia do usuário do agente do Linux | Microsoft Azure" 
	description="Saiba como instalar e configurar o agente Linux (waagent) para gerenciar sua interação de máquina virtual com os Recursos de Infraestrutura do Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2016" 
	ms.author="szark"/>



#Guia do usuário do agente Linux para o Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

##Introdução

O Agente Linux do Azure (/usr/sbin/waagent) gerencia a interação entre uma máquina virtual e o Controlador de Malha do Azure. Consulte também [Como atualizar o Agente Linux do Azure](virtual-machines-linux-update-agent.md) para obter informações sobre como instalar e atualizar o agente em uma VM em execução.

O Agente Linux do Azure é responsável pelo seguinte:

> [AZURE.NOTE] Confira o [LEIAME](https://github.com/Azure/WALinuxAgent/blob/master/README.md) do agente Linux do Azure para obter a versão mais recente deste guia.

* **Provisionamento de imagem**
  - Criar uma conta do usuário
  - Configurar tipos de autenticação do SSH
  - Implantar chaves públicas do SSH e pares de chaves
  - Define o nome do host
  - Publica o nome do host para a plataforma de DNS
  - Relata impressão digital da chave host SSH para a plataforma
  - Gerencia o disco do recurso
  - Formata e monta o disco do recurso
  - Configura o espaço de permuta
* **Rede**
  - Gerencia as rotas para melhorar a compatibilidade com os servidores DHCP de plataforma
  - Garante a estabilidade do nome da interface de rede
* **Kernel**
  - Configura NUMA virtual
  - Consome entropia de Hyper-V para /dev/random
  - Configura os tempos limite de SCSI para o dispositivo raiz (o qual poderia ser remoto)
* **Diagnostics**
  - Redireciona o console para a porta serial
* **Implantações SCVMM**
    - Detecta e inicializa o agente VMM para Linux quando executado em um ambiente de System Center Virtual Machine Manager 2012 R2
* **Extensão de VM**
    - Injete o componente criado pela Microsoft e seus Parceiros na VM do Linux (IaaS) para habilitar o software e a automação da configuração
    - Implementação de referência de extensão de VM em [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


##Comunicação

O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um DVD anexado ao tempo de inicialização para as implementações de IaaS. Este DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de configuração que não seja os pares de chaves SSH real.

* Um ponto de extremidade TCP expondo uma API REST usada para obter a implantação e a configuração de topologia.

###Obtenção do agente Linux
Você pode obter o agente mais recente do Linux diretamente de:

- [Diferentes provedores de distribuição endossando o Linux no Azure](http://support.microsoft.com/kb/2805216)
- ou do [Repositório de software livre do GitHub para o Agente Linux do Azure](https://github.com/Azure/WALinuxAgent)


## Requisitos
Os sistemas a seguir foram testados e funcionam com o agente Linux do Azure. **Observe que essa lista pode ser diferente da lista oficial de sistemas com suporte na plataforma Microsoft Azure**, conforme descrito aqui: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

###Distribuições Linux com suporte

* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+
* Red Hat 6.7+

Outros sistemas com suporte:

* FreeBSD 9+ (Agente Linux do Azure v2.0.10+)


O agente do Linux depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.6+
* OpenSSL 1.0 +
* Openssh 5.3+
* Utilitários de sistema de arquivos: sfdisk, fdisk, mkfs, parted
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: roteamento ip


##Instalação

Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Agente Linux do Azure.

Se instalar manualmente, o 'waagent' deve ser copiado para /usr/sbin/waagent e instalado executando:

	# sudo chmod 755 /usr/sbin/waagent
	# sudo /usr/sbin/waagent -install -verbose

Arquivo de log do agente é mantido em /var/log/waagent.log.


##Opções de linha de comando

###Sinalizadores

- verbose: aumentar o nível de detalhes do comando especificado
- forçar: Ignorar confirmação interativa para alguns comandos

###Comandos

- Ajuda: lista os comandos com suporte e sinalizadores.

- instalar: instalação Manual do agente
 * Verifica o sistema para dependências necessárias

 * Cria o script de inicialização de SysV (/ etc/init.d/waagent), o arquivo de configuração do logrotate (/etc/logrotate.d/waagent) e configura a imagem para executar o script de inicialização na inicialização

 * Arquivo de configuração função Web de gravações para /etc/waagent.conf

 * Qualquer arquivo de configuração existente é movido para /etc/waagent.conf.old

 * Detectar a versão do kernel e aplica a solução VNUMA, se necessário

 * Move as regras udev que podem interferir na rede (/ lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) para/var/lib/waagent /

- desinstalar: remover waagent e arquivos associados
 * Cancela o registro do script de inicialização do sistema e o exclui

 * Exclui a configuração do logrotate e o arquivo de configuração waagent em /etc/waagent.conf

 * Restaura quaisquer regras udev movidas que foram movidas durante a instalação

 * Não há suporte para a reversão automática da solução alternativa VNUMA; edite os arquivos de configuração do GRUB manualmente para reabilitar NUMA, se necessário.

- deprovision: tentativa de limpar o sistema e torná-lo adequado para reprovisionamento. Esta operação excluiu o seguinte:
 * Todas as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)

 * Configuração de servidor de nomes em /etc/resolv.conf

 * Senha raiz do /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)

 * Concessões de cliente DHCP em cache

 * Reinicia o nome de host para localdomain.localdomain

 **Aviso:** o desprovisionamento não garante que a imagem esteja limpa de todas as informações confidenciais e adequada para redistribuição.

- deprovision + user: executa tudo em - deprovision (acima) e também exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) e dados associados. Este parâmetro é quando a desconfiguração de uma imagem que foi anteriormente provisionamento no Azure para podem ser capturada e usada novamente.

- versão: exibe a versão do waagent

- serialconsole: configura GRUB para marcar ttyS0 (a primeira porta serial) como o console de inicialização. Isso garante que os logs de inicialização do kernel são enviados para a porta serial e disponibilizados para depuração.

- daemon: executar waagent como um daemon para gerenciar a interação com a plataforma. Esse argumento é especificado para waagent no script de inicialização de waagent.

##Configuração

Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. Uma amostra do arquivo de configuração é mostrada abaixo:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Várias opções de configuração são descritas em detalhes abaixo. Há três tipos opções de configuração: Booliano, String ou Integer. As opções de configuração booliana podem ser especificadas como "y" ou "n". A palavra-chave especial "Nenhum" pode ser usado para entradas de configuração de tipo algum sequência conforme detalhado abaixo.

**Role.StateConsumer:**

Tipo: Padrão de Cadeia de Caracteres: Nenhum

Se for especificado um caminho para um programa executável, ele é chamado quando waagent forneceu a imagem e o estado "Pronto" está prestes a ser relatado à malha. O argumento especificado para o programa serão "Pronto". O agente não aguardará para que o programa retorne antes de continuar.

**Role.ConfigurationConsumer:**

Tipo: Padrão de Cadeia de Caracteres: Nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um arquivo de configuração está disponível para a máquina virtual. O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que for alterado o arquivo de configuração. O caminho atual desse arquivo é /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Tipo: Padrão de Cadeia de Caracteres: Nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um novo layout de topologia de rede está disponível para a máquina virtual. O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que a topologia de rede é alterado (devido ao serviço reparo por exemplo). O caminho atual desse arquivo é /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Tipo: Padrão Booliano: y

Isso permite que o usuário habilite ou desabilite a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se o provisionamento for desabilitado, as chaves SSH de host e usuário da imagem serão preservadas e qualquer configuração especificada na API de provisionamento do Azure será ignorada.

**Observação:** esse parâmetro segue o padrão "n" do Ubuntu Cloud Images, que usa cloud-init para provisionamento.

**Provisioning.DeleteRootPassword:**

Tipo: Padrão Booliano: n

Se definido, a senha raiz no arquivo sombra é apagado durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**

Tipo: Padrão Booliano: y

Se o conjunto de todos os SSH host pares de chaves (ecdsa, dsa e rsa) será excluído durante o processo de provisionamento de /etc/ssh /. E um único par de chave novo é gerado.

O tipo de criptografia para o novo par de chaves é configurável pela entrada do Provisioning.SshHostKeyPairType. Observe que algumas distribuições novamente criará pares de chaves SSH para todos os tipos de criptografia está faltando quando é reiniciado o daemon do SSH (por exemplo, após uma reinicialização).

**Provisioning.SshHostKeyPairType:**

Tipo: Padrão de Cadeia de Caracteres: rsa

Isso pode ser definido como um tipo de algoritmo de criptografia com suporte pelo daemon SSH na máquina virtual. Os valores geralmente aceitos são "rsa", "dsa" e "ecdsa". Observe que "putty.exe" no Windows não oferece suporte a "ecdsa". Portanto, se você pretende usar putty.exe no Windows para conectar-se a uma implantação do Linux, use "rsa" ou "dsa".

**Provisioning.MonitorHostName:**

Tipo: Padrão Booliano: y

Se definido, waagent monitorará máquina virtual Linux para alterações de nome do host (conforme retornado pelo comando "hostname") e atualizar automaticamente a configuração de rede da imagem para refletir a alteração. Para enviar a alteração do nome para os servidores DNS, a rede será reiniciado na máquina virtual. Isso resultará em resumo perda de conectividade com a Internet.

**ResourceDisk.Format:**

Tipo: Padrão Booliano: y

Se definido, o disco de recursos fornecido pela plataforma será formatado e montado por waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk.Filesystem" for algo diferente de "ntfs". Uma única partição do tipo Linux (83) será disponibilizada no disco. Observe que essa partição não será formatada se ele pode ser montado com êxito.

**ResourceDisk.Filesystem:**

Tipo: Padrão de Cadeia de Caracteres: ext4

Especifica o tipo de sistema de arquivos para o disco do recurso. Valores aceitos variam de acordo com a distribuição do Linux. Se a sequência for X, em seguida, mkfs.X deve estar presente na imagem do Linux. Imagens de 11 SLES geralmente devem utilizar 'ext3'. FreeBSD imagens devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**

Tipo: Padrão de Cadeia de Caracteres: /mnt/resource

Especifica o caminho em que o disco do recurso é montado. Observe que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada.

**ResourceDisk.EnableSwap:**

Tipo: Padrão Booliano: n

Se definir um arquivo de permuta (/ arquivo de permuta) é criado no disco recursos e adicionado ao espaço de troca de sistema.

**ResourceDisk.SwapSizeMB:**

Tipo: Padrão de Inteiro: 0

Especifica o tamanho máximo do arquivo de permuta em megabytes.

**LBProbeResponder:**

Tipo: Padrão Booliano: y

Se definido, waagent responderá ao balanceador de carga de testes da plataforma (se presente).

**Logs.Verbose:**

Tipo: Padrão Booliano: n

Se definido, a verbosidade do log é aumentado. Waagent faz /var/log/waagent.log e aproveita a funcionalidade de logrotate do sistema para girar os logs.

**OS.RootDeviceScsiTimeout:**

Tipo: Padrão de Inteiro: 300

Isso configura o tempo limite de SCSI em segundos nos drives de disco e os dados de SO. Se não for definido, o sistema de padrões são usados.

**OS.OpensslPath:**

Tipo: Padrão de Cadeia de Caracteres: Nenhum

Isso pode ser usado para especificar um caminho alternativo para o openssl binário a ser usado para operações de criptografia.



##Imagens de nuvem do Ubuntu

Observe que as Imagens de Nuvem do Ubuntu utilizam [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que de outra forma seriam gerenciadas pelo Agente Linux do Azure. Observe as seguintes diferenças:


- **Provisioning.Enabled** segue o padrão "n" nas Imagens de Nuvem do Ubuntu que usam cloud-init para executar tarefas de provisionamento.

- Os seguintes parâmetros de configuração não têm efeito nas Imagens de Nuvem do Ubuntu que usam cloud-init para gerenciar o disco de recurso e o espaço de troca:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consulte os seguintes recursos para configurar o ponto de montagem do disco de recurso e o espaço de troca nas Imagens de Nuvem do Ubuntu durante o provisionamento:

 - [Wiki do Ubuntu: configurar partições de troca](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Injetando dados personalizados em uma Máquina Virtual do Azure](virtual-machines-windows-classic-inject-custom-data.md)

 

<!---HONumber=AcomDC_0713_2016-->