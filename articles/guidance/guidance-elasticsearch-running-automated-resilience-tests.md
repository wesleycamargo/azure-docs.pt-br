<properties
   pageTitle="Executando testes automatizados de resiliência do Elasticsearch | Microsoft Azure"
   description="Descrição de como você pode executar testes de resiliência em seu próprio ambiente."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Executando os testes automatizados de resiliência do Elasticsearch

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

O documento [Teste de resiliência e recuperação] descreve uma série de testes que foram executados em um cluster Elasticsearch de exemplo para determinar como o sistema respondia a algumas formas comuns de falha e como ele se recuperava. Quatro cenários foram testados:

- **Falha de nó e reinicialização sem perda de dados**. Um nó de dados é interrompido e reiniciado após cinco minutos. O Elasticsearch foi configurado para não realocar fragmentos ausentes nesse intervalo; portanto, nenhuma E/S adicional é exigida na movimentação de fragmentos. Quando o nó é reiniciado, o processo de recuperação retorna os fragmentos nesse nó atualizados.

- **Falha de nó com perda de dados catastrófica**. Um nó de dados é interrompido e os dados que ele contém são apagados para simular uma falha de disco catastrófica. O nó é reiniciado (após cinco minutos), atuando efetivamente como uma substituição do nó original. O processo de recuperação requer a recriação dos dados ausentes para esse nó e pode envolver a realocação de fragmentos mantidos em outros nós.

- **Falha de nó e reinicialização sem perda de dados, mas com a realocação do fragmento**. Um nó de dados é interrompido e os fragmentos que ele contém são realocados para outros nós. O nó é reiniciado e outras realocações são feitas para reequilibrar o cluster.

- **Atualizações sem interrupção**. Cada nó do cluster é interrompido e reiniciado após um breve intervalo para simular a reinicialização dos computadores depois de uma atualização de software. Somente um nó é interrompido por vez. Os fragmentos não serão realocados enquanto um nó estiver inativo.

Esses testes foram colocados em script para que possam ser executados de maneira automática. Este documento descreve como você pode repetir os testes em seu próprio ambiente.

## Pré-requisitos

Os testes automatizados requerem os seguintes itens:

- Um cluster Elasticsearch

- Uma configuração de ambiente JMeter conforme descrito nas [Diretrizes de testes de desempenho].

- As adições a seguir instaladas somente na VM JMeter mestre.

    - Java Runtime 7.

    - Nodejs 4.x.x ou posterior.

    - Ferramentas de linha de comando do Git.

## Como funcionam os scripts

Os scripts de teste devem ser executados na VM JMeter mestre. Quando você seleciona um teste a ser executado, os scripts executam a seguinte sequência de operações:

1.  Inicie um plano de teste JMeter passando os parâmetros que você especificou.

2.  Copie um script que realiza as operações exigidas pelo teste na VM especificada no cluster. Isso poderá ser qualquer VM que tenha um endereço IP público, ou a VM do *Jumpbox*, se você tiver compilado o cluster usando o [Modelo de início rápido do Elasticsearch do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch).

3.  Execute o script na VM (ou Jumpbox).

A imagem a seguir mostra a estrutura do ambiente de teste e do cluster Elasticsearch. Observe que os scripts de teste usam SSH para se conectar a cada nó no cluster a fim de executar várias operações do Elasticsearch, como interromper ou reiniciar um nó.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## Configurando os testes do JMeter

Antes de executar os testes de resiliência, você deve compilar e implantar os testes JUnit localizados na pasta *resiliency/jmeter/tests*. Esses testes são referenciados pelo plano de teste JMeter. Para saber mais, confira o procedimento Como importar um projeto de teste JUnit existente no Eclipse em [Como implantar uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch][].

Há duas versões dos testes JUnit mantidos nas seguintes pastas:

- **Elasticsearch17.** O projeto nesta pasta gera o arquivo Elasticsearch17.jar. Use esse JAR para testar versões do Elasticsearch 1.7.x

- **Elasticsearch20**. O projeto nesta pasta gera o arquivo Elasticsearch20.jar. Use esse JAR para testar versões do Elasticsearch 2.0.0 e posteriores

