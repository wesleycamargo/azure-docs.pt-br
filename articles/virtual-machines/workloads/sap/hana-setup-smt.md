---
title: Como configurar o servidor SMT de instalação para o SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como configurar o servidor SMT para o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982618"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>Configurando o servidor SMT para o SUSE Linux
As Instâncias Grandes do SAP HANA não têm conectividade direta com a Internet. Portanto, não é um processo simples registrar uma unidade como essa no provedor do sistema operacional e baixar e aplicar patches. No caso do SUSE Linux, uma solução pode ser a configuração de um servidor SMT em uma VM do Azure. Ao contrário da VM do Azure, que precisa ser hospedada em uma VNet do Azure, que está conectada à Instância Grande do HANA. Com um servidor SMT desse tipo, a unidade de Instância Grande do HANA pode registrar e baixar patches. 

O SUSE fornece um guia maior em sua [Ferramenta de Gerenciamento de Assinaturas para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Como uma pré-condição para a instalação de um servidor SMT que realiza a tarefa para a Instância Grande do HANA, você precisará:

- Uma VNet do Azure que está conectada ao circuito de ER da Instância Grande do HANA.
- Uma conta SUSE associada a uma organização. Ao contrário da organização, que precisa ter uma assinatura válida do SUSE.

## <a name="installation-of-smt-server-on-azure-vm"></a>Instalação do servidor SMT em uma VM do Azure

Nesta etapa, você instala o servidor SMT em uma VM do Azure. A primeira medida é fazer logon para o [Centro de atendimento do SUSE](https://scc.suse.com/).

Quando estiver conectado, acesse Organização--> Credenciais da Organização. Nesta seção, você deve encontrar as credenciais necessárias para configurar o servidor SMT.

A terceira etapa é instalar uma VM SUSE Linux na VNet do Azure. Para implantar a VM, pegue uma imagem de galeria do SLES 12 SP2 do Azure (selecione imagem SUSE BYOS). No processo de implantação, não defina um nome DNS nem use endereços IP estáticos, como visto nesta captura de tela

![implantação da VM no servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A VM implantada era uma VM menor e obteve o endereço IP interno 10.34.1.4 na VNet do Azure. O nome da VM era smtserver. Após a instalação, a conectividade com as unidades de Instância grande do HANA foi verificada. Dependendo de como você organizou a resolução de nomes, talvez precise configurar a resolução das unidades de Instância Grande do HANA em etc/hosts da VM do Azure. Adicione outro disco à VM que será usado para manter os patches. O disco de inicialização em si pode ser muito pequeno. No caso demonstrado, o disco foi montado em /srv/www/htdocs, conforme mostrado na captura de tela a seguir. Um disco de 100 GB deve ser suficiente.

![implantação da VM no servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Faça logon nas unidades de Instância Grande do HANA, mantenha /etc/hosts e verifique se você consegue acessar a VM do Azure que deve executar o servidor SMT na rede.

Depois que essa verificação for feita com êxito, você precisará fazer logon na VM do Azure que deve executar o servidor SMT. Se você estiver usando o putty para fazer logon na VM, precisará executar esta sequência de comandos na janela bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Depois de executar esses comandos, reinicie o bash para ativar as configurações. Depois, inicie o YAST.

Conecte sua VM (smtserver) para o site do SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois que a VM estiver conectada ao site do SUSE, instale os pacotes de smt. Digite o seguinte comando para instalar os pacotes necessários.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Você também pode usar a ferramenta YAST para instalar os pacotes smt. No YAST, acesse Manutenção de Software e pesquise smt. Selecione smt, que alterna automaticamente para yast2-smt, conforme mostrado abaixo

![SMT no yast](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para a instalação no smtserver. Depois de instalado, acesse a configuração do servidor SMT e insira as credenciais da organização da Central do Cliente do SUSE recuperadas anteriormente. Insira também seu nome do host da VM do Azure como a URL do Servidor SMT. Nesta demonstração, ela era https://smtserver, conforme exibido no próximo gráfico.

![Configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Como a próxima etapa, você precisa testar se a conexão com a Central do Cliente do SUSE funciona. Como você vê nos gráficos a seguir, no caso de demonstração, isso funcionou.

![Testar a conexão à Central do Cliente do SUSE](./media/hana-installation/image7_test_connect.png)

Após o início da instalação do SMT, você precisará fornecer uma senha de banco de dados. Como essa é uma nova instalação, é necessário definir essa senha, conforme mostrado no próximo gráfico.

![Definir a senha do banco de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A próxima interação que ocorre é quando um certificado é criado. Percorra a caixa de diálogo, conforme mostrado a seguir e a etapa deverá continuar.

![Criar um certificado para o servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

A etapa “Executar verificação de sincronização” ao final da configuração pode levar alguns minutos. Após a instalação e configuração do servidor SMT, você deverá encontrar o repositório do diretório sob o ponto de montagem /srv/www/htdocs/, além de alguns subdiretórios no repositório. 

Reinicie o servidor SMT e seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>Download de pacotes no servidor SMT

Depois que todos os serviços forem reiniciados, selecione os pacotes apropriados no Gerenciamento SMT usando o Yast. A seleção do pacote depende da imagem do sistema operacional do servidor da Instância Grande do HANA e não da liberação ou versão do SLES da VM que executa o servidor SMT. Um exemplo da tela de seleção é mostrado abaixo.

![Selecionar pacotes](./media/hana-installation/image10_select_packages.PNG)

Depois de concluir a seleção do pacote, você precisará iniciar a cópia inicial dos pacotes selecionados para o servidor SMT configurado. Essa cópia é disparada no shell com o comando smt-mirror, conforme mostrado abaixo


![Baixar os pacotes no servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Como você vê acima, os pacotes devem ser copiados para os diretórios criados no ponto de montagem /srv/www/htdocs. Esse processo pode levar algum tempo. Dependendo de quantos pacotes você selecionar, isso pode levar até uma hora ou mais.
Quando esse processo for concluído, você precisará ir para a instalação do cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de Instância Grande do HANA

Nesse caso, os clientes são as unidades de Instância Grande do HANA. A instalação do servidor SMT copiou o script clientSetup4SMT.sh para a VM do Azure. Copie esse script para a unidade de Instância Grande do HANA que você deseja conectar ao servidor SMT. Inicie o script com a opção -h e dê a ele como parâmetro o nome do servidor SMT. Neste exemplo, smtserver.

![Configurar o cliente SMT](./media/hana-installation/image12_configure_client.PNG)

Pode haver um cenário em que a carga do certificado do servidor pelo cliente foi bem-sucedida, mas o registro falhou, conforme mostrado abaixo.

![Falha no registro do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registro falhou, leia este [documento de suporte do SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e execute as etapas descritas nele.

> [!IMPORTANT] 
> Como o nome do servidor, você precisa fornecer o nome da VM, nesse caso, smtserver, sem o nome de domínio totalmente qualificado. Apenas o nome da VM funciona. 

Depois que essas etapas forem executadas, você precisará executar o seguinte comando na unidade de Instância Grande do HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> Em nossos testes, sempre precisamos aguardar alguns minutos após essa etapa. O clientSetup4SMT.sh de execução imediata, após as medidas corretivas descritas no artigo sobre o SUSE, terminou com mensagens informando que o certificado ainda não era válido. Geralmente, aguardar de 5 a 10 minutos e executar clientSetup4SMT.sh terminou com uma configuração de cliente bem-sucedida.

Se você teve o problema que precisou corrigir de acordo com as etapas descritas no artigo sobre o SUSE, precisará reiniciar clientSetup4SMT.sh na unidade de Instância Grande do HANA novamente. Agora ele deverá ser concluído com êxito, conforme mostrado abaixo.

![Registro do cliente bem-sucedido](./media/hana-installation/image14_finish_client_config.PNG)

Com essa etapa, você configurou o cliente SMT da unidade de Instância Grande do HANA para se conectar ao servidor SMT instalado na VM do Azure. Agora, você pode usar “zypper up” ou “zypper in” para instalar patches dos sistema operacional nas Instâncias Grandes do HANA ou instalar outros pacotes. Fica entendido que você somente pode obter patches baixados antes no servidor SMT.

**Próximas etapas**
- Veja [Instalação do HANA em HLI](hana-example-installation.md).











