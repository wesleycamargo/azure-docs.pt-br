<properties
	pageTitle="Diretrizes sobre a carga de trabalho da Recuperação de Site | Microsoft Azure" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# Diretrizes sobre a carga de trabalho da Recuperação de Site

O Azure Site Recovery permite que os clientes implantem soluções de disponibilidade com reconhecimento de aplicativos. Quer seja com aplicativos baseados no Windows Server ou em Linux, aplicativos corporativos da Microsoft ou ofertas de outros fornecedores, você poderá usar o Azure Site Recovery para habilitar a recuperação de desastres, os ambientes de desenvolvimento/teste sob demanda ou as migrações na nuvem.

A Microsoft tem bastante experiência com o desenvolvimento dos melhores aplicativos corporativos, como o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory. A Microsoft também trabalha em conjunto com os principais fornecedores, incluindo a Oracle, a SAP, a IBM e a Red Hat, a fim de assegurar o bom funcionamento de seus aplicativos e serviços nas plataformas da Microsoft, incluindo o Azure e o Hyper-V. Aproveitamos esses pontos fortes exclusivos para obter uma compreensão maior sobre os requisitos de disponibilidade de cada aplicativo, a fim de permitir a implantação das melhores soluções de recuperação de desastre e de disponibilidade, que ainda podem ser personalizadas para cada aplicativo.


##Principais recursos
Os recursos do Azure Site Recovery foram criados tendo em mente a proteção/recuperação no nível do aplicativo:

- Replicação quase síncrona com RPOs de até 30 segundos e que atendem às necessidades dos aplicativos mais importantes.
- Instantâneos consistentes de aplicativos para aplicativos simples ou de N camadas
- Integração com replicação no nível do aplicativo. Aproveite as melhores ofertas no nível do aplicativo, incluindo a replicação do AD, SQL Always On, Grupos de Disponibilidade do Banco de Dados do Exchange e Oracle Data Guard
- Planos de recuperação flexíveis permitem a recuperação de uma pilha inteira de aplicativos com um único clique, incluindo a execução de scripts externos ou até mesmo ações manuais. 
- O gerenciamento avançado de rede em ASR e Azure automatiza todas as configurações de rede específicas ao seu aplicativo: reservar endereços IP, configurar balanceadores de carga ou usar o Gerenciador de Tráfego da Microsoft para alternâncias de rede com baixo RTO.
- A Biblioteca de Automação Avançada, que fornece scripts específicos ao aplicativo e prontos para produção. Baixe-os e integre-os às suas soluções baseadas em ASR.


##Resumo das diretrizes de carga de trabalho

As tecnologias de replicação de ASR são compatíveis com qualquer aplicativo em execução em uma máquina virtual. Conduzimos testes adicionais em parceria com as equipes de produto do aplicativo a fim de dar mais suporte a cada aplicativo.

**Carga de trabalho** | <p>**Replicar máquinas virtuais do Hyper-V**</p> <p>**(para o site secundário)**</p> | <p>**Replicar máquinas virtuais do Hyper-V**</p><p>**(para o Azure)**</p> | <p>**Replicar máquinas virtuais da VMware**</p> <p>**(para o site secundário)**</p> | <p>**Replicar máquinas virtuais da VMware**</p><p>**(para o Azure)****</p>---|---|---|---|--- Active Directory, DNS | Y | Y | Y | Y Aplicativos Web (IIS, SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y <p>SAP</p><p>Replicar site da SAP para o Azure para não cluster</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) Exchange (não DAG) | Y | Em breve | Y | Y Área de Trabalho Remota/VDI | Y | Y | Y | N/D <p>Linux</p> <p>(sistema operacional e aplicativos)</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | Em breve | Y | Em breve Oracle | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) Servidor de Arquivos do Windows | Y | Y | Y | Y

##Active Directory e DNS

Todos os aplicativos corporativos, como o SharePoint, o Dynamics AX e o SAP dependem da infraestrutura de AD e DNS para funcionarem corretamente. Durante a criação de uma solução de recuperação de desastres (DR) para qualquer aplicativo desse tipo, é importante proteger e recuperar o AD antes que os outros componentes do aplicativo sofram uma interrupção.