Copie o arquivo JAR apropriado junto com o restante das dependências nas suas máquinas JMeter. O processo é descrito pelo procedimento Como implantar um teste JUnit JMeter em [Considerações sobre o JMeter].

## Configurando a segurança da VM para cada nó

Os scripts de teste exigem um certificado de autenticação para ser instalado em cada nó do cluster Elasticsearch. Isso permite que os scripts sejam executados automaticamente sem solicitar um nome de usuário ou senha quando se conectam às várias VMs.

Inicie entrando em um de nós no cluster Elasticsearch (ou na VM Jumpbox) e execute o seguinte comando para gerar uma chave de autenticação:

```Shell
ssh-keygen -t rsa
```

Enquanto estiver conectado ao nó Elasticsearch (ou Jumpbox), execute os comandos a seguir para cada nó do cluster Elasticsearch. Substitua `<username>` pelo nome de um usuário válido em cada VM e substitua `<nodename>` pelo nome DNS do endereço IP da VM que hospeda o nó do Elasticsearch. Observe que você será solicitado a fornecer a senha do usuário durante a execução desses comandos. Para saber mais, confira [Logon SSH sem senha](http://www.linuxproblem.org/art_9.html):

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## Baixando e configurando os scripts de teste

Os scripts de teste são fornecidos em um repositório Git. Use o procedimento a seguir para baixar e configurar os scripts.

Na máquina mestre JMeter onde você executará os testes, abra uma janela de área de trabalho do Git (Git BASH) e clone o repositório que contém os scripts da seguinte maneira:

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Mova para a pasta *resiliency-tests* e execute o seguinte comando a fim de instalar as dependências exigidas para executar os testes:

```Shell
npm install
```

Se o JMeter Master estiver em execução no Windows, [Baixe o PLINK](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) e copie-o para a pasta *resiliency-tests/lib*

Se o JMeter Master está sendo executado no Linux, você não precisa baixar o PLINK, mas precisa configurar o SSH sem senha entre o mestre JMeter e o nó do Elasticsearch ou do Jumpbox usado, seguindo as etapas descritas no procedimento [Configurar a segurança da VM para cada nó](#configuring-vm-security-for-each-node).

Edite o arquivo `config.js` e edite os parâmetros de configuração a seguir para corresponder com seu ambiente de teste e com o cluster Elasticsearch. Os parâmetros são comuns a todos os testes:

| Nome | Descrição | Valor Padrão |
| ---- | ----------- | ------------- |
| `jmeterPath` | Caminho do local onde se encontra o jmeter | `C:/apache-jmeter-2.13` |
| `resultsPath` | Diretório relativo onde o script despeja o resultado | `results` |
| `verbose` | Indica se as saídas do script estão em modo detalhado ou não. | `true` |
| `remote` | Indica se os testes jmeter são executados localmente ou em servidores remotos | `true` |
| `cluster.clusterName` | O nome do cluster Elasticsearch. | `elasticsearch` |
| `cluster.jumpboxIp` | O endereço IP da máquina jumpbox. |-| 
| `cluster.username` | O usuário de administrador criado durante a implantação do cluster |-| 
| `cluster.password` | A senha para o usuário admin |-| 
| `cluster.loadBalancer.ip` | O endereço IP do balanceador de carga do Elasticsearch |-| 
| `cluster.loadBalancer.url` | A URL base do balanceador de carga |-|

## Executando os testes

Mova para a pasta *resiliency-tests* e execute o seguinte comando:

```Shell
node app.js
```

O seguinte menu deve ser exibido:

![](./media/guidance-elasticsearch/resilience-testing2.png)

Insira o número do cenário que você deseja executar: `11`, `12`, `13` ou `21`.

Depois de selecionar um cenário, o teste será executado automaticamente. Os resultados são armazenados como um conjunto de arquivos CSV em uma pasta criada no diretório *results*. Cada execução tem sua própria pasta de resultados. Você pode usar o Excel para analisar e criar um gráfico com esses dados.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Diretrizes de testes de desempenho]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considerações sobre o JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[Teste de resiliência e recuperação]: guidance-elasticsearch-resilience-testing.md
[Como implantar uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
