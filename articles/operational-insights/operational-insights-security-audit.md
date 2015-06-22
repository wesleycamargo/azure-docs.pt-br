<properties 
   pageTitle="Explorar os dados de segurança e auditoria do Insights Operacionais"
   description="Saiba mais sobre como você pode usar o pacote de inteligência de Segurança e Auditoria para obter uma visão abrangente da postura de segurança de TI de sua organização com consultas de pesquisa interna para problemas importantes que exigem atenção"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Explorar os dados de segurança e auditoria do Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

O pacote de inteligência de Segurança e Auditoria do Insights Operacionais fornece uma visão abrangente da postura de segurança de TI de sua organização com consultas de pesquisa interna para problemas importantes que exigem atenção.

Neste artigo, você irá:

- Conduzir uma investigação simples para um executável suspeito
- Entender as práticas recomendadas para análise forense
- Entender as práticas recomendadas para investigações de padrão de violação de segurança
- Entender as práticas recomendadas para cenários de auditoria

## Conduzir uma investigação simples para um executável suspeito

1. Entrar no Insights Operacionais.
2. Na página **Visão geral**, examine as informações exibidas no bloco **Segurança e Auditoria** e, em seguida, clique nele. ![Imagem da página Visão geral](./media/operational-insights-security-audit/sec-audit-dash02.png)
3. Na página **Segurança e Auditoria**, examine as informações na folha **PROBLEMAS IMPORTANTES**. Na imagem de exemplo você verá seis problemas importantes de hoje com dois de ontem. Neste exemplo, há um executável suspeito. Clique em **Executáveis Suspeitos** na folha **PROBLEMAS IMPORTANTES** . ![Imagem da página Segurança e Auditoria](./media/operational-insights-security-audit/sec-audit-dash03.png)
4. A pesquisa exibe a consulta e os resultados para o executável suspeito que você clicou. No exemplo, há um resultado e o hash do arquivo é exibido. Clique na ID **FILEHASH** . ![Imagem do filehash dos Resultados de pesquisa](./media/operational-insights-security-audit/sec-audit-search01.png) 
5. A pesquisa exibe informações adicionais sobre o arquivo executável, incluindo seu nome de processo e o caminho do arquivo. Clique no **&lt;Nome do arquivo&gt; do processo**. No exemplo, isso é HEXEDIT. EXE. ![Imagem do processo dos Resultados de pesquisa](./media/operational-insights-security-audit/sec-audit-search02.png) 
6. A pesquisa anexará o nome do processo entre aspas na consulta. "* * HEXEDIT. EXE "* *, neste exemplo. ![Imagem da Consulta de pesquisa](./media/operational-insights-security-audit/sec-audit-search03.png)
7. Na caixa de Pesquisa, remova tudo menos o nome do processo e as aspas e, em seguida, clique no ícone de Pesquisa. ![Imagem das informações de pesquisa detalhada](./media/operational-insights-security-audit/sec-audit-search04.png)
8. A pesquisa exibe informações detalhadas sobre o processo, incluindo os computadores em que o processo é executado, a conta de usuário em que o processo é executado, e a data e hora em que um evento foi criado para o processo.
9. Usando as informações que encontrar, você pode tomar uma ação corretiva conforme necessário. Por exemplo, se determinar que o executável é malware, em seguida, você deve agir para removê-lo de todos os sistemas de computador que ele afeta. Depois que o executável é removido e o Insights Operacionais recebe eventos de auditoria e log atualizados para os sistemas de computador, os valores na folha PROBLEMAS IMPORTANTES são alterados no dia seguinte.

## Práticas recomendadas para análise forense

**Procurar as evidências**

Ao realizar uma análise forense usando o pacote de inteligência de Segurança e Auditoria, você procura a evidência que usuários mal-intencionados deixarem para trás. Independentemente do que os usuários estão fazendo em seu ambiente de TI, muitas das atividades em que participam geram artefatos de segurança. Você deve observar que logs geralmente são apagados por invasores e costuma ser a primeira etapa que eles executam ocultando seu caminho.

No entanto, se os usuários estão acessando seus próprios computadores ou acessando-os remotamente, as evidências sobre seu uso são armazenadas nos logs de eventos. O Insights Operacionais coleta esses artefatos *assim que eles ocorrem*, antes de qualquer pessoa poder violá-los e permite executar diferentes tipos de análise, correlacionando os dados em vários computadores.

**Saiba como configurar e coletar eventos de auditoria**

Para aproveitar ao máximo o pacote de inteligência de Segurança e Auditoria, você deve configurar o nível dos eventos de auditoria coletados por seu ambiente do Windows que melhor atenda às suas necessidades, usando os recursos a seguir.

