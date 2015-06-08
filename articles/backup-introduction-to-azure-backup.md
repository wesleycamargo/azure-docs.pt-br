<properties
	pageTitle="Introdução ao Backup do Azure"
	description="Este artigo fornece uma visão geral do serviço de Backup do Azure que permite que os clientes a dados de backup para o Azure"
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
Este artigo fornece uma visão geral de alto nível da solução da Microsoft nuvem integrado backup que permite aos clientes fazer o backup de seus dados no local para o Azure.

## O que é o Backup do Azure?
O Backup do Azure é um serviço do Azure com inquilinos múltiplas que permite que você faça backup dos dados no local para o Azure. Ele substitui o local existente ou solução de backup externo com uma nuvem competitiva confiável, segura e econômico oferta baseada em. O Backup do Azure baseia-se uma infra-estrutura de classe mundial que é escalonável, durável e altamente disponível. Com essa solução, você pode fazer backup de dados e aplicativos de seus servidores do System Center Data Protection Manager (SCDPM), servidores Windows ou máquinas cliente Windows. O Backup do Azure e SCDPM são as tecnologias fundamentais que compõem a solução de backup integrada de nuvem da Microsoft.

## Ponto de Design de nuvem
Soluções tradicionais de backup foram desenvolvidos para tratar de nuvem como um ponto de extremidade semelhante em disco ou fita. Embora essa abordagem é simples, fácil de implantar e fornece uma experiência consistente, ele tem usos limitados e não tirar total proveito da plataforma subjacente. Isso se traduz em uma solução ineficiente e cara para clientes finais. Tratando o Azure como "apenas como um ponto de extremidade armazenamento", as soluções de backup são capazes de canalizar a riqueza e a potência da plataforma de nuvem pública. Backup do Azure, por outro lado, oferece um serviço real que utiliza construções de nuvem para oferecer uma solução eficiente e econômica de backup. Ele se integra à sua solução de backup no local (SCDPM) para fornecer uma solução híbrida de ponta a ponta.

As vantagens dessa abordagem são:

+ Arquitetura de armazenamento eficiente de nuvem que fornece armazenamento de dados de baixo custo e resilientes

+ Dimensionamento automático não intrusivo, do serviço com garantias de alta disponibilidade

+ Modo consistente de backup no local, híbrida e implantações de IaaS

Os principais recursos dessa solução são:

1. **Um serviço confiável**: ao adotar o Backup do Azure, você obtém um serviço de backup que está altamente disponível. O serviço é com inquilinos múltipla e você não precisa se preocupar sobre como gerenciar a computação subjacente ou armazenamento.

2. **Armazenamento confiável**: Backup do Azure baseia-se ao armazenamento em nuvem confiável que é apoiado por SLAs altos. Você não precisa se preocupar sobre as despesas de capital ou operação na manutenção do armazenamento. Além disso, você tem a opção de backup de LRS (armazenamento com redundância local) ou GRS (replicação geográfica) de armazenamento. Enquanto o LRS permite 3 cópias dos dados no mesmo local em que protege contra falhas de hardware local; GRS fornece 3 cópias adicionais (um total de seis cópias) em um datacenter emparelhado. Isso garante que os dados de backup são altamente disponíveis. Mesmo se houver um desastre de nível de site do Azure, os dados de backup serão seguros conosco.

3. **Secure**: dados de backup do Azure são criptografados no código-fonte, durante a transmissão e armazenado Azure criptografado no. A chave de criptografia é armazenada na fonte e nunca é transmitida ou armazenada no Azure. A chave de criptografia é necessária para restaurar os dados e somente o cliente tem acesso completo aos dados no serviço.

4. **Proteção externa**: em vez de pagar por soluções de backup em fita externo, os clientes podem fazer backup do Azure que fornece uma solução atraente com fita-like semântica com um custo muito baixo.

5. **Simplicidade**: Backup do Azure fornece uma interface familiar que pode ser dimensionada para proteger uma implantação de qualquer tamanho. À medida que o serviço evolui, recursos como central de gerenciamento permitirá que você gerencie sua infra-estrutura de backup em um único local.

6. **Econômico**: preços do Azure Backup incluem uma taxa de gerenciamento de backup por instância e o custo de armazenamento (preço de blob de bloco) consumidos no Azure. Ao contrário de outra oferta de backup baseado em nuvem, Backup do Azure não cobra seus clientes para qualquer operação de restauração. Além disso, os clientes não são cobrados qualquer saída (saída) para transferência de dados de custo durante uma operação de restauração.


## Aplicativos e cargas de trabalho que podem ser feitas no Azure
Combinado com SCDPM Azure Backup pode fazer backup:

+ Arquivos e pastas de cliente enterprise, máquinas de servidor

+ Backups de máquina virtual de nível de host das VMs do Microsoft Hyper-V

+ Microsoft SQL Server

+ Microsoft SharePoint

+ Microsoft Exchange

## Próximas etapas
+ Perguntas freqüentes pergunta sobre o serviço de Backup do Azure está listada [aqui](backup-azure-backup-faq.md).
+ Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=GIT-SubDir-->