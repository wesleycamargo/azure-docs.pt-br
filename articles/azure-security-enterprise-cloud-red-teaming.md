<properties
   pageTitle="Uso de Equipes Vermelhas no Microsoft Enterprise Cloud"
   description="Este artigo aborda a estratégia da Microsoft e o uso de Equipes Vermelhas e a execução de testes de penetração de sites ativos na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados da Microsoft. Você verá como a Microsoft simula violações do mundo real, realiza o monitoramento de segurança contínua e pratica a resposta a incidentes de segurança para validar e melhorar a segurança do Microsoft Azure e do Office 365."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="yuridio"/>


#Uso de Equipes Vermelhas no Microsoft Enterprise Cloud

O uso de Equipes Vermelhas está acima e além das referências de conformidade ou outros requisitos do setor e oferece à Microsoft e aos nossos clientes a garantia adicional de que os serviços de nuvem da Microsoft estarão realizando continuamente o monitoramento de segurança, os testes e as atualizações para refletir as ameças em constante alteração enfrentadas pelos clientes e pela Microsoft.

##Introdução

As organizações podem se preparar melhor para o impacto das ameaças atuais e futuras ao simularem ataques reais e exercitarem as Táticas, Técnicas e Procedimentos (TTPs) que adversários determinados e persistentes usam durante as violações. Em vez de simplesmente impedir a ocorrência de incidentes de segurança, é fundamental supor que um incidente de segurança pode ocorrer e que ocorrerá. As informações obtidas do uso de Equipes Vermelhas e os exercícios de testes de penetração de sites ativos ajudam a fortalecer significativamente as defesas, a aprimorar as estratégias de resposta, a treinar defensores e a orientar uma maior eficácia do programa de segurança como um todo.

As organizações não podem identificar de forma abrangente as lacunas na detecção e na resposta de segurança simplesmente se concentrando em estratégias de prevenção de violações. Compreenda como não só a proteção é importante, mas também detectar as violações e responder a elas é tão importante quanto — se não for mais — executar uma ação para impedir uma violação em primeiro lugar. Ao planejarem-se para os piores cenários, por meio de jogos de guerra (exercício e penetração de mesa) e uso de Equipes Vermelhas (ataque e penetração reais), as organizações podem desenvolver os recursos necessários para detectar tentativas de penetração e melhorar significativamente as respostas associadas a violações de segurança.

###Uso de Equipes Vermelhas no Microsoft Enterprise Cloud 

Este artigo descreverá como a Microsoft utiliza o uso de Equipes Vermelhas, uma forma de testes de penetração de sites ativos, na infraestrutura gerenciada, nos serviços e nos aplicativos da Microsoft. Você também aprenderá como a Microsoft simula violações do mundo real, monitora continuamente a segurança e as práticas de resposta de incidente de segurança para testar e melhorar a segurança do Microsoft Azure e do Office 365 (O365). No entanto, observe que nenhum dado de cliente final é deliberadamente visado durante o uso de Equipes Vermelhas e os testes de penetração de sites ativos. Os testes são feitos na infraestrutura e nas plataformas do Microsoft Azure e do O365, bem como nos locatários, nos aplicativos e nos dados da Microsoft. Os locatários, os aplicativos e os dados do cliente hospedados no Microsoft Azure ou no O365 nunca são visados.

###Além da prevenção 

As estratégias e as tecnologias de prevenção de segurança não podem garantir a segurança contra todos os ataques. Dado o panorama de ameaças de hoje, é vital confirmar que uma violação já ocorreu ou que é apenas uma questão de tempo até que ela ocorra. É mais provável que uma organização já tenha sido comprometida, mas simplesmente ainda não descobriu isso. Operar com essa suposição remodelará a detecção e as estratégias de resposta de uma maneira que supere os limites de infraestrutura, de pessoas, de processos e de tecnologias de qualquer organização.

![Citação da NSA](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig1.png)

