<properties linkid="manage-services-biztalk-services-editions-chart" urlDisplayName="Gráfico das edições" pageTitle="Conheça os recursos das edições dos Serviços BizTalk | Azure" metaKeywords="Serviços BizTalk, introdução, Azure, edições" description="Comparar os recursos das edições dos Serviços BizTalk: Developer, Basic, Standard e Premium." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Basic" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />




# Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium

Os Serviços BizTalk do Azure oferecem quatro edições: Developer, Basic, Standard e Premium:

**Developer**: os recursos incluem processamento de mensagens EAI e EDI, conectividade híbrida usando o BizTalk Adapter Pack e cenários de mensagens EAI comuns conectando serviços na nuvem com qualquer protocolo HTTP/S, REST, FTP, WCF e SFTP para ler e escrever mensagens. Tudo em um ambiente centralizado no desenvolvedor com ferramentas do Visual Studio para permitir desenvolvimento e implantação fáceis. Limitado para fins de desenvolvimento e teste apenas sem nenhum Contrato de Nível de Serviço.

**Basic**: inclui o processamento de mensagens EAI e EDI com um portal de gerenciamento de parceiro comercial fácil de usar, suporte para esquemas comuns de EDI e processamento sofisticado de EDI por X12 e AS2. Pode criar cenários comuns de EAI conectando serviços na nuvem com qualquer protocolo HTTP/S, REST, FTP, WCF e SFTP para ler e gravar mensagens. O processamento e a mediação avançados de mensagens têm o suporte de ferramentas de desenvolvimento controladas pela configuração. Utilize a conectividade híbrida com sistemas LOB locais com adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel e SQL Server prontos para uso.

**Standard**: inclui todos os recursos da Basic, além de você poder dimensionar sua implantação para atender às suas necessidades crescentes.

**Premium**: inclui todos os recursos da Standard com aumentos de escala, pontes de EAI, contratos de EDI, conectividade híbrida e também adiciona Arquivamento.


A tabela a seguir lista as diferenças:

<table border="1">
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>Preço inicial</strong></td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.</td>
</tr>
<tr>
<td><strong>Configuração mínima padrão</strong></td>
<td>1 unidade Developer</td>
<td>1 unidade Basic</td>
<td>1 unidade Standard</td>
<td>1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Sim, em incrementos de 1 unidade Standard</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala máxima permitida</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Até 4 unidades</td>
<td>Até 8 unidades</td>
</tr>
<tr>
<td><strong>EAI Bridges por unidade</strong></td>
<td>30</td>
<td>50</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2
<br/><br/>
Inclui Contratos, Tipos de Mensagens, Portal dos Serviços BizTalk</strong></td>
<td>Inclusa. 10 contratos por unidade.</td>
<td>Inclusa. 25 contratos por unidade.</td>
<td>Inclusa. 250 contratos por unidade.</td>
<td>Inclusa. 1000 contratos por unidade.</td>
</tr>
<tr>
<td><strong>Conexões do Serviço do Adaptador do BizTalk para sistemas de LOB no local</strong></td>
<td>1 conexão</td>
<td>2 conexões</td>
<td>5 conexões</td>
<td>25 conexões</td>
</tr>
<tr>
<td align="left"><strong>Protocolos suportados/Sistemas:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Blob do Azure</li>
<li>APIs REST</li>
</ul>
</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Alta Disponibilidade</strong>
<br/><br/>
Para obter o Contrato de Nível de Serviço (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Preços dos Serviços BizTalk do Azure</a>.
</td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Backup e restauração</strong></td>
<td>Não incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Acompanhamento</strong></td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Arquivamento</strong><br/><br/>
Inclui NRR (não repúdio de recebimento) e o download de mensagens rastreadas</td>
<td>Incluso</td>
<td>Não incluso</td>
<td>Não incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
<tr>
<td><strong>Uso de transformações, incluindo XSLT personalizado</strong></td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
<td>Incluso</td>
</tr>
</table>

**Observação**
<br/>Para resiliência contra falhas de hardware, a Alta Disponibilidade implica em ter várias VMs dentro de uma única Unidade do BizTalk.

## Perguntas frequentes
#### O que é uma Unidade do BizTalk?
Uma “unidade” é o nível atômico de uma implantação dos Serviços BizTalk do Azure. Cada edição é fornecida com uma unidade que tem capacidade e memória de computação diferentes. Por exemplo, uma Unidade Básica tem mais computação do que a Developer, a Standard tem mais computação do que a Basic e assim por diante. Quando você dimensiona um Serviço BizTalk, você o faz em termos de Unidades.

#### Qual é a diferença entre os Serviços BizTalk e a VM do BizTalk do Azure?
Os Serviços BizTalk fornecem uma arquitetura de PaaS (Plataforma como Serviço) verdadeira para criar soluções de integração na nuvem. Com o modelo de PaaS, você focaliza completamente na lógica do aplicativo e deixa toda a infraestrutura de gerenciamento para a Microsoft, incluindo:

- Sem necessidade de gerenciar ou aplicar patches em máquinas virtuais
- A Microsoft garante a disponibilidade
- Você controla a escala sob demanda simplesmente solicitando mais ou menos capacidade por meio do Portal de Gerenciamento do Azure

O BizTalk Server em Máquinas Virtuais do Azure fornece uma arquitetura de IaaS (Infraestrutura como Serviço). Você cria máquinas virtuais e as configura exatamente como seu ambiente local, facilitando a execução dos aplicativos existentes na nuvem sem alterações no código. Com a IaaS, você ainda é responsável por configurar as máquinas virtuais, gerenciá-las (por exemplo, instalando software e patches do sistema operacional) e pela criação da arquitetura do aplicativo para alta disponibilidade. 

Se você estiver planejando criar novas soluções de integração que minimizem seu esforço de gerenciamento da infraestrutura, use os Serviços BizTalk. Se estiver planejando migrar rapidamente suas soluções existentes do BizTalk ou procurando um ambiente sob demanda para desenvolver e testar aplicativos do BizTalk Server, use o BizTalk Server em uma Máquina Virtual do Azure.

#### Quando crio um contrato nos Serviços BizTalk, por que o número de pontes é aumentado em dois, em vez de apenas um? 

Cada contrato é composto de duas pontes diferentes, uma ponte de comunicação do lado do envio e outra do lado do recebimento.

####  O que acontecerá quando eu atingir o limite de cota do número de pontes ou contratos? 

Você não poderá implantar nenhuma ponte nova, nem criar novos contratos. Para implantar mais, será necessário aumentar o número de unidades do Serviço BizTalk ou atualizar para uma Edição mais alta.

#### Como faço para migrar de uma camada dos Serviços BizTalk para outra? 

Use o fluxo de backup e restauração para migrar de uma camada para outra. Apenas alguns caminhos de migração têm suporte. Consulte [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873) para obter mais detalhes sobre os caminhos de migração com suporte.

#### O Serviço do Adaptador do BizTalk está incluído no serviço? Como recebo o software?

Sim, o Serviço do Adaptador do BizTalk com o BizTalk Adapter Pack são incluídos com o SDK dos Serviços BizTalk do Azure [download](http://www.microsoft.com/download/details.aspx?id=39087).

## Avançar

Para provisionar os Serviços BizTalk do Azure no Portal de Gerenciamento do Azure, consulte [Serviços BizTalk: provisionando com o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Consulte também
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: gráfico do status do provisionamento (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: guias Painel, Monitor e Escala (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: limitação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços BizTalk: nome e chave do emissor (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

