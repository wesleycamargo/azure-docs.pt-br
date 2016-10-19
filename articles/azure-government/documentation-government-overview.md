<properties
	pageTitle="Documentação do Azure Governamental | Microsoft Azure"
	description="Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Visão geral documentação do Azure Governamental

##  Introdução à documentação do Azure Governamental

Esse site descreve os recursos dos serviços [Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/) e fornece diretrizes gerais aplicáveis a todos os clientes. Antes de incluir dados regulados especificamente em sua assinatura do Azure Governamental, familiarize-se com os recursos do Azure Governamental e consulte sua equipe de contas se tiver alguma dúvida.

Você deve consultar a [Página de Conformidade da Central de Confiabilidade do Microsoft Azure](http://www.microsoft.com/pt-BR/TrustCenter/Compliance/default.aspx) para obter informações atuais sobre os serviços do Azure Governamental cobertos por referências e regulamentos específicos. Outros serviços da Microsoft também podem estar disponíveis, mas não estão dentro do escopo dos serviços cobertos pelo Azure Governamental e não são abordados por este documento. Os serviços do Azure Governamental também podem permitir que você use vários outros recursos, aplicativos ou serviços fornecidos por terceiros — ou pela Microsoft em termos de uso e políticas de privacidade separados — que não estão incluídos no escopo deste documento. Você é responsável por ler os termos de todas as essas ofertas de "complemento", como ofertas do Marketplace, para garantir que atendam às suas necessidades de conformidade.

O Azure Governamental está disponível às entidades que lidam com dados que estão sujeitos a determinadas normas e requisitos governamentais (como NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS), nas quais o uso do Azure Governamental é necessário para atender às normas. Clientes do Azure Governamental estão sujeitos a validação da elegibilidade.

As entidades que tiverem dúvidas sobre a elegibilidade para o Azure Governamental devem consultar a própria equipe de conta.

##  Princípios para a proteção de dados do cliente no Azure Governamental

O Azure Governamental oferece uma gama de recursos e serviços que você pode usar para compilar soluções de nuvem para atender às suas necessidades de dados regulamentados/controlados. Uma solução de cliente compatível é apenas a implementação eficaz de recursos originais do Azure Governamental, juntamente com uma prática de segurança de dados sólida. Quando você hospeda uma solução no Azure Governamental, a Microsoft lida com muitos desses requisitos no nível de infraestrutura da nuvem.

O diagrama a seguir mostra com detalhes o modelo de defesa do Azure. Por exemplo, a Microsoft fornece a infraestrutura de nuvem básica DDOS, juntamente com recursos de cliente, como dispositivos de segurança para aplicativos específicos ao cliente que precisam de DDOS.

![texto alternativo](./media/azure-government-Defenseindepth.png)

Esta página descreve os princípios fundamentais para proteger seus Serviços e Aplicativos, fornecendo orientação e práticas recomendadas sobre como aplicar esses princípios; em outras palavras, como os clientes devem usar de forma inteligente o Azure Governamental para atender às obrigações e responsabilidades necessárias para uma solução que lida com informações ITAR.

Os princípios abrangentes para a proteção de dados do cliente são:
* Proteção de dados usando a criptografia
* Gerenciamento de segredos
* Isolamento para restringir o acesso aos dados

##  Proteção de dados do cliente usando criptografia

Redução de riscos e atendimento às obrigações regulatórias estão aumentando o foco e a importância da criptografia de dados. Use uma implementação de criptografia eficaz para aprimorar as medições atuais de segurança de rede e de aplicativo — e diminua o risco geral de seu ambiente de nuvem.

### <a name="Overview"></a>Criptografia em repouso
A criptografia de dados em repouso aplica-se à proteção de conteúdo do cliente mantido no armazenamento em disco. Há várias maneiras disso acontecer:

### <a name="Overview"></a>Criptografia do Serviço de Armazenamento

A Criptografia do Serviço de Armazenamento é habilitada no nível da conta de armazenamento, resultando na criptografia automática em blobs de blocos e blobs de páginas quando gravados no Armazenamento do Azure. Quando você lê os dados no Armazenamento do Azure, eles são descriptografados pelo serviço de armazenamento antes de serem retornados. Use isso para proteger seus dados sem precisar modificar ou adicionar o código a algum aplicativo.

### <a name="Overview"></a>Azure Disk Encryption
Use o Azure Disk Encryption para criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual. Integração com o Cofre de Chaves do Azure proporciona controle e ajuda você a gerenciar chaves de criptografia de disco.

### <a name="Overview"></a>Criptografia do cliente
A criptografia do cliente é incorporada no Java e às bibliotecas de cliente de armazenamento .NET que, por sua vez, podem usar as APIs do Cofre de Chaves do Azure, o que a torna muito fácil de implementar. Use o Cofre de Chaves do Azure para obter acesso aos segredos no Cofre de Chaves do Azure para indivíduos específicos usando o Azure Active Directory.

### <a name="Overview"></a>Criptografia em trânsito

A criptografia básica disponível para conectividade com o Azure Governamental oferece suporte ao protocolo TLS (Transport Level Security) 1.2 e aos certificados X.509. Os algoritmos de criptografia de padrão FIPS 140-2 Nível 1 também são usados para conexões de rede de infraestrutura entre os datacenters do Azure Governamental. As VMs do Windows Server 2012 R2 e do Windows 8-plus e os Compartilhamentos de Arquivos do Azure podem usar SMB 3.0 para criptografia entre a VM e o compartilhamento de arquivos. Use Criptografia do Cliente para criptografar os dados antes que eles sejam transferidos para o armazenamento em um aplicativo cliente e para descriptografar os dados após serem transferidos para fora do armazenamento.

### <a name="Overview"></a>Práticas recomendadas para criptografia

* VMs de IaaS: usar o Azure Disk Encryption. Ative a Criptografia do Serviço de Armazenamento para criptografar os arquivos VHD usados para fazer back-up dos discos no Armazenamento do Azure, mas ela só criptografa dados recém-criados. Isso significa que se você criar uma VM e habilitar a Criptografia do Serviço de Armazenamento na conta de armazenamento que mantém o arquivo VHD, somente as alterações serão criptografadas, não o arquivo VHD original.
* Criptografia do Cliente: esse é o método mais seguro para criptografar dados, pois ele os criptografa antes da transferência, além de criptografar os dados em repouso. No entanto, ela exige a adição de código aos aplicativos usando armazenamento, o que talvez não seja conveniente. Nesses casos, é possível usar HTTPs para os dados em trânsito e a Criptografia do Serviço de Armazenamento para criptografar os dados em repouso. A Criptografia do Cliente também invoca mais carga no cliente, e você deve levar isso em conta em seus planos de escalabilidade, especialmente se estiver criptografando e transferindo muitos dados.

Para saber mais sobre as opções de criptografia no Azure, confira o [Guia de Segurança do Armazenamento](/storage-security-guide).

##  Proteção de dados do cliente por meio do gerenciamento de segredos

O gerenciamento de chaves seguro é fundamental para proteger seus dados na nuvem. Os clientes devem se esforçar para simplificar o gerenciamento de chaves e manter o controle das chaves usadas por serviços e aplicativos em nuvem para criptografar dados.

### <a name="Overview"></a>Práticas recomendadas para gerenciamento de segredos

* Use o Cofre de Chaves para minimizar os riscos de exposição dos segredos por meio de arquivos de configuração embutidos, scripts ou no código-fonte. O Azure Disk Encryption criptografa as chaves (como as chaves de criptografia para Criptografia de Disco do Azure) e segredos (como senhas), armazenando-as em HSM (módulos de segurança de hardware) validados por FIPS 140-2 nível 2. Para garantia extra, você pode importar ou gerar chaves nestes HSMs.
* O código de aplicativo e os modelos devem conter somente as referências de URI aos segredos (o que significa que os segredos reais não estão nos repositórios de códigos, de configurações ou de códigos-fonte). Isso impede grandes ataques de phishing em repositórios internos ou externos, como de harvest-bots no GitHub.
* Use controles RBAC fortes no Cofre de Chaves. Se um operador confiável deixar a empresa ou for transferido para um novo grupo dentro da empresa, será necessário impedir que ele possa acessar os segredos.

Para saber mais, confira [Cofre de Chaves para o Azure Governamental](/azure-government/azure-government-tech-keyvault)

##  Isolamento para restringir o acesso aos dados

Isolamento se resume ao uso de limites, segmentação e contêineres para limitar o acesso aos dados somente aos usuários, serviços e aplicativos autorizados. Por exemplo, a separação entre locatários é um mecanismo de segurança essencial para plataformas de nuvem com vários locatários, como o Microsoft Azure. O isolamento lógico ajuda a impedir que um locatário interfira com as operações de qualquer outro locatário.

### <a name="Overview"></a>Isolamento de ambiente
O ambiente do Azure Governamental é uma instância física separada do restante da rede da Microsoft. Isso é obtido com uma série de controles físicos e lógicos que incluem o seguinte: proteção de barreiras físicas usando dispositivos biométricos e câmeras. Uso de credenciais específicas e autenticação multifator por funcionários da Microsoft que precisam de acesso lógico ao ambiente de produção. Toda a infraestrutura de serviço para o Azure Governamental está localizada nos Estados Unidos.

#### <a name="Overview"></a>Isolamento por cliente
O Azure implementa o controle de acesso à rede e a segregação por meio do isolamento de VLAN, ACLs, filtros IP e balanceadores de carga

Os clientes podem isolar ainda mais seus recursos nas assinaturas, grupos de recursos, redes virtuais e sub-redes.

Para saber mais sobre o isolamento no Microsoft Azure, confira a [Seção Isolamento do Guia de Segurança do Azure](/azure-security-getting-started/#isolation).

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0928_2016-->