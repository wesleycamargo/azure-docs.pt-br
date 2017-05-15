---
title: "Segurança de banco de dados NoSQL - Azure DocumentDB | Microsoft Docs"
description: "Saiba como o Azure DocumentDB fornece proteção de banco de dados e segurança para seus dados NoSQL."
keywords: "segurança do banco de dados nosql, segurança de informações, segurança de dados, criptografia de banco de dados, proteção de banco de dados, políticas de segurança, teste de segurança"
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 71668ceedca16647f814866ea9cf068f73e5daf6
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-security"></a>Segurança de banco de dados NoSQL do DocumentDB

Este artigo discute as práticas recomendadas de segurança de banco de dados NoSQL e os principais recursos oferecidos pelo Azure DocumentDB para ajudar você a evitar, detectar e responder a falhas do banco de dados.
 
## <a name="whats-new-in-documentdb-security"></a>Novidades na segurança do DocumentDB

A criptografia em repouso agora está disponível para documentos armazenados no DocumentDB em todas as regiões do Azure, exceto regiões do governo, o Azure na China e o Azure na Alemanha. As regiões restantes serão habilitadas na próxima semana, juntamente com a criptografia em repouso em backups. A criptografia em repouso é aplicada automaticamente para clientes novos e existentes nessas regiões. Não é necessário configurar nada e você obtém a mesma latência, taxa de transferência, disponibilidade e funcionalidade excelentes de antes, com o benefício de saber que seus dados estão seguros e protegidos com a criptografia em repouso.

## <a name="how-do-i-secure-my-nosql-database"></a>Como proteger o meu banco de dados NoSQL? 

