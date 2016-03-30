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
	ms.date="03/18/2016" 
	ms.author="raynew"/>

# Quais cargas de trabalho posso proteger com o Azure Site Recovery?

O Azure Site Recovery permite que os clientes implantem soluções de disponibilidade com reconhecimento de aplicativo que colaboram com a estratégia de continuidade de negócios e recuperação de desastre (BCDR) da sua organização. Quer seja com aplicativos baseados no Windows Server ou em Linux, aplicativos corporativos da Microsoft ou ofertas de outros fornecedores, você poderá usar o Azure Site Recovery para habilitar a recuperação de desastre, os ambientes de desenvolvimento e teste sob demanda ou as migrações na nuvem.

O Site Recovery se integra aos aplicativos da Microsoft, incluindo o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory. A Microsoft também trabalha em conjunto com os principais fornecedores, incluindo a Oracle, a SAP, a IBM e a Red Hat, a fim de assegurar o bom funcionamento de seus aplicativos e serviços nas plataformas da Microsoft, como o Azure e o Hyper-V. Você pode personalizar sua solução de recuperação de desastre para cada aplicativo específico.


##Principais recursos
Os recursos do Azure Site Recovery que contribuem para a sua estratégia de proteção e de recuperação no nível de aplicativo incluem:

- Replicação quase síncrona com RPOs de até 30 segundos e que atendem às necessidades dos aplicativos mais importantes.
- Instantâneos consistentes de aplicativos para aplicativos simples ou de N camadas.
- Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação em nível de aplicativo, incluindo a replicação do Active Directory, o SQL AlwaysOn, os DAGs (Grupos de Disponibilidade de Banco de Dados do Exchange) e o Oracle Data Guard.
- Os planos de recuperação flexíveis permitem a recuperação de uma pilha inteira de aplicativos com um único clique e incluem scripts externos ou até mesmo ações manuais. 
- O gerenciamento avançado de rede na Recuperação de Site e no Azure simplifica os requisitos de rede para um aplicativo, incluindo a reserva de endereços IP, a configuração de balanceadores de carga ou a integração do Gerenciador de Tráfego do Azure para switchovers de rede de baixo RTO.
-  biblioteca de automação avançada que fornece scripts específicos do aplicativo, prontos para produção, que pode ser baixada e integrada à Recuperação de Site.



##Resumo de carga de trabalho

As tecnologias de replicação do Site Recovery são compatíveis com qualquer aplicativo em execução em uma máquina virtual. Além disso, conduzimos testes adicionais em parceria com as equipes de produto do aplicativo para darmos mais suporte a cada aplicativo.

**Carga de trabalho** | <p>**Replicar VMs do Hyper-V**</p> <p>**(para um site secundário)**</p> | <p>**Replicar VMs do Hyper-V**</p> <p>**(para o Azure)**</p> | <p>**Replicar VMs VMware**</p> <p>**(para um site secundário)**</p> | <p>**Replicar VMs VMware**</p><p>**(para o Azure)****</p>
---|---|---|---|---
Active Directory, DNS | S | S | S | S 
Aplicativos Web (IIS, SQL) | S | S | S | S
SCOM | S | S | S | S
Sharepoint | S | S | S | S
<p>SAP</p><p>Replicar site SAP para o Azure para não cluster</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Exchange (não DAG) | S | Em breve | S | S
Área de Trabalho Remota/VDI | S | S | S | N/D 
<p>Linux</p> <p>(sistema operacional e aplicativos)</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) 
Dynamics AX | S | S | S | S
Dynamics CRM | S | Em breve | S | Em breve
Oracle | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Servidor de arquivos do Windows | S | S | S | S

##Active Directory e DNS

Todos os aplicativos corporativos, como o SharePoint, o Dynamics AX e o SAP, dependem da infraestrutura do Active Directory e de DNS. Como parte de sua solução BCDR, você precisará proteger e recuperar esses componentes de infraestrutura antes de recuperar suas cargas de trabalho e seus aplicativos.

Com o Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para o Active Directory e o DNS. Por exemplo, se você estiver usando o Active Directory para vários aplicativos, como o SharePoint e o SAP em seu site primário, e quiser fazer failover de todo o site, o failover do Active Directory deverá ser feito primeiro usando um plano de recuperação e depois deverá ser feito o failover dos aplicativos que dependem do Active Directory usando planos de recuperação específicos dos aplicativos.

