<properties
  pageTitle="Preparando o backup de cargas de trabalho no Microsoft Azure | Microsoft Azure"
  description="Este artigo oferece uma visão geral dos recursos de carga de trabalho de aplicativos no Backup do Microsoft Azure"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Preparando o backup de cargas de trabalho no Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

Este artigo é sobre a preparação do seu ambiente para fazer backup de cargas de trabalho, de forma que você possa fazer backup dos seus dados no Azure. Com o servidor de Backup do Microsoft Azure, você pode proteger cargas de trabalho de aplicativo como VMs do Hyper-V, o Microsoft SQL Server, o SharePoint Server, o Microsoft Exchange e os clientes do Windows em:

- **Disco** (D2D), fornecendo altas RTOs para cargas de trabalho de nível 1
- **Azure** (D2D2C) para retenção a longo prazo

E você pode gerenciar a proteção de diversas entidades protegidas (servidores e clientes) de uma interface do usuário de logon único.

Você pode implantar um servidor de Backup do Microsoft Azure como:

- Um servidor autônomo físico.
- Uma máquina virtual do Hyper-V - você pode executar o DPM como uma máquina virtual hospedada em um servidor host do Hyper-V local para fazer backup dos dados locais. Para obter uma lista de considerações sobre este ambiente, consulte [Instalar o DPM como uma máquina virtual em um servidor do Hyper-V local](https://technet.microsoft.com/library/dn440941.aspx).
- Uma máquina virtual do Windows no VMWare — você pode implantar o DPM para oferecer proteção para cargas de trabalho da Microsoft em execução em máquinas virtuais do Windows no VMWare. Neste cenário, o DPM pode ser implantado como um servidor autônomo físico, como uma máquina virtual do Hyper-V ou como uma máquina virtual do Windows no VMWare.
- Uma máquina virtual do Azure - você pode executar o DPM como uma máquina virtual no Azure para fazer backup de cargas de trabalho de nuvem em execução como máquinas virtuais do Azure. Para saber mais sobre essa implantação, consulte [instalar o DPM como uma máquina virtual do Azure](https://technet.microsoft.com/library/hh758176.aspx).

Além disso,

- o Backup do Microsoft Azure pode ser instalado no Windows Server 2008 R2 SP1, 2012 e 2012 R2.
- O servidor de Backup do Microsoft Azure não pode ser instalado em um computador com o agente RA SCDPM ou SCDPM instalado.
- O servidor de Backup do Microsoft Azure não pode ser instalado em um computador com o agente do Backup do Microsoft Azure instalado e registrado em um cofre de Backup do Azure.

>[AZURE.NOTE]O servidor de Backup do Microsoft Azure herda a funcionalidade do Data Protection Manager (DPM) para backup de carga de trabalho, mas ele não fornece proteção em fita ou se integra ao System Center.

Assim que você selecionar o servidor no qual instalará o Backup do Microsoft Azure, precisará:

- Garantir que os pré-requisitos do servidor de Backup do Microsoft Azure sejam atendidos
- Criar um cofre de Backup
- Baixar as credenciais do cofre e
- Baixar os arquivos de instalação do servidor de Backup do Microsoft Azure.

Cada um deles será explicado em detalhes a seguir:

## Antes de começar

Hoje, o servidor de Backup do Microsoft Azure só está disponível para a localidade inglês. Atualmente, os Serviços de Recuperação do Microsoft Azure estão disponíveis em todas as regiões nas quais o Azure está disponível, exceto no datacenter do Microsoft Azure Governamental e no Microsoft Azure na China.

Para uma instalação suave, verifique se os seguintes pré-requisitos são atendidos antes de instalar o servidor de backup do Microsoft Azure.

- O servidor deve ter conectividade com a Internet. O servidor deve ser capaz de acessar o Microsoft Azure.
- Os requisitos do servidor para a instalação do Backup do Microsoft Azure são iguais aos do DPM. Consulte esses [requisitos de hardware](https://technet.microsoft.com/library/dn726764.aspx) para obter informações adicionais.
- O servidor de Backup do Microsoft Azure deve ter ingressado no domínio.
- O servidor de Backup do Microsoft Azure deve ter os recursos .Net 3.5, .Net 4.0, .Net 3.5 SP1 instalados. Consulte [mais informações sobre como habilitar o .Net Framework](https://technet.microsoft.com/library/dn482071.aspx) para obter informações adicionais.
- O servidor de Backup do Microsoft Azure deve ter o Windows Management Framework 4.0 instalado. Você pode baixá-lo [aqui](http://www.microsoft.com/download/details.aspx?id=40855).
- Para o disco usado como espaço dedicado para armazenamento de dados do DPM, o tamanho do pool de armazenamento recomendado é de 1,5 vez o tamanho dos dados protegidos. Para saber mais, consulte a seção sobre os discos e o armazenamento de [este tópico] (servidor https://technet.microsoft.com/library/hh758176.aspx#DPM).

Prepare o servidor de Backup do Microsoft Azure para fazer backup de dados:

1. **Criando um cofre de Backup** — crie um cofre no console do Backup do Azure.
2. **Baixando as credenciais do cofre** — no Backup do Azure, carregue o certificado de gerenciamento que você criou para o cofre.
3. **Baixando o servidor de Backup do Microsoft Azure** — você pode baixar o servidor de Backup do Microsoft Azure para cargas de trabalho de aplicativo da Página de Início Rápido de um cofre de backup.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Baixar o servidor de Backup do Microsoft Azure
Semelhante às credenciais do cofre, você pode baixar o Backup do Microsoft Azure para cargas de trabalho de aplicativo da **Página de Início Rápido** do cofre de backup.

1. Clique em **Para Cargas de Trabalho do Aplicativo (Disco para Disco para Nuvem)**.

    ![Tela de boas-vindas do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Clique em **Download**.

    ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Selecione todos os arquivos e clique em **Avançar**. ![Centro de download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]Como o tamanho do download de todos os arquivos juntos é superior a 3G, em um link de download de 10 Mbps link ele poderá levar até 60 minutos para ser concluído.

4. Coloque todos os arquivos na mesma pasta.

5. Baixe todos os arquivos provenientes da página de download do Backup do Microsoft Azure.

## Instalar o servidor de Backup do Microsoft Azure
Verifique se os pré-requisitos mencionados na seção anterior foram atendidos antes de iniciar a instalação.

1. Depois de baixar todos os arquivos, clique em **MicrosoftAzureBackupInstaller.exe**. O **Assistente de Instalação do Backup do Microsoft Azure** será exibido.

    ![Instalador do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. Forneça o local neste servidor onde os arquivos serão extraídos e clique em **Avançar**.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. Clique em **Extrair** para iniciar a extração dos arquivos de instalação.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. Marque a caixa para iniciar o arquivo setup.exe para começar a instalar o servidor de Backup do Microsoft Azure e clique em **Concluir**.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. Clique em **Backup do Microsoft Azure** para iniciar o assistente de instalação.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. Na tela de boas-vindas, clique em **Avançar**.

    ![Tela de boas-vindas do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. Clique em **Verificar** para determinar se os pré-requisitos de hardware e de software para o servidor de Backup do Microsoft Azure foram atendidos.

    ![Pré-requisito 1 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. Se todos os pré-requisitos tiverem sido atendidos com êxito, você verá uma mensagem indicando que o computador atende aos requisitos. Clique em **Próximo**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. O servidor de Backup do Microsoft Azure requer o SQL Server Standard. Selecionar uma instância local do SQL Server Standard 2014 existente ou permita que o assistente instale o SQL Server Standard. Clique em **Verificar e Instalar** para verificar se o servidor tem os pré-requisitos SQL instalados.

    ![SQL 1 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    Se ocorrer uma falha com uma recomendação de reiniciar o computador, faça isso e clique em **Verificar Novamente**.

    ![Falha de Filtro do SiS](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. Depois que os pré-requisitos tiverem sido instalados com êxito, clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Forneça um local para a instalação dos arquivos do servidor de Backup do Microsoft Azure e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]O local temporário é um requisito para fazer backup do Azure. Verifique se o local temporário é de pelo menos 5% dos dados planejados para fazer backup na nuvem. Para proteção de disco, será necessário configurar discos separados após a conclusão da instalação. Para saber mais sobre pools de armazenamento, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

12. Forneça uma senha forte para as contas de usuário local restritas e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]A configuração de senhas fortes é essencial para a segurança do seu sistema. Uma senha forte é uma senha com pelo menos seis caracteres, que não contém todo ou parte do nome da conta do usuário e que contém pelo menos três destas quatro categorias de caracteres: caracteres maiúsculos, caracteres minúsculos, dígitos de base 10 e símbolos (como!, @, #).

13. Selecione se você deseja usar o *Microsoft Update* para verificar se há atualizações e clique em **Avançar**.

    >[AZURE.NOTE]É recomendável fazer o Windows Update ser redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos, como o servidor de Backup do Microsoft Azure.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. Examine o *Resumo das Configurações* e clique em **Instalar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    A instalação acontece em fases. Na primeira fase, um agente é instalado no servidor e você precisa registrar o servidor com as credenciais do cofre de backup válido do Azure baixado com este produto. Em seguida, o assistente verifica a conectividade direta com a Internet. Se houver conectividade com a Internet, você poderá prosseguir com a instalação; caso contrário, será necessário fornecer detalhes de proxy para se conectar à Internet.

15. Clique em **Avançar** para iniciar a fase de instalação do Agente de Serviços de Recuperação do Microsoft Azure.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/proxy.png)

    O assistente verifica a existência de pré-requisitos e instalará os que estiverem ausentes.

16. Clique em **Instalar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. Quando concluir a instalação do agente, clique em **Avançar** para registrar esse servidor no cofre do Backup do Azure.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Forneça suas credenciais do cofre do Backup do Azure para registrar o servidor e clique em **Avançar**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Forneça uma senha usada para criptografar/descriptografar os dados enviados para o Azure e forneça um local para armazenar essa senha. Você pode gerar automaticamente uma senha ou fornecer sua própria senha mínima de 16 caracteres. Clique em **Próximo**.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]Para manter a confidencialidade, o servidor de Backup do Microsoft Azure não envia essa senha com os dados no Azure. Você precisa armazenar essa senha, que será necessária durante a restauração de dados do Azure, em um local seguro.

    Assim que o registro do servidor de Backup do Microsoft Azure for concluído com êxito, a instalação prosseguirá para a instalação e a configuração do SQL Server Standard 2014 (se escolhido).

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    Após a conclusão da instalação do SQL, os componentes do servidor de Backup do Microsoft Azure serão instalados.

    ![Pré-requisito 2 do Backup do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. Quando o produto estiver instalado e os ícones da área de trabalho tiverem sido criados, clique duas vezes no ícone para iniciar o produto.

    ![Ícones](./media/backup-azure-microsoft-azure-backup/icons.png)

## Adicionar um disco ao pool de armazenamento

Para fazer backup de cargas de trabalho em disco e no Azure, primeiro será necessário adicionar um disco ao pool de armazenamento. Para saber mais sobre a adição de discos, consulte [Configurar os pools de armazenamento e o armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

Depois que os pools de armazenamento estiverem configurados, você poderá fazer backup das cargas de trabalho de aplicativo no disco e no Azure.

## Solucionar problemas

Se o servidor do Microsoft Azure Backup falhar com erros durante a fase de instalação (ou no backup ou na restauração), consulte o [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para saber mais. Você também pode consultar as [Perguntas frequentes relacionadas ao Backup do Azure](backup-azure-backup-faq.md)

## Perguntas frequentes

### Instalação e implantação

P: A compactação NTFS tem suporte nos discos/volumes do servidor de Backup do Microsoft Azure usados para backups em disco? <br>R: O NTFS **não** tem suporte em discos/volumes conectados ao servidor de Backup do Microsoft Azure.

P: Posso mover o servidor de Backup do Microsoft Azure para um novo domínio após a implantação? <br>R: Não, mover o servidor de Backup do Microsoft Azure para um novo domínio após a implantação **não** tem suporte.

P: Quais recursos poderão ser protegidos pelo servidor de Backup do Microsoft Azure caso estejam instalados em um controlador de domínio? <br>R: O servidor de Backup do Microsoft Azure só poderá proteger as fontes de dados locais se for co-instalado no mesmo servidor que um controlador de domínio. Para o servidor de Backup do Microsoft Azure proteger as cargas de trabalho em outros servidores, não o instale no mesmo computador do controlador de domínio.

P: O servidor de Backup do Microsoft Azure pode usar uma instância remota do SQL Server? <br>R: Não, o servidor de Backup do Microsoft Azure só pode usar uma instância local do SQL Server.

### Pool de armazenamento do servidor de Backup do Microsoft Azure

P: Posso fornecer um disco virtual (VHD/HDx) no pool de armazenamento do servidor de Backup do Microsoft Azure? <br>A: Sim.

P: Posso eliminar a duplicação em um pool de armazenamento do servidor de Backup do Microsoft Azure? <br>R: Sim, a eliminação de duplicação está disponível em um pool de armazenamento do servidor de Backup do Microsoft Azure. A experiência do usuário para o servidor de Backup do Microsoft Azure é exatamente como detalhado nesta [postagem de blog do DPM](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx).

P: É possível usar USB ou unidades removíveis em um pool de armazenamento do servidor de Backup do Microsoft Azure? <br>R: Não.

### Conectividade do servidor de Backup do Microsoft Azure ao serviço do Azure

P: Como a conectividade do servidor de Backup do Microsoft Azure para o serviço do Azure e para o estado de assinatura do Azure afetam os backups e a restauração? <br>R: O servidor de Backup do Microsoft Azure requer conectividade com o serviço do Azure para que o produto funcione com êxito. Ao mesmo tempo, a assinatura do Azure deve estar em um estado íntegro.

A tabela a seguir explica a funcionalidade do servidor de Backup do Microsoft Azure em relação aos diferentes estados de conectividade e de assinatura do Azure.

| Estado de conectividade | Assinatura do Azure | Backup no Azure| Backup em disco | Restaurar do Azure | Restaurar do disco |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Conectado | Ativo | Permitido | Permitido | Permitido | Permitido |
| Conectado | Expirado | Parada | Parada | Permitido | Permitido |
| Normal | Provisionamento Cancelado | Parada | Parada | Parado e pontos de recuperação do Azure excluídos | Parada |
| Perda de conectividade > 15 dias | Ativo | Parada | Parada | Permitido | Permitido |
| Perda de conectividade > 15 dias | Expirado | Parada | Parada | Permitido | Permitido |
| Perda de conectividade > 15 dias | Provisionamento Cancelado | Parada | Parada | Parado e pontos de recuperação do Azure excluídos | Parada |

P: Supondo que o estado da assinatura continue a ser *Ativo*, o que acontecerá se a conectividade do servidor de Backup Microsoft Azure for restaurada? <br>R: Assim que a conectividade do servidor de Backup do Microsoft Azure para o serviço do Azure volte a ficar normal e o estado da assinatura esteja *Ativo*, todas as operações do servidor de Backup do Microsoft Azure serão permitidas. Os backups em ambos os discos, bem como no Azure, serão executados normalmente.

P: O que acontecerá se o estado da assinatura for restaurado de um estado *Expirado* para um estado *Ativo*? <br>R: Supondo que a conectividade do servidor de Backup do Microsoft Azure para o serviço do Azure esteja normal, assim que o estado da assinatura do servidor de Backup do Microsoft Azure for restaurado para *Ativo*, todas as operações do servidor de Backup do Microsoft Azure serão permitidas. Os backups em ambos os discos, bem como no Azure, agora serão executados normalmente.

P: O que acontecerá se o estado da assinatura for restaurado de um estado *Desprovisionado* para um estado *Ativo*? <br>R: Supondo que a conectividade do servidor de Backup do Microsoft Azure para o serviço do Azure esteja normal, assim que o estado da assinatura do servidor de Backup do Microsoft Azure for restaurado para *Ativo*, todas as operações do servidor de Backup do Microsoft Azure serão permitidas. No entanto, os pontos de recuperação do Azure serão excluídos e não poderão ser restaurados. Os pontos de recuperação de disco poderão ser restaurados se os backups em disco estejam em um período de retenção válido.

P: Quais exceções eu preciso criar para permitir a conectividade do servidor de Backup do Microsoft Azure para um serviço público do Azure? <br>R: Você precisa permitir os seguintes endereços de domínio no perfil do firewall - www.msftncsi.com, *.Microsoft.com, *.WindowsAzure.com, *.microsoftonline.com, *.windows.net.

P: Como posso validar se o servidor de Backup do Microsoft Azure tem conectividade para o serviço do Azure? <br>R: Execute o seguinte cmdlet do PowerShell no console do servidor de Backup do Microsoft Azure:

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

P: Por que o estado da minha assinatura não é *Ativo*? Como eu o altero para *Ativo*? <br>R: Faça logon no [portal de assinatura](https://account.windowsazure.com/Subscriptions) e gerencie sua assinatura.

### Cobrança

P: Qual é o modelo de cobrança do servidor de Backup do Microsoft Azure? <br>R: Os usuários serão cobrados por meio de um modelo de instância protegida. Para saber mais, consulte as perguntas frequentes na página [preços](http://azure.microsoft.com/pricing/details/backup/).

P: Qual será o modelo de cobrança se eu só proteger os dados em disco? <br>R: O modelo de cobrança é igual ao modelo de instância protegida. Como esses dados estão protegidos no armazenamento local, não haverá cobrança de armazenamento do Azure para os backups baseados em disco. Nesse caso, a taxa de instância protegidos seria cobrada do cliente. Para saber mais sobre o que define uma instância e quanto será cobrado por instância, consulte as perguntas frequentes na página [preços](http://azure.microsoft.com/pricing/details/backup/).

P: Qual é a cobrança por instância protegida? <br>R: Consulte a página de [preços](http://azure.microsoft.com/pricing/details/backup/).

P: Onde posso encontrar exemplos que realcem as cargas de trabalho de aplicativo diferentes e a taxa de instância protegida para elas? <br>R: Consulte a “seção Exemplos" na página [preços](http://azure.microsoft.com/pricing/details/backup/).

P: Como o servidor de Backup do Microsoft Azure cobraria uma fonte de dados protegida em disco e na nuvem? <br>R: Independentemente do backup ser feito em disco ou na nuvem, o servidor de Backup do Microsoft Azure cobra com base em instâncias protegidas. O tamanho da instância protegida é calculado com base no tamanho da fonte de dados de front-end. Para dados de backup no armazenamento do Azure, os custos de armazenamento do Azure também se aplicam.

## Próximas etapas

Você pode usar estes artigos para obter um entendimento mais profundo sobre a proteção da carga de trabalho usando o servidor de Backup do Microsoft Azure.

- [Backup do SQL Server](backup-azure-backup-sql.md)
- [Backup do servidor do SharePoint](backup-azure-backup-sharepoint.md)
- [Backup do servidor alternativo](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO2-->