De acordo o Relatório de Investigação de Violação de Dados 2014 da Verizon, em cerca de 80% dos casos, a organização invadida não detectou o comprometimento (pág. 12, <http://www.verizonenterprise.com/DBIR/2014/>[)](http://www.verizonenterprise.com/DBIR/2014/). Em vez disso, eles foram notificados da violação por terceiros, como um cliente, autoridades competentes, serviço de terceiros ou uma agência de detecção de fraudes externa. Acima disso, essas métricas são obtidas apenas de incidentes em que o invasor eventualmente foi detectado! Elas não incluem os casos em que o invasor até agora passou despercebido.

Nas seções a seguir, discutiremos a mudança para uma nova estratégia de segurança que está sendo usada em serviços de nuvem da Microsoft, incluindo o Microsoft Azure e o Office 365. Essa estratégia de segurança, chamada Assume Breach, é uma metodologia e uma mudança cultural que altera a premissa básica do design, de engenharia e de operações, supondo que os invasores já tenham explorado vulnerabilidades, tenham obtido acesso privilegiado e que estejam ativamente persistentes nos serviços de produção.

##Metodologia de segurança tradicionais 

As metodologias de segurança tradicionais se concentraram amplamente na prevenção. A prevenção é uma estratégia de defesa destinada à eliminação de vulnerabilidades, atenuando assim as violações de segurança antes que eles ocorram. No portfólio dos Microsoft Online Services (que inclui o Microsoft Azure, o Office 365, o CRM Online e outros), isso envolve melhorias contínuas para processos de segurança com nossos programas [*Desenvolvimento de Segurança*](http://www.microsoft.com/security/sdl/default.aspx) [*Garantia de Segurança Operacional*](http://www.microsoft.com/download/details.aspx?id=40872) (OSA). A Modelagem de Ameaças, a análise de código estático e os testes de segurança são úteis para a enumeração, a redução e o gerenciamento de superfícies de ataque — mas não eliminam todos os riscos de segurança.

Um exemplo de uma estratégia de prevenção é a forma como a Microsoft limita o acesso do operador/administrador aos funcionários com uma necessidade comprovada de acesso e que atendam aos requisitos qualificação (por exemplo, que passaram em uma verificação de antecedentes, que atenderam a todos os requisitos de conformidade e de segurança, em uma função/cargo que exija o acesso etc.). Além disso, os administradores mantêm zero permissões permanentes; em vez disso, eles recebem acesso JIT (Just-In-Time)[1] e JEA (Just Enough Administration)[2]. Outros exemplos incluem segregar o ambiente de email do funcionário do ambiente de produção e o uso de estações de trabalho robustas especializadas e altamente seguras para executar operações confidenciais.

Sempre que possível, a intervenção do usuário será substituída por processos automatizados, com auditoria pesada e baseados em ferramentas. Alguns exemplos de funções de rotina incluem a implantação, a depuração, a coleta de dados de diagnóstico e a administração do serviço. Os Microsoft Online Services continuam a investir na automação de segurança e nas operações de sistemas para reduzir a exposição a possíveis riscos de segurança.

De uma perspectiva de tecnologia de operações, os investimentos em prevenção contra violação limitam a exposição, mas nunca conseguirão eliminá-la completamente. Portanto, embora a adoção e a maturação contínua dos programas de segurança da Microsoft (por exemplo, SDL e OSA) e das tecnologias permaneçam um meio importante para ajudar a impedir as violações de segurança, todos deverão aceitar que é inevitável que as violações de segurança afetarão a organização, os serviços e os usuários.

###Ameaças novas e emergentes 

Durante os últimos cinco anos ou mais, uma categoria de ameaça específica tem sido muito mais discutida. A Ameaça Persistente Avançada (APT) foi um termo cunhado para se referir às tentativas patrocinadas pelo Estado-nação de invasão de redes governamentais e industriais de defesa militar com o objetivo específico de se infiltrar e obter dados confidenciais. Hoje, o termo APT é amplamente utilizado em círculos de mídia e de segurança para descrever qualquer ataque que pareça especificamente destinado a uma organização individual ou que se acredite ser notavelmente técnico por natureza, independentemente de o ataque ter sido realmente avançado ou persistente. As características comuns de uma APT incluem:

- Planejamento sofisticado

- Direcionamento específico/sequencial

- Reconhecimento efetivo

- Uso de ferramenta

- Engenharia social

Os adversários de hoje têm recursos e orquestração substanciais à disposição deles. Independentemente da sofisticação do adversário, as tendências de incidentes de segurança desde o fim de 2009 até hoje são uma indicação clara de que a maior probabilidade de um evento e de riscos está aumentando. A sofisticação crescente e a natureza direcionada das ameaças de segurança, juntamente com sua frequência cada vez maior sugere que, cedo ou tarde, as violações de segurança afetarão todos os usuários e as organizações.

No atual cenário de ameaças, um foco somente na prevenção não é suficiente para lidar com os adversários persistentes e determinados. Além disso, com as ferramentas de segurança comuns, como antivírus e sistemas de detecção de intrusões (IDS), é difícil capturar ou reduzir toda a gama de violações de hoje. Os controles de borda de rede podem manter os amadores do lado de fora, mas os invasores talentosos e motivados sempre encontrarão os meios para entrar nesses perímetros virtuais. Como resultado, com frequência as organizações estão mal preparadas ao enfrentar a necessidade de responder à profundidade e à amplitude de uma violação.

Com a evolução de TI e com a adoção da nuvem, os limites da empresa não podem mais ser definidos por um perímetro de rede gerenciado de forma física ou virtual por meio de firewalls. Os dados corporativos, incluindo os dados confidenciais e os aplicativos, podem ser encontrados praticamente em todos os lugares: no local, em datacenters privados, na nuvem, com parceiros e em uma variedade de dispositivos de usuário. Tudo isso exige uma estratégia de segurança fundamentalmente diferente, bem como de uma mudança nas metodologias de segurança utilizada pela maioria das organizações.

A resposta à violação sempre representou muitos desafios, incluindo a identificação do escopo de violação, a notificação em tempo hábil para as partes interessadas e os clientes, a investigação da perda de dados e a recuperação de ativos comprometidos. Por meio de uma combinação dos adversários atuais e da evolução do IT, a resposta a violação nunca foi um desafio tão grande como agora. Portanto, em vez de manter o foco tradicional em simplesmente impedir violações, uma estratégia de segurança efetiva pressupõe que adversários determinados e persistentes conseguirão violar todas as defesas.

##Metodologia Assume Breach 

O panorama de ameaças atual requer que as estratégias de segurança eficazes equilibrem os investimentos em prevenção com a detecção e a resposta. Por meio de uma análise detalhada das tendências de segurança, a Microsoft começou a defender e a realçar a necessidade dos investimentos adicionais em processos e tecnologias reativos de segurança que se concentram na detecção e na resposta às ameaças emergentes, em vez de apenas na prevenção dessas ameaças. Além disso, como resultado das alterações no panorama de ameaças e de uma análise aprofundada, a Microsoft decidiu refinar sua estratégia de segurança além de simplesmente impedir violações de segurança para utilizar uma estratégia mais bem equipada para lidar com as violações quando elas ocorrerem – uma estratégia que considerasse a previsão dos principais eventos de segurança, não como uma questão de “se”, mas “quando”.

Embora as práticas de Assume Breach da Microsoft já estejam em vigor por muitos anos, a maioria dos clientes não estão cientes do trabalho que está sendo feito nos bastidores para otimizar a nuvem da Microsoft. A Assume Breach é uma mentalidade que orienta os investimentos em segurança, as decisões sobre design e as práticas de segurança operacional. A Assume Breach limita a confiança em aplicativos, em serviços, em identidades e em redes ao tratá-los, internos e externos, como não seguros e provavelmente já comprometidos. Embora a estratégia Assume Breach não nasceu de uma violação de qualquer um dos serviços de nuvem ou empresariais da Microsoft, ela foi um reconhecimento de que muitas organizações, em todos os setores, estavam sendo violadas, apesar de todas as tentativas para impedir isso.

Embora impedir as violações seja uma parte crítica das operações de qualquer organização, essas práticas deverão ser continuamente testadas e ampliadas para enfrentarem efetivamente os adversários modernos, como APTs. Para que as organizações se preparem para uma violação, primeiro elas deverão criar e manter procedimentos de resposta de segurança cuidadosamente testados, robustos e repetíveis.

Embora os processos de segurança de Prevent Breach, como a modelagem de ameaças, as revisões de código e os testes de segurança, podem ser comuns nos ciclos de vida do desenvolvimento seguro, a Assume Breach oferece diversas vantagens que ajudam a contabilizar a segurança geral ao exercitar e medir os recursos reativos no caso de uma violação. Na Microsoft, cumprimos isso por meio de exercícios contínuos de jogos de guerra e de testes de penetração de sites ativos de nossos planos de resposta de segurança com a intenção de aprimorarmos os recursos de detecção e de resposta.

![Prevent Breach e Assume Breach](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig2.png)

*Figura 1: Evitar e supor modelos de violação*

Com a Assume Breach, o foco na segurança muda para a identificação e o tratamento das lacunas em:

- Detecção de ataque e de penetração

- Resposta a ataques e a penetrações

- Recuperação de perda de dados, de falsificações ou de comprometimento

- Prevenção de ataques e penetrações futuros

A Assume Breach verifica que os mecanismos de proteção, de detecção e de resposta são implementados adequadamente - até mesmo reduzindo as ameaças potenciais de “invasores profissionais” (usando ativos legítimos, como contas e máquinas comprometidas).

A natureza dos modelos de nuvem e de computação híbrida adiciona significância à função que os testes de penetração têm na manutenção de um ambiente operacional seguro, incluindo a preparação para lidar com as violações. Já que vários aspectos da nuvem da Microsoft não estão sob o controle direto do cliente, a Microsoft trabalha não só para proteger contra ataques à infraestrutura, à plataforma e aos serviços, mas também para responder a eles.

A Microsoft conduz, portanto, exercícios de jogos de guerra e uso de Equipes Vermelhas regulares para avaliar e aprimorar a prontidão da Assume Breach da Microsoft. Por meio da modelagem de ataques reais, bem como de testes de penetração, a Microsoft pode testar sua capacidade de lidar com ataques, identificar lacunas na detecção e na resposta e se concentrar em recursos para tratar essas lacunas. Ao praticar a resposta a incidentes de segurança, a execução de monitoramento contínuo, a análise forense e a recuperação, a Microsoft se esforça para desenvolver os recursos essenciais necessários para lidar com violações.

###Execução da Assume Breach 

A Assume Breach nos serviços de nuvem da Microsoft foi executada inicialmente por meio de jogos de guerra e depois por exercícios de violação real, chamada de violação Red Team, destinada a simular ataques reais. As violações da Equipe Vermelha testam as habilidades da Microsoft de responder a ataques direcionados e persistentes com o objetivo de reduzir significativamente o Tempo Médio para Detecção (MTTD)[3] e o Tempo Médio para Recuperação (MTTR)[4].


![Ciclo de execução da Assume Breach](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig3.png)

*Figura 2. Ciclo de execução do Assume Breach*

Os jogos de guerra são um exercício de mesa que serve com uma simulação de incêndio versus um incêndio real. Os exercícios da Equipe Vermelha oferecem simulações completas de violações que oferecem, o mais realisticamente possível, incidentes de segurança que preparam os envolvidos com o tratamento de violações reais.

Os jogos de guerra e as violações da Equipe Vermelha oferecem oportunidades de resposta a incidentes de segurança praticados antes de um evento real – quanto mais prática, mas bem equipado você será em um incidente ou em uma violação genuínos


###Jogos de guerra 

Antes de dedicar recursos a todas as violações da Equipe Vermelha, na Microsoft, começamos a fazer exercícios de mesa chamados jogos de guerra. Os exercícios de jogos de guerra são semelhantes à [Modelagem de Ameaças SDL](http://msdn.microsoft.com/magazine/dd148644.aspx)[,](http://msdn.microsoft.com/magazine/dd148644.aspx)[ ](http://msdn.microsoft.com/magazine/dd148644.aspx)mas direcionados ao processo de resposta de segurança e ao pessoal de uma organização ou de um serviço que lidem com um ataque. A intenção dos jogos de guerra é aprimorar os procedimentos de resposta a incidentes de segurança ao envolver a equipe de diferentes grupos dentro da Microsoft – de Segurança a Engenharia e Operações. À medida que iniciamos e demos prosseguimento aos jogos de guerra com uma profundidade cada vez maior, tornou-se claro quais grupos ou representantes estavam ausentes e precisavam ser envolvidos.

Qualquer indivíduo ou grupo que provavelmente foi necessário e envolvido em uma violação real foi adicionado como um participante dos nossos exercícios de jogos de guerra. A lista de representantes aumentou não apenas para incluir Segurança, Engenharia e Operações, mas os representantes de quase todos os grupos da empresa, como Jurídico, Recursos Humanos, Marketing e até mesmo os executivos. O envolvimento de um amplo conjunto de grupos e de indivíduos ajudou a criar os relacionamentos necessários nas unidades de negócios e nas disciplinas que garantem o desenvolvimento de planos abrangentes de resposta a violações. Em muitos casos, esses exercícios também serviram para educar e fornecer conhecimento em toda a Microsoft sobre os riscos e as necessidades associados à resposta de violação.

O objetivo principal dos exercícios de jogos de guerra é garantir a integridade de Procedimentos Operacionais Padrão (SOPs) e aprimorar a detecção e a resposta. Há três (3) fases principais de exercícios de jogos de guerra:

-   Identificação do cenário de ataque

-   Processo de ataque e de resposta de simulação

-   Post-mortem

A identificação de cenários de ataque foi iniciada pela formulação de uma lista de possíveis ataques ao modelo. Esses cenários de ataque tiveram muitas formas diferentes e muitas vezes foram combinados a vários cenários de ataque, como:

-   Negação de serviço

-   Ataque de malware

-   Execução remota de código

-   Comprometimento de dados do cliente

-   Invasor interno

-   Comprometimento de serviço

Os cenários geralmente eram escolhidos e priorizados em relação aos cenários comuns ou por meio da revisão de incidentes de segurança relatados anteriormente (conhecidos por terem afetado a Microsoft, os nossos concorrentes ou que ocorreram no setor).

Depois que um cenário de ataque tiver sido escolhido, um ou mais dos membros do grupo, normalmente um membro da equipe de segurança, faria o papel do adversário (ataque) e descreveria a execução do cenário de ataque. Este é o início do processo de ataque e de resposta fictícios.

Os outros membros participantes dos jogos de guerra fariam o papel da defesa e descreveriam como eles poderiam detectar ou responder ao cenário de ataque - mencionando qualquer Procedimento Operacional Padrão já existente (ou identificando os ausentes ou incompletos). Quando a defesa respondesse com etapas para detectar, investigar, reduzir ou recuperar-se do ataque fictício, o ataque responderia com métodos de contra-ataque que geralmente invalidariam as suposições da defesa e forçariam os defensores a pensar mais profundamente sobre detecção e resposta.

Por exemplo, se a defesa sugerisse a identificação e o bloqueio dos endereços IP dos invasores usados no ataque, o ataque observaria a facilidade de alterar os endereços IP de origem ou de aproveitar a infraestrutura de ataque comumente disponível, como os botnets, para originar o ataque de vários endereços IP. Esse vai e vem constante, durante os exercícios de mesa, leva a um aumento significativo de SOPs e à identificação de várias necessidade de investimento em segurança, bem como de algumas discussões valiosas.

Embora sejam muitos úteis, os jogos de guerra têm suas limitações sobre o que pode ser medido – especialmente quando lidam com ataques contínuos direcionados e com adversários persistentes. Portanto, a Microsoft se propôs a avaliar e a medir a prontidão de ataques sofisticados de adversários persistentes e determinados. Além de lidarmos com as violações de segurança real, podemos considerar que há não uma forma melhor do que simplesmente fazer isso – produzir violações de segurança reais - usando um conceito chamado uso de Equipes Vermelhas. O uso de Equipes Vermelhas refere-se ao uso de táticas de violação reais para ataque e penetração. O uso de Equipes Vermelhas pega o aspecto teórico dos jogos de guerra e o torna real.

### Uso de Equipes Vermelhas 

A estratégia de segurança Assume Breach é executada por dois (2) grupos fundamentais: a Equipe Vermelha (invasores) e a Equipe Azul (defensores)[5]. Conhecida como uso de Equipes Vermelhas (Red Teaming), a abordagem serve para testar os sistemas as operações do Microsoft Azure e do Office 365 usando as mesmas Táticas, Técnicas e Procedimentos (TTPs) que os adversários reais, na infraestrutura de produção, sem o conhecimento prévio das equipes de Engenharia ou de Operações da infraestrutura e da plataforma.

Isso testa a detecção de segurança e os recursos de resposta, além de ajudar a identificar as vulnerabilidades de produção, os erros de configuração, as suposições inválidas ou outros problemas de segurança de forma controlada. Cada violação da Equipe Vermelha é seguida pela divulgação completa entre a Equipe Vermelha e a Equipe Azul para identificar as lacunas, tratar as descobertas e aprimorar significativamente a resposta às violações.

#### A Equipe Vermelha 

A Equipe Vermelha é um grupo de funcionários em tempo integral da Microsoft que se concentra na violação da infraestrutura, da plataforma e dos próprios locatários e aplicativos da Microsoft. Eles são o adversário dedicado (um grupo de hackers éticos) que executa ataques direcionados e persistentes contra os Online Services (a infraestrutura, as plataformas e os aplicativos da Microsoft, mas não os aplicativos e os dados dos clientes finais).

A função de uma Equipe Vermelha é atacar e invadir ambientes usando as mesmas etapas de uma cadeia de eliminação de um adversário[6] mostrada na Figura 3:

![Estágios de violação](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig4.png)

*Figura 3. Estágios de violação*

Portanto, pesquisar e compreender os incidentes do setor e as tendências do cenário de ameaças para ficar atualizado em relação às técnicas e às ferramentas de ataque mais recentes usadas pelos adversários é uma parte fundamental de qualquer abordagem da Equipe Vermelha. Como uma das propriedades da Internet mais atacadas no mundo, o Microsoft Online Services gera uma grande quantidade de dados sobre as ameaças emergentes. A Equipe Vermelha usa essa pesquisa e essa inteligência não apenas para modelar, como também para executar táticas reais associadas a uma cadeia de eliminação de um adversário.

Além de pesquisar e de modelar os adversários conhecidos, a Equipe Vermelha desenvolve e deriva suas próprias técnicas para comprometer o Microsoft Azure e o Office 365 usando métodos de ataque e ferramentas de penetração personalizados. Como adversários determinados, a Equipe Vermelha utiliza ameaças emergentes e mescladas para comprometer e mudará de tática quando encontrar novos bloqueios ou defesas.

Como os invasores talentosos e motivados violam as defesas do perímetro, a Equipe Vermelha também fará isso. Os controles de borda podem manter os amadores do lado de fora, mas os adversários persistentes sempre conseguem entrar. Uma vez dentro, é comum que a Equipe Vermelha adquira privilégios internos que poderão ser usados para movimentos laterais de penetração ainda mais profunda na infraestrutura.. Além disso, assim como os adversários mais capacitados, a Equipe Vermelha estabelece um campo inimigo a partir do qual mantêm a persistência e pode modificar continuamente a abordagem para escapar da detecção.

Por exemplo, a Equipe Vermelha pode instalar ferramentas personalizadas (bots, controle remoto etc.) que permitam o acesso contínuo a um recurso comprometido e a recuperação de informações sempre que ela quiser. A mecânica de tal ataque permite que a Equipe Vermelha não apenas se infiltre e obtenha dados confidencias como também aproveite esses dados comprometidos (novamente, somente os dados da Microsoft, e não os dados dos clientes).

Devido à natureza confidencial e crítica do trabalho, os funcionários que trabalham nas Equipes Vermelhas da Microsoft são submetidos a padrões muito altos de segurança e de conformidade. Eles passam por validação extra, triagem de antecedentes e treinamento antes de poderem participar de qualquer cenário de ataque. Embora nenhum dado de cliente seja deliberadamente afetado pela Equipe Vermelha, eles mantêm os mesmos requisitos de Acesso a Dados do Cliente

(ATCD) que a equipe de Operações de serviço que implanta, mantém e administra o Microsoft Azure e o Office 365. Além disso, as Equipes Vermelhas só atam a infraestrutura e as plataformas gerenciadas da Microsoft. Em vez de atacar os dados ou os aplicativos do cliente final, as Equipes Vermelhas atacam locatários, aplicativos e dados na nuvem que a Microsoft possui e opera.

No entanto, as Equipes Vermelhas seguem um código de conduta bastante rígido. Os cinco (5) principais princípios de orientação do código de conduta da equipe vermelha incluem:

1.  Não causar impacto ou tempo de inatividade intencionais no contrato de Nível de Serviço (SLA).

2.  Não acessar ou modificar intencionalmente dados do cliente.

3.  Não executar intencionalmente ações destrutivas.

4.  Não enfraquecer as proteções de segurança locais.

5.  Resguardar informações críticas ou de vulnerabilidade na Equipe Vermelha e só compartilhá-las com quem precisar conhecê-las.

Além disso, as Equipes Vermelhas do Azure e do O365 devem seguir um conjunto documentado de Regras de Participação (ROE), desenvolvidas para garantir que o código de conduta seja atendido. Essas Regras de Participação são assinadas pela liderança da Microsoft.

Algumas métricas de segurança são essenciais para controlar cada Equipe Vermelha à medida que ela executa suas violações:

-   Tempo Médio de Comprometimento (MTTC)

-   Tempo Médio de Elevação de Privilégio ou "Pwnage" (MTTP)

O Tempo Médio de Comprometimento mede o início dos exercícios até o ponto no tempo em que a Equipe Vermelha consiga comprometer um ativo. Nesse estágio, a Equipe Vermelha geralmente estabelece sua base no ambiente de destino. O Tempo Médio de Elevação de Privilégio, ou o que alguns adversários preferem chamar de "Pwnage", é o tempo desde o início do exercício até o comprometimento total. Por exemplo, em ambientes baseados no Active Directory, esse é o ponto no tempo quando a Equipe Vermelha adquire privilégios de Administrador de Domínio e/ou compromete o Controlador de Domínio.

Você pode considerar o MTTP como "Fim de Jogo", já que a maioria das organizações não está bem preparada para lidar com esse nível de violação. No entanto, também vale a pena observar que os cenários de "Fim de Jogo" não são os únicos objetivos ou os cenários demonstrados pelo uso de Equipes Vermelhas. Os objetivos da violação da Equipe Vermelha podem incluir roubo de dados, comprometimento no nível do locatário ou Negação de Serviço, entre outros. Além disso, métricas MTTC e MTTP podem ser calculadas por exercício ou também podem ser rastreadas e calculadas por ativo de destino ou por tipo de ativo para granularidade adicional.

A função das Equipes Vermelhas da Microsoft é identificar lacunas nos controles de segurança do destino. O controle da MTTC e da MTTP permite que a Microsoft determine uma linha de base a partir da qual se aprimorará continuamente. Ao explorar as vulnerabilidades de segurança e realçar as lacunas de monitoramento e de recuperação de segurança, o uso de Equipes Vermelhas melhora as ferramentas e o processo de resposta a incidentes (mais sobre isso posteriormente). Mais importante, o uso de Equipes Vermelhas produz um impacto demonstrável, por meio de comprometimentos, o que prova a necessidade da Assume Breach. Como resultado, o uso de Equipes Vermelhas enumera os riscos corporativos e justifica as necessidades de investimento, de prioridades e de recursos de segurança.

#### A Equipe Azul 

A Equipe Azul é composta por um conjunto dedicado de respondentes ou de membros de segurança das organizações de resposta a incidentes, de Engenharia e de Operações. Independentemente de suas características, eles são independentes e operam de forma separada da Equipe Vermelha. A Equipe Azul segue processos de segurança estabelecidos e usa as ferramentas e as tecnologias mais recentes para detectar ataques e penetração e para responder a eles.

Assim como os ataques reais, a Equipe Azul não sabe quando ou como os ataques da Equipe Vermelha ocorrerão ou quais métodos poderão ser usados. Seu trabalho, seja um ataque da Equipe Vermelha ou um ataque legítimo, é detectar todos os incidentes de segurança e responder a eles. Por esse motivo, a Equipe Azul fica de plantão 24 horas por dia, sete dias por semana, 365 dias por ano e deve reagir às violações da Equipe Vermelha da mesma forma como o fariam com qualquer outro adversário.

Quando um adversário, como a Equipe Vermelha, tiver violado um ambiente, a Equipe Azul deverá:

-   Coletar evidências deixadas pelo adversário

-   Detectar a evidência como uma Indicação de Comprometimento

-   Alertar as equipes apropriadas de Engenharia e de Operações

-   Fazer a triagem de alertas para determinar sele eles exigem mais investigação

-   Coletar o contexto do ambiente para verificar o escopo da violação

-   Formar um plano de correção para conter ou remover o adversário

-   Executar o plano de correção e recuperar-se da violação

Essas etapas formam a cadeia de eliminação de resposta a incidentes de segurança que é executada em paralelo à cadeia de eliminação do adversário, como mostrado na Figura 4:

![Estágios de resposta de violação](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig5.png)

*Figura 4. Estágios de resposta de violação*

Em poucas palavras, as violações da Equipe Vermelha permitem exercitar a capacidade da Equipe Azul de detectar ataques reais completos e de responder a eles. Mais importante, ela permite a prática de resposta a incidentes de segurança antes de uma violação genuína (de um adversário muito menos cooperativo). Além disso, como resultado de violações da Equipe Vermelha, a Equipe Azul aprimora a percepção da situação, o que pode ser valioso ao lidar com futuras violações (da Equipe Vermelha ou de outro adversário).

Durante o processo de detecção e resposta, a Equipe Azul produz inteligência acionável e obtém visibilidade das condições reais dos ambientes que está tentando defender. Frequentemente, isso é feito por meio de análise de dados e de análise forense, realizadas pela Equipe Azul, ao responder a ataques da Equipe Vermelha e por meio do estabelecimento de indicadores de ameaça, como os Indicadores de Comprometimento.

De forma muito semelhante a como a Equipe Vermelha identifica as lacunas na segurança, as Equipes Azuis identificam as lacunas na capacidade de detectar e de responder. Além disso, como a Equipe Vermelha modela ataques reais, a Equipe Azul pode ser avaliada com precisão quanto à sua capacidade, ou incapacidade, de lidar com adversários determinados e persistentes.

Por fim, as violações da Equipe Vermelha medem a preparação e o impacto da resposta de violação da organização.

As métricas que normalmente são avaliadas pela Equipe Azul incluem:

- Tempo Estimado para Detecção (ETTD)

- Tempo Estimado para Recuperação (ETTR)

Esses tempos são “estimados” porque, durante uma violação, independentemente de a detecção ocorrer ou quando ocorrer, a Equipe Azul não poderá ter 100% de certeza de quando a Equipe Vermelha foi bem-sucedida no comprometimento. Da mesma forma, a Equipe Azul não pode estar totalmente certa se conseguiu remover com êxito a Equipe Vermelha e recuperar-se por completo de uma violação. A falta de evidência da presença da Equipe Vermelha não é uma prova da remoção do adversário ou da recuperação da violação. Isso é o que torna a resposta a incidentes de segurança tão desafiadora – é semelhante a montar um quebra-cabeça, uma peça de cada vez, sem saber como será a imagem final do quebra-cabeça e quais peças do quebra-cabeça podem estar ausentes de uma imagem completa.

Com o maior reconhecimento do panorama geral de ameaças e a maior visibilidade das condições reais no ambiente de operação, a Equipe Azul pode ampliar as práticas e os procedimentos de resposta a incidentes de segurança para aprimorar a preparação para lidar com uma violação.


#### Vermelha versus Azul 

Em vez dos testes de penetração tradicionais, as violações da Equipe Vermelha se esforçam para simular ameaças reais, incluindo a "névoa da guerra" inerente[7]. Isso significa que a Equipe Azul não sabe o que a Equipe Vermelha está fazendo e vice-versa. Quando a Equipe Vermelha comprometer um ativo, poderá disparar um alerta de segurança ou incidente sem perceber que fez isso.

Por outro lado, a Equipe Vermelha pode disparar intencionalmente alertas para verificar e validar suposições de monitoramento ou como uma tática de distração. Da mesma forma, durante um exercício, a Equipe Vermelha poderá observar a atividade de resposta, como redefinições de senha, recuperação de ativos comprometidos, mecanismos de persistência removidos ou até mesmo a experiência de remoção completa (embora esse último possa ser raro).

Com isso, as violações da Equipe Vermelha podem observar os impactos da detecção e da recuperação. No entanto, a detecção e a recuperação não podem ser avaliadas com precisão somente pela Equipe Vermelha ou pela Equipe Azul. O Tempo Médio de Detecção (MTTD) e o Tempo Médio de Resposta (MTTR) podem ser calculados apenas no final de um exercício quando a divulgação completa ocorrer entre as Equipes Azul e Vermelha. Para afetar totalmente a detecção e a resposta, uma Equipe Vermelha deve:

- Emular um amplo intervalo de TTPs enraizados em inteligência sobre os adversários enfrentados por uma organização (ou que ela enfrentará)

- Empregar TTPs em toda a cadeia de eliminação que exercitará e validará as detecções e a resposta

- Empregar a sofisticação técnica e a sofisticação operacional

- Utilizar a repetição para garantir que as melhorias de detecção e de resposta sejam suficientes para combater os adversários persistentes e determinados

Para garantir que o exercício inclua o a iniciação e o envolvimento da Equipe Azul, a Equipe Vermelha poderá disparar intencionalmente a detecção e a resposta. Isso geralmente acontece depois que a Equipe Vermelha cumpre todos os seus objetivos principais e não tenha observado uma atividade da Equipe Azul.

Um exemplo de acionamento de detecção e de resposta é causar intencionalmente uma verificação antimalware ou fazer com que um ativo, como um controlador de domínio (DC), alerte sobre o uso de um arquivo de teste EICAR[8]. A presença de um alerta de segurança inesperado, especialmente em um ativo crítico como o controlador de domínio, deverá ativar os procedimentos de resposta da Equipe Azul. A criação de um incidente de segurança garante que uma violação de ponta a ponta seja exercitada – do comprometimento até a resposta. A menos que a Equipe Azul esteja envolvida, a resposta de violação poderá não ser totalmente exercitada.

O ideal é que a Equipe Azul não saiba (inicialmente) se um incidente foi disparado de um invasor real ou da Equipe Vermelha interna. Sempre que possível, a Equipe Azul deverá tratar os incidentes de segurança como incidentes reais; pelo menos até que a atribuição à Equipe Vermelha possa ser estabelecida e confirmada. Essa abordagem de simulações de violação do mundo real é o melhor método para criar defesas reais.


#### Post-mortem da violação da Equipe Vermelha 

No final de cada violação da Equipe Vermelha, as Equipes Vermelha e Azul deverão se reunir para um post-mortem, que avalia a violação e a resposta à violação. É nesse estágio que ambas as equipes compartilham táticas e lições aprendidas durante a divulgação completa. A Equipe Vermelha fornece todos os detalhes sobre quando e como eles violaram o ambiente, quais ativos foram comprometidos e controlados pela Equipe Vermelha, se a Equipe Azul foi parcialmente ou totalmente bem-sucedida na remoção da Equipe Vermelha e se a recuperação de violação foi concluída.

Além disso, a Equipe Azul fornece todos os detalhes sobre como e quando a Equipe Vermelha foi detectada (se tiver sido detectada), quais ativos foram identificados como comprometidos, quais mecanismos de persistência da Equipe Vermelha foram descobertos e quais etapas de resposta foram realizadas em uma tentativa de remover a Equipe Vermelha e de recuperar-se da violação. Esse é um dos elementos mais exclusivos e essenciais do uso de Equipes Vermelhas – a Equipe Vermelha é um dos únicos adversários que podem e que fornecerão detalhes sobre suas violações, bem como comentários sobre o êxito (ou a falta dele) da detecção e da resposta. A capacidade das Equipes Vermelha e Azul de comparar anotações é vital para garantir que todas as lacunas de detecção e de resposta sejam identificadas.

Somente durante o post-mortem, quando a divulgação completa ocorre, as verdadeiras métricas Tempo Médio Detecção (MTTD) e Tempo Médio de Recuperação (MTTR) poderão ser calculadas – por exemplo, comparando a diferença entre a MTTC da Equipe Vermelha e a ETTD da Equipe Azul para o cálculo da MTTD verdadeira. Essas métricas de detecção e de resposta, durante e após o incidente, são úteis para enumerar os riscos de negócios associados às lacunas de detecção e de recuperação de segurança identificadas como parte do exercício.

O restante de post-mortem é gasto com a colaboração entre as Equipes Vermelha e Azul para a identificação das vulnerabilidades e das necessidades de investimento que poderiam melhorar a proteção, a detecção e a resposta. Com cada violação da Equipe Vermelha, as Equipes Vermelha e Azul identificam os investimentos em segurança necessários para uma melhor proteção contra ataques futuros, para atrasar os adversários e para acelerar a detecção e a recuperação. Em outras palavras, aumentar a MTTC e reduzir a MTTD e a MTTR.

A etapa final durante o post-mortem é o fornecimento de relatórios detalhados por escrito, feitos pelas Equipes Vermelha e Azul. Esses relatórios incluem os cronogramas de violação das Equipes Vermelha e Azul, um resumo do impacto nos negócios do exercício, bem como uma lista detalhada de vulnerabilidades, de descobertas e de investimentos necessários para melhorar a detecção de violações e a resposta a elas.

### Princípios de segurança 

Uma vantagem importante do uso de Equipes Vermelhas é a consolidação dos recursos de segurança especializada e de hackers éticos. Como com qualquer outra profissão, a equipe de engenheiros e de operações tem áreas de especialização, que podem não incluir habilidades de ataque e de penetração. Na Microsoft, os engenheiros e os operadores estão familiarizados com o SDL/OSA e com os Critérios de Engenharia Comum (CEC), mas a Equipe Vermelha assume a responsabilidade de testar a segurança até os limites externos de um ambiente de produção que, caso contrário, seria muito difícil de realizar.

Ao investir no uso de Equipes Vermelhas com o único foco de compreender, de comunicar e de aproveitar as ameaças mais recentes durante ataques a sites ativos, a Microsoft se esforça para testar e para aprimorar continuamente seus recursos reativos e seguir princípios fundamentais de segurança:

- Resistir à estratégia de segurança de ancoragem em **cenários de ataque estáticos** ou a supor que os adversários virão apenas de **uma posição fixa**

- Utilizar **camadas de controles de segurança complementares**; o efeito é cumulativo, melhorando as defesas

- O **número e a distribuição** de controles de segurança é mais importante do que a eficiência individual

- Buscar **atrasar e responder** em vez de **impedir** um ataque

## Resumo 

As empresas de todo o setor se deparam com a cruel realidade de que elas podem estar vivendo em um estado constante de comprometimento. Isso fica ainda pior pelo fato de que um grande número de empresas ainda não reconhecem que elas também foram violadas. O panorama atual de ameaças exige a redução da exposição a ataques, incluindo as ameaças internas. A alteração mais fundamental exige que as empresas diminuam significativamente o tempo médio para detecção e recuperação de uma violação.

Este white paper destaca a necessidade das organizações de se tornarem mais ágeis na resolução de lacunas de segurança. Por meio da estratégia de segurança Assume Breach, a Microsoft continua sua busca incessante por investimentos mais amplos e aprofundados em segurança. Todas as organizações podem se beneficiar da adoção de estratégias de segurança semelhantes para o combate às ameaças emergentes e em evolução.

A Microsoft desenvolve recursos de gerenciamento de incidentes de segurança ao planejar-se, com antecedência, para evitar comprometimentos e ao simular ataques e penetrações com jogos de guerra e com uma Equipe Vermelha ativa. O uso de Equipes Vermelhas torna reais os cenários teóricos ao expor as lacunas na segurança, ao explorar vulnerabilidades ativas e ao fornecer evidências completas da necessidade da Assume Breach.

O mundo desafiador do uso de Equipes Azuis exercita a capacidade de detectar os ataques e de responder a eles. Ao realizar os exercícios de violação da Equipe Vermelha versus a Equipe Azul, as organizações de segurança podem se concentrar nos principais vetores de ataque, desenvolver contramedidas e desenvolver seus mecanismos de resposta essenciais necessários para a preparação contra adversários persistentes e determinados.

O uso de Equipes Vermelhas tornou-se uma das partes mais essenciais do desenvolvimento e da proteção da infraestrutura, da plataforma e dos serviços da Microsoft. As Equipes Vermelhas do Microsoft Azure e do Office 365 representam adversários sofisticados e permitem que a Microsoft valie e aprimore a segurança, fortaleça suas defesas e torne mais efetivos seus programas de segurança de nuvem empresarial.

Por meio de ataques e de penetração de sites ativos, as violações da Equipe Vermelha fornecem os meios críticos para a prática de resposta a incidentes de segurança, bem como para medir com precisão a preparação contra ataques reais e o impacto deles. Os clientes podem ter a certeza de que a Microsoft está continuamente aprimorando a proteção, a detecção e a resposta no processo de oferecer serviços de nuvem mais seguros.

## Referências e leitura adicional 

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre os serviços do Microsoft Azure e do Office 365, bem como itens específicos mencionados no texto principal:

- [Página inicial do Microsoft Azure](http://azure.microsoft.com) – informações gerais e links sobre o Microsoft Azure

- [Página inicial do Microsoft Office](http://products.office.com) – informações gerais e links sobre o Microsoft Office

- [Central de confiabilidade do Microsoft Azure](https://www.microsoft.com/TrustCenter/CloudServices/Azure)

- [Central de Confiabilidade do Microsoft Office 365](https://www.microsoft.com/TrustCenter/CloudServices/Office-365)

- [Microsoft Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)

- Microsoft Azure ou Office 365, pode ser relatado] o [**http://www.microsoft.com/security/msrc/default.aspx*](http://www.microsoft.com/security/msrc/default.aspx)[ ](http://www.microsoft.com/security/msrc/default.aspx) ou por email para **secure@microsoft.com*

- [Entrevista com Mark Russinovich sobre o Microsoft Azure e segurança - atualização sobre os testes de penetração](http://blogs.technet.com/b/matthewms/archive/2013/07/02/an-interview-with-mark-russinovich-on-windows-azure-amp-security-update-on-penetration-testing.aspx)

- [Vermelha versus Azul - testes de penetração de segurança interna do Microsoft Azure e no O365](http://azure.microsoft.com/documentation/videos/red-vs-blue-internal-security-penetration-testing-of-microsoft-azure/)



## APÊNDICE 

### Diferença entre o uso de Equipes Vermelhas e os testes de penetração 

As técnicas de teste de penetração padrão usam ferramentas comuns para tentar explorações, mas raramente aproveitam os recursos completos de um adversário experiente.

Os testes de penetração tradicionais representam somente um ponto no tempo e terminam sem sobrecarregar o ambiente e a resposta a incidentes de uma forma que um adversário real faz. O uso de Equipes Vermelhas é contínuo e não para quando o teste é concluído. O uso de Equipes Vermelhas encontrará pontos que outras metodologias de teste de penetração com frequência deixam passar (resumidos na Tabela 1 abaixo):

**TESTES DE PENETRAÇÃO TRADICIONAIS USO DE EQUIPES VERMELHAS**

| | Testes de penetração tradicionais | Uso de Equipes Vermelhas |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Persistência** | Para quando compromete o ambiente | Estabelece um mecanismo de persistência para manter o acesso e avaliar a integridade da recuperação de violação |
| **Ferramentas** | Usa apenas o que está disponível no momento e então deve esperar até que você se envolva no próximo ciclo de testes | Pesquisa constantemente novas explorações e vulnerabilidades, e implementa um ataque com novas ferramentas assim que elas são descobertas |
| **Atividade pós-violação** | São "chapéu branco" (só tentam entrar e, se conseguirem, normalmente não fazem mais nada) | Aproveita a violação ao se infiltrar e explorar os dados críticos para iniciar outros ataques |
| **Fuga e remoção** | Os testes estáticos podem não tentar fugir da detecção e parar se forem encontrados | Os testes dinâmicos que tentam evitar a detecção |
| **Adversários experientes** | Tendem a favorecer a sobrecarga de sistemas de segurança conhecidos e que estejam em vigor (já que talvez tenha conhecimento total dos sistemas de TI), testam se eles foram implantados corretamente ou usam explorações conhecidas | Verifica o que foi *e* o que não foi implantado para encontrar vulnerabilidades; usa o conhecimento interno e ferramentas personalizadas para atacar os sistemas, incluindo o desenvolvimento de explorações personalizadas e antes desconhecidas |
| **Site ativo** | Quase nunca ataca sistemas completos de produção; usado com mais frequência para testar sistemas de pré-implantação | Ataca sistemas de produção; ataca todas as camadas da pilha |
| **Motivação** | Faz suposições sobre o ambiente, pode não testar locais não divulgados | Não faz nenhuma suposição; tenta comprometer todos os lugares; inverte e altera táticas se/quando necessário |
| **Resultados** | Mostra quais testes foram realizados e os resultados (êxito / falha) | Mede e avalia com precisão MTTC, MTTP, MTTD e MTTR. Fornecem evidência detalhada sobre quando e como violar o ambiente, quais ativos foram comprometidos e se a detecção e a resposta foram bem-sucedidas. |


**Tabela 1: Resumindo as diferenças entre os testes de penetração tradicionais e o uso de Equipes Vermelhas**


##Por que atacar o ambiente de produção? 

Invariavelmente, os ambientes de testes e de pré-produção nunca correspondem ao ambiente de produção. As alterações feitas durante a implantação, as operações contínuas de serviço e as diferenças no uso de clientes de produção contribuem para uma variação no estado. Essa variação existe por um bom motivo, já que a produção enfrenta necessidades diferentes no que diz respeito à carga esperada, ao tempo de inatividade aceitável, bem como os requisitos de conformidade, normativos e contratuais.

O uso de Equipes Vermelhas é direcionado a sistemas reais por meio de testes de penetração de sites ativos em vez de simplesmente atacar um ambiente de desenvolvimento/testes em um laboratório com cargas hipotéticas, ou até mesmo componentes reais com dados fictícios. É uma das principais diferenças entre o uso de Equipes Vermelhas e as estratégias de segurança tradicionais: servidores reais, locatários reais e dados reais. Os testes de penetração de sites ativos de produção oferece uma medição muito mais confiável e precisa do cliente e dos impactos nos negócios associados a uma violação.

A penetração em sites ativos de produção oferece benefícios adicionais. Ele julga os recursos de defesa de detecção e de resposta. Os ataques reais testam a força das proteções implantadas e dos recursos de detecção e de resposta em produção (ou a falta dela). Ele força esses investimentos a aceitar o fato de que o ambiente de produção está sob ataque, que pode estar em um estado comprometido e representa uma função que impõe que a resposta a incidentes de segurança esteja regularmente praticando métodos para lidar com ataques direcionados e com adversários persistentes.

##Administrando uma Equipe Vermelha 

A adoção da Assume Breach não exige absolutamente que você invista em seus próprios recursos de Equipe Vermelha. Muitos dos benefícios podem ser percebidos simplesmente pela alteração da sua abordagem de implantação e de testes de segurança de aplicativos e de infraestrutura. Por exemplo, compreender que uma violação de dados pode persistir muito além do comprometimento inicial pode ajudar a projetar garantias para proteger as informações e os ativos críticos mesmo no caso de uma violação.

No entanto, o uso de Equipes Vermelhas é uma maneira efetiva de testar a veracidade de suas medidas e de seus processos de segurança, aumentando a consciência sobre o panorama atual e manter cientes das ameaças emergentes os engenheiros, as equipes de operações, os respondentes de segurança e a gerência. Para determinar quais devem ser suas prioridades de investimento, considere o seguinte:

-   **Prevent Breach versus Assume Breach** uma não é a substituição de outra. A Prevent Breach é a implementação crítica dos recursos de segurança para a defesa contra ataques. A Assume Breach vai muito além dessas proteções para contabilizar a detecção e a resposta antes durante e depois de uma violação.

-   A **identificação das vedetes** prioriza investimentos usando a abordagem Assume Breach. Quais são os dados mais importantes para os negócios? Onde os dados estão armazenados? Quais são os ativos mais importantes para a administração dos negócios?

-   A **identificação de exercícios relevantes** prioriza os exercícios de segurança com base nas tendências de violações e no TTP do invasor aplicável ao seu ambiente.

-   **Obtenha a aprovação** em particular no nível executivo e garanta a aprovação legal. Além disso, os vários grupos de trabalho/divisões de sua empresa devem estar cientes do trabalho que você está fazendo e devem aceitá-lo; iniciar ataques contra as operações que nunca foram atacadas antes poderia causar interrupções de serviço não intencionais e problemas de satisfação do cliente.


Depois de criar um plano para o uso de Equipes Vermelhas, você precisará proteger suas operações:

-   **Equipe** — verifique se você tem os recursos especialistas adequados em ambos os lados da Assume Breach (Equipes Vermelha e Azul). A Equipe Azul deve tratar todos os incidentes de segurança, incluindo as violações da Equipe Vermelha, como um incidente real. Considere a compensação dos recursos em tempo integral versus a terceirização, incluindo a segurança da propriedade intelectual produzida e comprometida durante o uso de Equipes Vermelhas. Se você envolver um fornecedor terceirizado, *verifique o escopo dele*, sua responsabilidade e sua reputação.

-   **Tela** — além de os membros da equipe terem as habilidades técnicas necessárias, eles deverão ser confiáveis; pesquise cuidadosamente os potenciais funcionários, incluindo as verificações detalhadas de histórico.

-   **Treinar** — se você estiver contratando funcionários em tempo integral, eles deverão ser treinados nas práticas de ética, de segurança, de privacidade e de conformidade, além de todos os requisitos exclusivos da sua empresa ou do seu setor (as agências externas deve ser certificadas para garantir as atividades de treinamento regulares).

-   **Auditoria** — é imperativo que atividade da Equipe Vermelha seja corretamente monitorada, registrada e auditada antes e durante os exercícios da Equipe Vermelha. Isso serve como uma ‘carta de saída livre da prisão’, caso a Equipe Vermelha seja responsabilizada por danos que não sejam o resultado do exercício; pode ajudar a garantir que a Equipe Vermelha siga um código de conduta rígido e aja de acordo com as Regras de Participação. Além disso, esses registros de auditoria e de log costumam ser fundamentais para o cálculo da MTTC, da MTTD e da MTTR, bem como para fornecer um relatório pós-exercício detalhado.

-   **Segurança operacional** – é absolutamente fundamental que os membros da Equipe Vermelha exercitem a segurança operacional rígida em relação aos TTPs, às ferramentas, às descobertas de segurança, aos relatórios, às explorações, aos backdoors e a outros ativos. Se um invasor obtiver acesso a ativos da Equipe Vermelha, seus membros deverão ser capazes de reconstruir e de reproduzir rapidamente os ataques contra vulnerabilidades conhecidas com impactos devastadores.

##Informações adicionais

Os testes de penetração de grande porte exigem o envolvimento de várias partes em uma organização. Uma vez que este documento aborda diretrizes de procedimentos, ele se destina às pessoas responsáveis pela avaliação de ambientes, de sistemas e de processos em busca de riscos de segurança. Observe que os detalhes fornecidos aqui são uma visão geral de como as equipes de segurança para o Microsoft Azure e o Office 365 devem conduzir o uso de Equipes Vermelhas e os testes de penetração de sites ativos, e não são instruções sobre como os clientes devem atacar a infraestrutura, as plataformas ou os serviços da Microsoft.

Embora a Microsoft conduza testes de penetração regularmente para aprimorar os controles e os processos de segurança da nuvem, compreendemos que as avaliações de segurança também são uma parte importante das atividades de desenvolvimento e de implantação de aplicativos dos nossos clientes. Assim, estabelecemos uma política para permitir que clientes realizem testes de penetração autorizados em seus aplicativos hospedados no Microsoft Azure.

Como esses testes podem ser difíceis de diferenciar de um ataque real, é essencial que os clientes realizem testes de penetração somente depois de obter aprovação com antecedência do Atendimento ao Cliente do Microsoft Azure. Os testes de penetração do cliente devem ser realizados de acordo com os termos e condições da Microsoft. As solicitações de testes de penetração devem ser enviadas com, no mínimo, sete dias de antecedência. Para saber mais, veja [Central de Confiabilidade da Microsoft]https://www.microsoft.com/TrustCenter/CloudServices/Azure).

## Referências

[1] Acesso e elevação JIT (Just-in-Time) – a elevação é concedida de acordo com a necessidade e somente no momento da necessidade. Geralmente implementado ao exigir uma ou mais aprovações antes que o acesso seja concedido.

[2] JEA (Just Enough Administration) – uma solução desenvolvida para ajudar a proteger os sistemas ao permitir que usuários específicos executem tarefas administrativas sem que lhes sejam concedidos direitos de administrador, com uma auditoria posterior de todas as ações executadas por esses usuários. Para saber mais, veja o [artigo sobre Just Enough Administration na Galeria do TechNet](https://gallery.technet.microsoft.com/Just-Enough-Administration-6b5ad370)

[3] No contexto de uma violação de segurança, MTTD refere-se ao tempo levado para detectar uma violação.

[4] MTTR refere-se ao tempo até a recuperação de uma violação depois de detectada.

[5] Os funcionários em tempo integral do Microsoft Azure e do Office 365 são separados entre as equipes vermelha e azul.

[6] Veja [“Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains”](http://www.lockheedmartin.com/content/dam/lockheed/data/corporate/documents/LM-White-Paper-Intel-Driven-Defense.pdf), da Lockheed Martin, para saber mais.

[7] Névoa de guerra – usado para descrever a incerteza na percepção da situação experimentada pelos participantes em relação às capacidades de cada um, às capacidades do adversário e à intenção do adversário.

[8] Arquivo de teste EICAR – também conhecido como Arquivo de Teste Antivírus Padrão - é um arquivo de computador desenvolvido pelo EICAR (Instituto Europeu para Pesquisa de Antivírus de Computador) e pela CARO (Organização de Pesquisa de Antivírus) para testar a resposta dos programas antimalware. Em vez de usar malware real, que poderia causar danos reais, esse arquivo de teste permite testar software antimalware sem o uso de um vírus de computador real.


*(c) 2014 Microsoft Corporation. Todos os direitos reservados. Este documento é fornecido "no estado em que se encontra". Informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. O uso é por sua conta e risco. Alguns exemplos são fornecidos somente para fins de ilustração e são fictícios. Nenhuma associação real é intencional ou inferida. Este documento não fornece a você nenhum direito legal a qualquer propriedade intelectual de qualquer produto da Microsoft. Você pode copiar e usar este documento para fins de consulta interna.*

<!---HONumber=AcomDC_0218_2016-->