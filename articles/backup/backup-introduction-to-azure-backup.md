<properties
	pageTitle="O que é o Backup do Azure? | Microsoft Azure"
	description="Com os serviços do Backup e recuperação do Azure, você pode fazer backup e restaurar dados e aplicativos de Windows Servers, máquinas de cliente do Windows, servidores SCDPM ou máquinas virtuais do Azure."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"
	keywords="backup e restauração; serviços de recuperação"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="aashishr"; "trinadhk"; "jimpark"/>

# O que é o Backup do Azure?
O Backup do Azure é um serviço multilocatário do Azure que permite fazer backup e restaurar seus dados localmente ou no Azure. Ele substitui a solução de backup local ou externa existente por uma solução de backup na nuvem confiável, segura e econômica. Ele também fornece a flexibilidade de proteger seus ativos que estão em execução na nuvem. O Backup do Azure foi desenvolvido em uma infraestrutura de excelência internacional que é escalonável, durável e altamente disponível. Com essa solução, você pode fazer backup de dados e aplicativos de seus servidores do SCDPM (System Center Data Protection Manager), servidores Windows, computadores cliente Windows ou máquinas virtuais de IaaS do Azure. O Backup do Azure e o SCDPM são as tecnologias fundamentais que compõem a solução de backup integrada na nuvem da Microsoft.

> [AZURE.VIDEO what-is-azure-backup]

## Ponto de design em nuvem
As soluções tradicionais de backup foram desenvolvidas para tratar a nuvem como um ponto de extremidade semelhante a disco ou fita. Embora essa abordagem seja simples, fácil de implantar e forneça uma experiência consistente, ela tem usos limitados e não aproveita a plataforma subjacente. Isso se traduz em uma solução ineficiente e cara para os clientes finais. Tratando o Azure como "apenas um ponto de extremidade de armazenamento", as soluções de backup são incapazes de aproveitar os avanços e a eficiência da plataforma de nuvem pública. O Backup do Azure, por outro lado, oferece um serviço real que utiliza a nuvem para oferecer uma solução de backup eficiente e econômica. Ele se integra à sua solução de backup local (SCDPM) para fornecer uma solução híbrida completa.

As vantagens dessa abordagem são:

- Arquitetura de armazenamento eficiente em nuvem que fornece armazenamento de dados de baixo custo e resiliente
- Dimensionamento automático não intrusivo do serviço com garantias de alta disponibilidade
- Modo consistente de backup local, híbrido e implantações de IaaS

Os principais recursos dessa solução são:

1. **Serviço confiável**: ao adotar o Backup do Azure, você obtém um serviço de backup altamente disponível. O serviço é multilocatário e você não precisa se preocupar com o gerenciamento da computação subjacente ou do armazenamento.

2. **Armazenamento confiável**: o Backup do Azure foi criado em um armazenamento em nuvem confiável com suporte de altos SLAs. Você não precisa se preocupar com as despesas de capital ou de operação na manutenção do armazenamento. Além disso, você tem a opção de backup em armazenamento LRS (armazenamento com redundância local) ou GRS (armazenamento com replicação geográfica). Enquanto o LRS permite três cópias dos dados no mesmo local que protege contra falhas de hardware local, o GRS fornece três cópias adicionais (um total de seis cópias) em um datacenter emparelhado. Isso garante que os dados de backup sejam altamente disponíveis. Mesmo se houver um desastre localmente no Azure, os dados de backup estarão seguros conosco.

3. **Seguro**: os dados de backup do Azure são criptografados no código-fonte, durante a transmissão e armazenados criptografados no Azure. A chave de criptografia é armazenada na fonte e nunca é transmitida ou armazenada no Azure. A chave de criptografia é necessária para restaurar quaisquer dados e somente o cliente tem acesso completo aos dados no serviço.

4. **Proteção externa**: em vez de pagar por soluções de backup em fita externa, os clientes podem fazer backup no Azure, o que fornece uma solução atraente com semântica semelhante à da fita por um custo muito baixo.

5. **Simplicidade**: o Backup do Azure fornece uma interface conhecida que pode ser dimensionada para proteger uma implantação de qualquer tamanho. À medida que o serviço evolui, recursos como o gerenciamento centralizado permitirão que você gerencie sua infraestrutura de backup em um único local.

6. **Econômico**: os preços do Backup do Azure incluem uma taxa de gerenciamento de backup por instância e o custo do armazenamento (preço do blob de blocos) consumido no Azure. Ao contrário de outra oferta de backup em nuvem, o Backup do Azure não cobra seus clientes por qualquer operação de restauração. Além disso, os clientes não são cobrados por nenhuma transferência de dados de saída durante uma operação de restauração.

7. **Backup na Nuvem**: o Backup do Azure fornece backup consistente com o aplicativo e baseado em VSS das máquinas virtuais de IaaS do Azure em execução, sem que haja a necessidade de desligar a máquina virtual. Ele também pode fazer backup de máquinas virtuais Linux no Azure com consistência de sistema de arquivos.


## Cenários de implantação
| Componente | Pode ser implantado no Azure? | Pode ser implantado localmente? | Armazenamento de destino com suporte|
| --- | --- | --- | --- |
| Agente de Backup do Azure | **Sim** <br><br>O agente do Backup do Azure pode ser implantando em qualquer VM do Windows Server em execução no Azure. | **Sim** <br><br>O agente do Backup do Azure pode ser implantando em qualquer VM do Windows Server ou computador físico. | Criar cofre de backup do Azure |
| System Center Data Protection Manager (SCDPM) | **Sim** <br><br>Saiba mais sobre [como proteger cargas de trabalho no Azure usando o SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx). | **Sim** <br><br>Saiba mais sobre [como proteger cargas de trabalho e VMs no seu datacenter](https://technet.microsoft.com/pt-BR/library/hh758173.aspx). | Disco anexado localmente,<br>cofre de Backup do Azure,<br>Fita (somente localmente) |
| Backup do Azure (extensão VM) | **Sim** <br><br>Especializado no [backup de máquinas virtuais de IaaS do Azure](backup-azure-vms-introduction.md). | **Não** <br><br>Use o SCDPM para fazer backup das máquinas virtuais em seu datacenter. | Criar cofre de backup do Azure |


## Aplicativos e cargas de trabalho

| Carga de trabalho | Máquina de origem | Solução de Backup do Azure |
| --- | --- |---|
| Arquivos e pastas | Windows Server | [Agente de backup do Azure](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Arquivos e pastas | Windows Client | [Agente de backup do Azure](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Máquina virtual do Hyper-V (Windows) | Windows Server | System Center DPM |
| Máquina virtual do Hyper-V (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | [System Center DPM](backup-azure-backup-sql.md) |
| Microsoft SharePoint | Windows Server | [System Center DPM](backup-azure-backup-sharepoint.md) |
| Microsoft Exchange | Windows Server | System Center DPM |
| VMs de IaaS do Azure (Windows)| - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) | | VMs de IaaS do Azure (Linux) | - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) |


## Próximas etapas
- [Teste o Backup do Azure](backup-try-azure-backup-in-10-mins.md)
- As perguntas frequentes sobre o serviço de Backup do Azure estão listadas [aqui](backup-azure-backup-faq.md).
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=Nov15_HO4-->