<properties
	pageTitle="Introdução ao Backup do Azure"
	description="Este artigo fornece uma visão geral do serviço de Backup do Azure que permite aos clientes fazer backup de dados no Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Introdução ao Backup do Azure
Este artigo fornece uma boa visão geral da solução integrada de backup em nuvem da Microsoft que permite aos clientes fazer backup de seus dados locais no Azure.

## O que é o Backup do Azure?
O Backup do Azure é um serviço multilocatário do Azure que permite fazer backup dos seus dados locais no Azure. Ele substitui a solução de backup local ou externa existente por uma oferta em nuvem confiável, segura e econômica. O Backup do Azure foi desenvolvido em uma infraestrutura de excelência internacional que é escalonável, durável e altamente disponível. Com essa solução, você pode fazer backup de dados e aplicativos de seus servidores do System Center Data Protection Manager (SCDPM), servidores Windows ou máquinas cliente Windows. O Backup do Azure e o SCDPM são as tecnologias fundamentais que compõem a solução de backup integrada na nuvem da Microsoft.

## Ponto de design em nuvem
As soluções tradicionais de backup foram desenvolvidas para tratar a nuvem como um ponto de extremidade semelhante a disco ou fita. Embora essa abordagem seja simples, fácil de implantar e forneça uma experiência consistente, ela tem usos limitados e não aproveita a plataforma subjacente. Isso se traduz em uma solução ineficiente e cara para os clientes finais. Tratando o Azure como "apenas um ponto de extremidade de armazenamento", as soluções de backup são incapazes de aproveitar os avanços e a eficiência da plataforma de nuvem pública. O Backup do Azure, por outro lado, oferece um serviço real que utiliza construções de nuvem para oferecer uma solução eficiente e econômica de backup. Ele se integra à sua solução de backup local (SCDPM) para fornecer uma solução híbrida completa.

As vantagens dessa abordagem são:

+ Arquitetura de armazenamento eficiente em nuvem que fornece armazenamento de dados de baixo custo e resiliente

+ Dimensionamento automático não intrusivo do serviço com garantias de alta disponibilidade

+ Modo consistente de backup local, híbrido e implantações de IaaS

Os principais recursos dessa solução são:

1. **Serviço confiável**: ao adotar o Backup do Azure, você obtém um serviço de backup altamente disponível. O serviço é multilocatário e você não precisa se preocupar com o gerenciamento da computação subjacente ou do armazenamento.

2. **Armazenamento confiável**: o Backup do Azure foi criado em um armazenamento em nuvem confiável com suporte de altos SLAs. Você não precisa se preocupar com as despesas de capital ou de operação na manutenção do armazenamento. Além disso, você tem a opção de backup em armazenamento LRS (armazenamento com redundância local) ou GRS (armazenamento com replicação geográfica). Enquanto o LRS permite três cópias dos dados no mesmo local que protege contra falhas de hardware local, o GRS fornece três cópias adicionais (um total de seis cópias) em um datacenter emparelhado. Isso garante que os dados de backup sejam altamente disponíveis. Mesmo se houver um desastre localmente no Azure, os dados de backup estarão seguros conosco.

3. **Seguro**: os dados de backup do Azure são criptografados no código-fonte, durante a transmissão e armazenados criptografados no Azure. A chave de criptografia é armazenada na fonte e nunca é transmitida ou armazenada no Azure. A chave de criptografia é necessária para restaurar quaisquer dados e somente o cliente tem acesso completo aos dados no serviço.

4. **Proteção externa**: em vez de pagar por soluções de backup em fita externa, os clientes podem fazer backup no Azure, o que fornece uma solução atraente com semântica semelhante à da fita por um custo muito baixo.

5. **Simplicidade**: o Backup do Azure fornece uma interface conhecida que pode ser dimensionada para proteger uma implantação de qualquer tamanho. À medida que o serviço evolui, recursos como o gerenciamento centralizado permitirão que você gerencie sua infraestrutura de backup em um único local.

6. **Econômico**: os preços do Backup do Azure incluem uma taxa de gerenciamento de backup por instância e o custo do armazenamento (preço do blob de blocos) consumido no Azure. Ao contrário de outra oferta de backup em nuvem, o Backup do Azure não cobra seus clientes por qualquer operação de restauração. Além disso, os clientes não são cobrados por nenhuma transferência de dados de saída durante uma operação de restauração.


## Aplicativos e cargas de trabalho que podem ter backup no Azure
Combinado com o SCDPM, o Backup do Azure pode fazer backup de:

+ Arquivos e pastas de computadores servidores e computadores cliente corporativos

+ Backups de máquina virtual no nível do host das VMs do Microsoft Hyper-V

+ Microsoft SQL Server

+ Microsoft SharePoint

+ Microsoft Exchange

## Próximas etapas
+ As perguntas frequentes sobre o serviço de Backup do Azure estão listadas [aqui](backup-azure-backup-faq.md).
+ Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).
 

<!---HONumber=July15_HO3-->