Com o Azure Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para seu AD. Caso você tenha um AD para vários aplicativos, como o SharePoint e SAP, em seu site primário e você decidir aplicar o failover em todo o site, aplique o failover primeiro no AD usando o plano de recuperação do AD e aplique o failover nos outros aplicativos usando planos de recuperação específicos ao aplicativo.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para AD](http://aka.ms/asr-ad)

##SQL Server
O Microsoft SQL Server é a base para muitos aplicativos corporativos primários, de terceiros e de linha de negócios em nível empresarial executados no datacenter local do cliente. Aplicativos como o SharePoint, o Dynamics e o SAP usam o SQL Server para fornecer serviços de dados. As tecnologias de HA/DR do Azure Site Recovery e do SQL Server são complementares e podem ser usadas para fornecer proteção total para aplicativos corporativos de várias camadas. O Azure Site Recovery oferece os seguintes benefícios para ambientes do SQL Server:

- Proteja com facilidade os servidores SQL autônomos ou em cluster no Azure ou em um site secundário. Estenda uma solução de recuperação de desastres simples e econômica para qualquer versão e edição do SQL Server.
- Realize a integração com os Grupos de Disponibilidade Always On do SQL, uma excelente solução de recuperação de desastres, e gerencie a operação de failover/failback com os Planos de recuperação de ASR.
- Crie planos de recuperação completos para toda a pilha de aplicativos, incluindo bancos de dados SQL.
- Use o Azure Site Recovery para escalar verticalmente o SQL Server durante picos de carga, lançando essa carga em VMs de IaaS maiores no Azure.
- Use o Azure Site Recovery para criar cópias de teste do SQL Server no Azure ou em um site secundário. Use essa cópia para análises e verificações de conformidade dos dados, sem afetar o ambiente de produção.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para SQL](http://aka.ms/asr-sql)

##SharePoint
O SharePoint é um aplicativo popular que permite a colaboração entre as organizações por meio de portais de intranet, gerenciamento de documentos e de arquivos, redes sociais, recursos de pesquisa de sites e empresas. Também é uma plataforma de aplicativos para fácil implantação de fluxos de trabalho e aplicativos personalizados. Com o Azure Site Recovery para SharePoint, você pode:

- Eliminar a necessidade, e o custo associado à infraestrutura, de um farm autônomo para recuperação de desastres. Com o ASR, você pode habilitar a proteção de todo o farm (camadas da Web, de aplicativo e de banco de dados) no Azure ou em um site secundário.
- Simplificar a implantação e a capacidade de gerenciamento de aplicativos. As atualizações implantadas no site primário são automaticamente replicadas e disponibilizadas quando o farm é recuperado no site secundário. Isso elimina a complexidade de gerenciamento que envolve a manutenção de um farm autônomo atualizado, e ainda reduz o TCO.
- Facilitar o desenvolvimento de aplicativos do SharePoint e de teste por meio da criação de uma cópia de produção sob demanda para teste e depuração.
- Simplificar seu caminho até a nuvem usando o ASR para migrar implantações do SharePoint para o Azure.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para Sharepoint](http://aka.ms/asr-sharepoint)


##Dynamics AX
Microsoft Dynamics AX é uma solução líder de planejamento de recursos empresariais (ERP), simples de aprender e de usar, de modo que você possa proporcionar valor com mais rapidez, tirar proveito de oportunidades de negócios e gerar o envolvimento do usuário, além de permitir a inovação em toda a organização.

- Com o ASR, você pode habilitar a proteção de todo o Dynamics Ax (camada da Web e AOS, camadas de banco de dados, SharePoint) no Azure ou em um site secundário.
- Simplifique seu caminho até a nuvem usando o ASR para migrar implantações do Dynamics Ax para o Azure.
- Facilite o desenvolvimento de aplicativos Dynamics e de teste por meio da criação de uma cópia de produção sob demanda para teste e depuração.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para Dynamics AX](http://aka.ms/asr-dynamics)

## RDS 
Os Serviços de Área de Trabalho Remota (RDS) do Windows Server aceleram e ampliam as implantações de área de trabalho e de aplicativos para qualquer dispositivo, melhorando a eficiência do trabalhador remoto, enquanto ajuda a manter a propriedade intelectual crítica segura e a simplificar a conformidade regulatória. Os Serviços de Área de Trabalho Remota habilitam uma infraestrutura de área de trabalho virtual (VDI), áreas de trabalho baseadas em sessão e aplicativos, permitindo que os usuários trabalhem de qualquer lugar.

Com o ASR, você pode habilitar a proteção de áreas de trabalho virtuais em pool, gerenciadas ou não, em um site secundário, e aplicativos e sessões remotas em um site secundário ou no Azure.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para RDS/VDI](http://aka.ms/asr-rds)


## Exchange
Microsoft Exchange é o software preferido das empresas para hospedar seus serviços de mensagens e emails. O Exchange garante o acesso à comunicação no PC, telefone ou navegador, com confiabilidade, capacidade de gerenciamento e proteção de dados inigualável.

O Microsoft Exchange dá suporte nativo a soluções de nível empresarial de alta disponibilidade e de recuperação de desastres. As tecnologias dos Grupos de Disponibilidade de Banco de Dados do Exchange (DAG)e do Azure Site Recovery são complementares.

- Os DAGs do Exchange são a opção de implantação recomendada para possibilitar a melhor recuperação de desastres para implantações do Exchange. Os Planos de Recuperação de ASR podem incluir DAGs para orquestrar failovers dos DAG entre sites.
- Para implantações pequenas, como um único servidor ou servidores fora de clusters, o Azure Site Recovery pode proteger e aplicar o failover em servidores individuais no Azure ou em um site secundário.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para Exchange](http://aka.ms/asr-exchange)

## SAP

O SAP é um software líder de planejamento de recursos empresariais (ERP), usado por muitas organizações em todo o mundo. SAP é normalmente visto como um dos aplicativos mais essenciais nas empresas. A arquitetura e as operações desses aplicativos geralmente são muito complexas, e é muito importante que você atenda aos requisitos de BCDR.

- Com o ASR, você pode habilitar a proteção de toda a implantação SAP com camadas diferentes no Azure ou em um site secundário.
- Simplifique seu caminho até a nuvem usando o ASR para migrar implantações SAP para o Azure.
- Facilite o desenvolvimento de aplicativos SAP e de teste por meio da criação de uma cópia de produção sob demanda para teste e depuração.

Consulte o documento vinculado para obter uma orientação detalhada sobre a [implantação do Azure Site Recovery para SAP NetWeaver](http://aka.ms/asr-sap)

<!---HONumber=Oct15_HO3-->