[Saiba mais](http://aka.ms/asr-ad)

##SQL Server

O SQL Server fornece uma base para os serviços de dados para vários aplicativos de negócios em um datacenter local. As tecnologias de HA/DR do Site Recovery e do SQL Server são complementares e podem ser usadas em conjunto para fornecer proteção total para aplicativos corporativos de várias camadas. O Site Recovery oferece os seguintes benefícios para ambientes do SQL Server:

- Proteja e replique com facilidade os servidores SQL autônomos ou em cluster no Azure ou em um site secundário. Fornece uma solução de recuperação de desastre simples e econômica para várias versões e edições do SQL Server.
- Integração com Grupos de Disponibilidade SQL AlwaysOn, gerenciamento de processos de failover e de failback com planos de recuperação no Azure Site Recovery.
- Planos de recuperação completos para toda a pilha de aplicativos, incluindo os bancos de dados SQL.
- Expansão do SQL Server para cargas de pico usando o Azure Site Recovery ao "dispará-las" em tamanhos maiores de máquina virtual IaaS no Azure.
- Testes do SQL Server no Azure ou em um site secundário usando o Azure Site Recovery. Os failovers de teste podem ser usados para verificações de conformidade de dados de análise sem afetar o ambiente de produção.

[Saiba mais](http://aka.ms/asr-sql)

##SharePoint

O Azure Site Recovery ajuda a proteger a implantação do SharePoint. Com o Site Recovery, você pode:

- Eliminar a necessidade, e o custo associado à infraestrutura, de um farm autônomo para recuperação de desastres. Com o Site Recovery, você pode habilitar a proteção de todo o farm (camadas da Web, de aplicativo e de banco de dados) no Azure ou em um site secundário.
- Simplificar a implantação e a capacidade de gerenciamento de aplicativos. As atualizações implantadas no site primário são automaticamente replicadas e estarão disponíveis quando o farm for recuperado no site secundário. Isso elimina a complexidade de gerenciamento que envolve a manutenção de um farm autônomo atualizado, reduzindo o custo.
- Simplifique o desenvolvimento de aplicativos do SharePoint e de teste por meio da criação de um ambiente de réplica sob demanda cópia de produção para teste e depuração.
- Simplifique sua transição para a nuvem usando o Site Recovery para migrar implantações do SharePoint para o Azure.

[Saiba mais](http://aka.ms/asr-sharepoint)


##Dynamics AX

O Azure Site Recovery ajuda a proteger a sua solução de ERP do Dynamics AX:

- Habilite a proteção do ambiente inteiro do Dynamics AX (camadas da Web e do AOS, camadas de banco de dados, SharePoint) no Azure ou em um site secundário usando o Site Recovery.
- Simplifique sua migração para a nuvem usando o Site Recovery para migrar implantações do Dynamics AX para o Azure.
- Simplifique o desenvolvimento de aplicativos Dynamics AX e de teste por meio da criação de uma cópia de produção sob demanda para teste e depuração.

[Leia mais](http://aka.ms/asr-dynamics)

##RDS 
Os Serviços de Área de Trabalho Remota habilitam uma infraestrutura de área de trabalho virtual (VDI), áreas de trabalho baseadas em sessão e aplicativos, permitindo que os usuários trabalhem de qualquer lugar. Com o Site Recovery, você pode habilitar a proteção de áreas de trabalho virtuais em pool, gerenciadas ou não, em um site secundário, e aplicativos e sessões remotas em um site secundário ou no Azure.

[Saiba mais](http://aka.ms/asr-rds)


##Exchange

O Microsoft Exchange inclui suporte interno para alta disponibilidade e recuperação de desastre. Os Exchange DAGs e o Azure Site Recovery podem trabalhar juntos.

- Os Exchange DAGs são a solução recomendada para recuperação de desastre do Exchange em uma empresa. Os planos de recuperação do Site Recovery podem incluir DAGs para orquestrar failovers de DAG entre sites.
- Para implantações pequenas, como um único servidor ou servidores fora de clusters, o Site Recovery pode proteger e aplicar o failover no Azure ou em um site secundário.

[ganhar mais](http://aka.ms/asr-exchange)

##SAP

Use o Site Recovery para proteger sua implantação do SAP:

- Habilite a proteção de toda a implantação SAP com camadas diferentes no Azure ou em um site secundário.
- Simplifique a migração de nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
- Simplifique o desenvolvimento e o teste do SAP por meio da criação de uma cópia de produção sob demanda para teste e depuração de aplicativos.

[Saiba mais](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_0323_2016-->