A segurança dos dados é uma responsabilidade compartilhada por você, o cliente e seu provedor de banco de dados. Dependendo do provedor de banco de dados que você escolher, o tamanho da responsabilidade pode variar. Se você escolher uma solução local, precisará fornecer tudo, desde a proteção do ponto de extremidade até a segurança física do seu hardware, o que não é uma tarefa fácil. Se você escolher um provedor de banco de dados de nuvem PaaS, como o Azure DocumentDB, sua área de interesse será consideravelmente reduzida. A imagem a seguir, retirada do white paper [Responsabilidades compartilhadas para a computação em nuvem](https://aka.ms/sharedresponsibility) da Microsoft mostra como sua responsabilidade diminui com um provedor de PaaS, como o Azure DocumentDB.

![Responsabilidades do cliente e do provedor de banco de dados](./media/documentdb-nosql-database-security/nosql-database-security-responsibilities.png)

O diagrama acima mostra componentes de segurança de nuvem de alto nível, mas com quais itens você precisa se preocupar especificamente para sua solução de banco de dados NoSQL? E como você pode comparar as soluções? 

Recomendamos a seguinte lista de verificação de requisitos para comparação com os sistemas de banco de dados NoSQL:

- Configurações de firewall e de segurança de rede
- Autenticação de usuário e controles de usuário refinados
- Capacidade de replicar dados globalmente para falhas regionais
- Capacidade de executar failovers de um data center para outro
- Replicação de dados locais em um data center
- Backup de dados automático
- Restauração de dados excluídos de backups
- Proteger e isolar dados confidenciais
- Monitoramento de ataques
- Resposta aos ataques
- Capacidade de limitar os dados geograficamente a fim de aderir a restrições de controle de dados
- Proteção física dos servidores em data centers protegidos

E embora pareça óbvio, as [falhas recentes em grande escala de banco de dados](http://thehackernews.com/2017/01/mongodb-database-security.html) nos lembram da importância simples, mas fundamental, dos requisitos a seguir:
- Servidores com patches que permanecem atualizados
- Criptografia HTTPS por padrão/SSL
- Contas administrativas com senhas fortes

## <a name="how-does-azure-documentdb-secure-my-database"></a>Como o Azure DocumentDB protege meu banco de dados?

Vamos examinar novamente a lista anterior, quantos desses requisitos de segurança o Azure DocumentDB oferece? Todos.

Vamos examinar cada um deles em detalhes.

|Requisito de segurança|Abordagem de segurança do DocumentDB|
|---|---|---|
|Segurança de rede|O uso de um firewall de IP é a primeira camada de proteção de seu banco de dados NoSQL. O DocumentDB dá suporte a controles de acesso com base em IP conduzidos por política para suporte de firewall de entrada. Os controles de acesso com base em IP são semelhantes às regras de firewall usadas pelos sistemas de banco de dados tradicionais, mas eles são ampliados de modo que uma conta de banco de dados do DocumentDB possa ser acessada apenas a partir de um conjunto aprovado de máquinas ou serviços de nuvem. <br><br>O DocumentDB permite que você habilite um endereço IP específico (168.61.48.0), um intervalo de IPs (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todas as solicitações provenientes de máquinas fora dessa lista de permissão são bloqueadas pelo DocumentDB. As solicitações de computadores e de serviços de nuvem aprovados devem concluir o processo de autenticação para receber o controle de acesso aos recursos.<br><br>Saiba mais em [Suporte ao firewall do DocumentDB](documentdb-firewall-support.md).|
|Autorização|O DocumentDB usa o código de autenticação de mensagem baseado em hash (HMAC) para autorização. <br><br>Cada solicitação recebe o hash usando a chave de conta secreta, e o hash subsequente codificado em base 64 é enviado com cada chamada ao DocumentDB. Para validar a solicitação, o serviço do DocumentDB usa a chave secreta e as propriedades corretas para gerar um hash e depois compara o valor ao valor da solicitação. Se os dois valores corresponderem, a operação será autorizada e a solicitação processada; caso contrário, haverá uma falha de autorização e a solicitação será rejeitada.<br><br>Use uma [chave mestra](documentdb-secure-access-to-data.md#master-keys) ou um [token de recurso](documentdb-secure-access-to-data.md#resource-tokens) permitindo o acesso refinado a um recurso, como um documento.<br><br>Saiba mais em [Proteger o acesso aos recursos do DocumentDB](documentdb-secure-access-to-data.md).|
|Usuários e permissões|Com a [chave mestra](#master-key) para a conta, você pode criar recursos de usuário e de permissão por banco de dados. Um [token de recurso](#resource-token) está associado uma permissão em um banco de dados e determina se o usuário tem acesso (leitura/gravação, somente leitura ou nenhum acesso) a um recurso de aplicativo no banco de dados. Os recursos do aplicativo incluem coleções, documentos, anexos, procedimentos armazenados, gatilhos e UDFs. Em seguida, o token de recurso é usado durante a autenticação para fornecer ou negar acesso ao recurso.<br><br>Saiba mais em [Proteger o acesso aos recursos do DocumentDB](documentdb-secure-access-to-data.md).|
|Integração do Active Directory (RBAC)| Você também pode fornecer acesso à conta do banco de dados usando o controle de acesso (IAM) no Portal do Azure. O IAM fornece controle de acesso baseado em função e integra-se ao Active Directory. Você pode usar funções internas ou personalizadas para indivíduos e grupos, conforme mostra a imagem a seguir.<br><br>![Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL](./media/documentdb-nosql-database-security/nosql-database-security-identity-access-management-iam-rbac.png)|
|Replicação global|O DocumentDB oferece distribuição global completa, o que permite a você replicar os dados para qualquer um dos data centers do Azure em todo o mundo com o clique de um botão. A replicação global permite que você dimensione globalmente e forneça acesso de baixa latência aos seus dados no mundo inteiro.<br><br>No contexto de segurança, a replicação global garante a proteção dos dados contra falhas regionais.<br><br>Saiba mais em [Distribuir dados globalmente](documentdb-distribute-data-globally.md).|
|Failovers regionais|Se você tiver replicado seus dados em mais de um data center, o DocumentDB substitui automaticamente suas operações caso data center regional fique offline. Crie uma lista de prioridade de regiões de failover usando as regiões nas quais seus dados são replicados. <br><br>Saiba mais em [Failovers regionais no Azure DocumentDB](documentdb-regional-failovers.md).|
|Replicação local|Mesmo em um único data center, o DocumentDB replica automaticamente os dados para proporcionar alta disponibilidade, proporcionando a você a opção de [níveis de consistência](documentdb-consistency-levels.md). Isso garante um  [SLA de disponibilidade de tempo de atividade de 99,99%](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) e vem com uma garantia financeira, algo que nenhum outro serviço de banco de dados NoSQL pode oferecer.|
|Backups online automatizados|Os bancos de dados do DocumentDB passam por backup e são armazenados regularmente em um repositório com redundância geográfica. <br><br>Saiba mais em [Backup e restauração online automáticos com o DocumentDB](documentdb-online-backup-and-restore.md).|
|Restaurar dados excluídos|Os backups online automatizados podem ser usados para recuperar dados que você pode ter excluído acidentalmente até 30 dias após o evento. <br><br>Saiba mais em [Backup e restauração online automáticos com o DocumentDB](documentdb-online-backup-and-restore.md)|
|Proteger e isolar dados confidenciais|Todos os dados nas regiões listadas em [Novidades](#whats-new) agora serão criptografados em repouso.<br><br>As informações de identificação pessoal e outros dados confidenciais podem ser isolados em coleções específicas, e o acesso de leitura/gravação ou somente leitura pode ser limitado a usuários específicos.|
|Monitorar ataques|Com o registro em log de auditoria e os logs de atividade, você pode monitorar as atividades normais e anormais de sua conta. Veja as operações que foram executadas em seus recursos, quem iniciou a operação, quando a operação ocorreu, o status da operação e muito mais.<br><br>![Logs de atividade para o Azure DocumentDB](./media/documentdb-nosql-database-security/nosql-database-security-application-logging.png)|
|Responder a ataques|Depois de contatar o suporte do Azure para relatar um possível ataque, começa um processo de resposta ao incidente com cinco etapas. O objetivo do processo de cinco etapas é restaurar a segurança normal do serviço e as operações o mais rápido possível após a detecção de um problema e o início de uma investigação.<br><br>Saiba mais em [Resposta da segurança do Microsoft Azure na nuvem](https://aka.ms/securityresponsepaper).|
|Isolamento geográfico|O DocumentDB garante o controle dos dados e a conformidade para regiões soberana (por exemplo, Alemanha, China, Gov. dos EUA).|
|Instalações protegidas|Os dados no DocumentDB são armazenados em SSDs em centros data centers protegidos do Azure.<br><br>Saiba mais em [Datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Criptografia HTTPS/SSL/TLS|Todas as interações do cliente com o serviço do DocumentDB são impostas por SSL/TLS 1.2. Além disso, toda replicação dentro do datacenter e entre datacenters é imposta por SSL/TLS 1.2.|
|Criptografia em repouso|Todos os dados armazenados em DocumentDB são criptografado em repouso. Saiba mais em [Criptografia DocumentDB em repouso](.\documentdb-nosql-database-encryption-at-rest.md)|
|Servidores com patches|Por ser um banco de dados NoSQL gerenciado, o DocumentDB elimina a necessidade de gerenciar e corrigir servidores, o que é feito automaticamente para você.|
|Contas administrativas com senhas fortes|É difícil de acreditar que precisamos mencionar esse requisito, mas ao contrário de alguns de nossos concorrentes, é impossível ter uma conta administrativa sem senha no DocumentDB.<br><br> A segurança via autenticação baseada em segredo de SSL e HMAC está incorporada por padrão.|
|Certificações de proteção de dados e de segurança|O DocumentDB tem as certificações [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [EUMC (European Model Clauses)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) e [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) certificações. Outras certificações estão em andamento.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre chaves mestras e tokens de recurso, confira [Proteção do acesso aos dados do DocumentDB](documentdb-secure-access-to-data.md).

Para obter mais detalhes sobre as certificações da Microsoft, confira [Central de Confiabilidade do Azure](https://azure.microsoft.com/en-us/support/trust-center/).

