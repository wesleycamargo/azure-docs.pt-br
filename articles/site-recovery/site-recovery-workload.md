<properties
	pageTitle="Quais cargas de trabalho posso proteger com o Azure Site Recovery?" 
	description="O Azure Site Recovery protege suas cargas de trabalho e seus aplicativos ao coordenar a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="03/27/2016" 
	ms.author="raynew"/>

# Quais cargas de trabalho posso proteger com o Azure Site Recovery?


Este artigo descreve quais aplicativos e cargas de trabalho você pode proteger com o Azure Site Recovery.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que determine como os aplicativos, as cargas de trabalho e os dados permanecerão disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível.

O Site Recovery é um serviço do Azure que colabora com sua estratégia BCDR permitindo que você implante soluções de resiliência com reconhecimento de aplicativo coordenando a replicação dos servidores físicos e máquinas virtuais locais para a nuvem (Azure) ou para um site secundário. Se seus aplicativos são baseados no Windows ou no Linux, sendo executado em servidores físicos, VMware ou VMs do Hyper-V, você pode usar o Site Recovery para coordenar a replicação, realizar testes de recuperação de desastres e executar failovers e failback.


O Site Recovery se integra aos aplicativos da Microsoft, incluindo o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory. A Microsoft também trabalha em conjunto com os principais fornecedores, inclusive a Oracle, SAP, IBM e Red Hat, para garantir que seus aplicativos e serviços funcionem bem nas plataformas Microsoft, incluindo o Azure. Você pode personalizar uma solução por aplicativo.

## Por que usar o Site Recovery para a proteção do aplicativo?

O Site Recovery contribui para a recuperação e a proteção no nível do aplicativo da seguinte maneira:

- Replicação quase síncrona com RPOs de até 30 segundos para atender as necessidades dos aplicativos mais importantes.
- Instantâneos consistentes de aplicativos para os aplicativos simples ou multicamadas.
- Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação em nível de aplicativo, incluindo a replicação do Active Directory, o SQL AlwaysOn, os DAGs (Grupos de Disponibilidade de Banco de Dados do Exchange) e o Oracle Data Guard.
- Os planos de recuperação flexíveis permitem a recuperação de uma pilha inteira de aplicativos com um único clique e incluem scripts externos e ações manuais no plano. 
- O gerenciamento avançado de rede no Site Recovery e no Azure simplifica os requisitos de rede do aplicativo, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração com o Gerenciador de Tráfego do Azure para os switchovers de rede de baixo RTO.
-  Uma biblioteca de automação avançada que fornece scripts específicos do aplicativo, prontos para a produção, que pode ser baixada e integrada nos planos de recuperação.



##Resumo de carga de trabalho

O Site Recovery pode replicar qualquer aplicativo em execução em uma máquina virtual com suporte. Além disso, uma parceria com as equipes de produto para executar testes adicionais específicos do aplicativo.

**Carga de trabalho** | **Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure**
---|---|---|---|---
Active Directory, DNS | S | S | S | S 
Aplicativos Web (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
Sharepoint | S | S | S | S
SAP<br/><br/>Replicar site SAP para o Azure de não cluster | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Exchange (não DAG) | S | Em breve | S | S
Área de Trabalho Remota/VDI | S | S | S | N/D 
Linux (sistema operacional e aplicativos) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) 
Dynamics AX | S | S | S | S
Dynamics CRM | S | Em breve | S | Em breve
Oracle | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Servidor de arquivos do Windows | S | S | S | S


## Replicar o Active Directory e o DNS

As infraestruturas do DNS e do Active Directory são essenciais para a maioria dos aplicativos corporativos. Durante a recuperação de desastre, você precisará proteger e recuperar esses componentes de infraestrutura antes de recuperar suas cargas de trabalho e aplicativos.

Você usar o Site Recovery para criar um plano totalmente automatizado de recuperação de desastre para o Active Directory e o DNS. Por exemplo, se você quiser fazer o failover do SharePoint e do SAP de um site primário para um secundário, poderá configurar um plano de recuperação que executa o failover do Active Directory primeiro, em seguida, um plano de recuperação adicional específico do aplicativo para o failover em outros aplicativos que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger o Active Directory e o DNS.

## Proteger o SQL Server

O SQL Server fornece uma base de serviços de dados para muitos aplicativos de negócios em um datacenter local. O Site Recovery pode ser usado junto com as tecnologias HA/DR do SQL Server para proteger os aplicativos corporativos de vários níveis que usam o SQL Server. A Recuperação de Site fornece:

- Uma solução de recuperação de desastre simples e econômica para o SQL Server. Replique diversas versões e edições dos clusters e servidores autônomos do SQL Server para o Azure ou um site secundário.  
- Integração com Grupos de Disponibilidade SQL AlwaysOn para gerenciar o failover e o failback com os planos de recuperação do Azure Site Recovery.
- Planos de recuperação completos para todas as camadas em um aplicativo, incluindo os bancos de dados do SQL Server.
- Expansão do SQL Server para as cargas de pico com o Site Recovery "disparando-as" em máquinas virtuais IaaS maiores no Azure.
- Teste fácil de recuperação de desastres do SQL Server. Você pode executar failovers de teste para analisar os dados e executar verificações de conformidade sem afetar seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger o SQL Server.

##Proteger o SharePoint

O Azure Site Recovery ajuda a proteger as implantações do SharePoint assim:

- Elimina a necessidade e os custos associados à infraestrutura de um farm autônomo para a recuperação de desastres. Usa o Site Recovery para replicar um farm inteiro (camadas da Web, do aplicativo e do banco de dados) para o Azure ou um site secundário.
- Simplifica a implantação e o gerenciamento de aplicativos. As atualizações implantadas no site primário são automaticamente replicadas e disponibilizadas após o failover e a recuperação de um farm em um site secundário. Também reduz a complexidade do gerenciamento e custos associados à manutenção de um farm autônomo atualizado.
- Simplifica o desenvolvimento de aplicativos do SharePoint e teste criando um ambiente de réplica sob demanda com cópia de produção para o teste e a depuração.
- Simplifica a transição para a nuvem usando o Site Recovery para migrar as implantações do SharePoint para o Azure.

[Saiba mais](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sobre como proteger o SharePoint.


## Proteger o Dynamics AX

O Azure Site Recovery ajuda a proteger sua solução ERP do Dynamics AX:

- Coordenando a replicação de todo o ambiente Dynamics AX (camadas da Web e AOS, camadas do banco de dados, SharePoint) para o Azure ou para um site secundário.
- Simplificando a migração das implantações do Dynamics AX para a nuvem (Azure).
- Simplificando o desenvolvimento de aplicativos Dynamics AX e teste criando uma cópia de produção sob demanda para o teste e a depuração.

[Saiba mais](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sobre como proteger o Dynamic AX.

## Proteger o RDS 

O RDS (Serviços da Área de Trabalho Remota) habilita a infraestrutura da área de trabalho virtual (VDI), áreas de trabalho baseadas em sessão e aplicativos, permitindo que os usuários trabalhem de qualquer lugar. Com o Azure Site Recovery, você pode:

- Replicar as áreas de trabalho virtuais em pool gerenciadas ou não para um site secundário e aplicativos e sessões remotos para um site secundário ou Azure.
- Aqui está o que você pode replicar:

**RDS** | **Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure**
---|---|---|---|---|---|---
**Área de trabalho virtual em pool (não gerenciada)** | Sim | Não | Sim | Não | Sim | Não
**Área de trabalho virtual em pool (gerenciada e sem UPD)** | Sim | Não | Sim | Não | Sim | Não
**Aplicativos remotos e sessões da área de trabalho (sem UDP)** | Sim | Sim | Sim | Sim | Sim | Sim


[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger o RDS.


## Proteger o Exchange

O Site Recovery ajuda a proteger o Exchange da seguinte maneira:

- Para as implantações pequenas do Exchange, como um único servidor ou servidores sem cluster, o Site Recovery pode replicar e fazer failover no Azure ou em um site secundário.
- Para as implantações maiores, o Site Recovery integra-se ao Exchange DAGS.
- Os Exchange DAGs são a solução recomendada para recuperação de desastre do Exchange em uma empresa. Os planos de recuperação do Site Recovery podem incluir DAGs para coordenar o failover do DAG nos sites.


[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger o Exchange.

## Proteger o SAP

Use o Site Recovery para proteger sua implantação do SAP como a seguir:

- Habilite a proteção de toda a implantação do SAP replicando camadas diferentes de implantação para o Azure ou um site secundário.
- Simplifique a migração de nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
- Simplifique o desenvolvimento e o teste do SAP criando uma cópia de produção sob demanda para o teste e a depuração dos aplicativos.

[Saiba mais](http://aka.ms/asr-sap) sobre como proteger o SAP.

## Próximas etapas

[Preparar](site-recovery-best-practices.md) para implantação do Site Recovery

<!---HONumber=AcomDC_0330_2016-->