- [Como Definir Configurações de Política de Segurança](https://technet.microsoft.com/library/dn135243(v=ws.10).aspx)

- [Configurações Avançadas de Política de Auditoria](https://technet.microsoft.com/library/jj852202(v=ws.10).aspx)

- [Recomendações de Política de Auditoria](https://technet.microsoft.com/library/dn487457.aspx)

**Habilitar o AppLocker**

Você também deve habilitar os eventos do AppLocker para obter informações detalhadas sobre as execuções do processo que ocorrem em seu ambiente de TI. Para obter mais informações, consulte [Para Configurar uma Política do AppLocker como Somente Auditoria](https://technet.microsoft.com/library/hh994622.aspx)

**Configurar o nível de auditoria de eventos do Windows**

O ambiente de computação do Windows fornece a capacidade de configurar o nível de captura de registros relacionados à segurança. Por exemplo, você pode configurar seu ambiente para que sempre que alguém acesse um arquivo, leia um arquivo ou abra um arquivo, um evento seja gerado. O nível de detalhe que você deseja coletar varia dependendo de suas necessidades. No entanto, todas as opções que você habilita vêm com algum tipo de custo, porque você precisa armazenar todas as informações coletadas. Por esse motivo, em muitos ambientes de organizações de TI, as pessoas optam por não habilitar a coleta de dados de gravação do objeto ou de leitura do objeto. Sempre que alguém acessa qualquer arquivo, milhares de eventos podem ser gerados, alguns dos quais são ruídos inúteis. O nível de coleção que você decide depende do seu bom senso.

>[AZURE.NOTE]Se você estiver usando o agente direto e tiver um servidor proxy em sua organização, você deve configurá-lo para permitir que o agente acesse o Insights Operacionais. Para obter mais informações, consulte [Definir configurações de proxy e firewall](operational-insights-proxy-firewall.md).

## Práticas recomendadas para investigações de padrão de violação de segurança

**Investigue os padrões de atividade anormal**

Violações de segurança geralmente são originadas por credenciais legítimas e exigem que um usuário mal-intencionado tente obter um privilégio elevado por meio de ataques. O pacote de inteligência de Segurança e Auditoria não se concentra na detecção de intrusões mas ajuda-o a investigar e descobrir padrões de atividades anormais com Problemas Importantes. Por exemplo, você deve investigar as seguintes atividades anormais e quaisquer outras que apareçam nos **Problemas Importantes**.

- logons incomuns no computador de um usuário que normalmente não o utiliza

- enumeração de rede incomum de usuários ou computadores atípicos

- novas contas de usuários criadas com direitos administrativos

- alterações em políticas de segurança ou de log

- executáveis suspeitos

## Práticas recomendadas para cenários de auditoria

Sua organização pode ter regulamentos e políticas de conformidade de rede e computador que você deve cumprir, exigindo registros extensos de auditoria. Por exemplo, se sua organização é uma empresa de finanças, que talvez você precise manter registros provar para qualquer ponto no tempo, qual usuário executou uma operação específica na rede. Talvez você também seja solicitado a gerar relatórios detalhando a atividade de um usuário específico ou servidor selecionado sob demanda e a voltar no tempo muitos meses, às vezes até anos atrás.

Você pode usar o pacote de inteligência de Segurança e Auditoria para coletar dados de auditoria em todo o seu ambiente de TI, tanto se os computadores estiverem no local ou na nuvem. Todos os dados de auditoria são armazenados, indexados e com uma assinatura Premium para o Insights Operacionais; todos os dados são armazenados indefinidamente. Em seguida você pode exibir dados de auditoria, realizar pesquisas e correlacionar através de diferentes tipos de dados e computadores para obter resultados abrangentes para qualquer intervalo de tempo desde que os dados foram coletados.

**Use a Política de Grupo para coletar dados de auditoria**

Os dados de auditoria que você deseja coletar e enviar ao Insights Operacionais é totalmente gerenciado com a Política de Grupo. Você pode usá-lo para definir as configurações de segurança como parte de um Objeto de Política de Grupo (GPO), que são vinculadas aos contêineres do Active Directory como sites, domínios e unidades organizacionais, e eles ajudam a gerenciar as configurações de segurança. Os dados de política são registrados e posteriormente enviados para o serviço do Insights Operacionais.

**Use o AppLocker para coletar dados de auditoria**

Além das configurações de política local, se você usar o AppLocker para coletar dados de auditoria, o Operacional Insights coletará os dados e, em seguida, você poderá exibí-los.

<!--HONumber=54--> 