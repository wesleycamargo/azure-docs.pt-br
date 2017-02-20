---
title: "Criar um contrato X12 no aplicativo lógico do Azure | Microsoft Docs"
description: "Aprenda a criar um contrato X12 para o Enterprise Integration Pack | Aplicativo Lógico do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 1a982309fe771c0c244c691ae648bd5f4844a825
ms.openlocfilehash: f7b61b85cadfabde6637e46f0e65108a29901198


---
# <a name="enterprise-integration-with-x12"></a>Integração corporativa com o X12

## <a name="prereqs"></a>Pré-requisitos
Para poder trocar mensagens X12, você precisará criar um contrato X12 e armazená-lo em sua conta de integração. As etapas a seguir orientarão você pelo processo de criação de um contrato X12.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) definida em sua assinatura do Azure  
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) configurados com o qualificador EDI X12 em identidades comerciais.   
* [Esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) obrigatório a ser carregado na [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="create-an-x12-agreement"></a>Criar um contrato X12
Depois de fazer logon no [Portal do Azure](http://portal.azure.com "Portal do Azure"):  

1. Entre no [portal do Azure](http://portal.azure.com "portal do Azure")
2. Selecione **Mais serviços** e digite **integração** na caixa de pesquisa de filtro. Selecione **Contas de Integração** na lista de resultados:    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Selecione a conta de integração à qual você deseja adicionar o certificado:    
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)   
4. Selecione o bloco **Contratos** . Caso você não veja o bloco de contratos, adicione-o:     
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Selecione o botão **Adicionar** na folha Contratos que se abre.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     
6. Insira um **Nome** para seu contrato, em seguida, selecione o **Tipo de Contrato**, **Parceiro de Host**, **Identidade do Host**,  **Parceiro Convidado**, **Identidade do Convidado**, na folha Contratos aberta.    
![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome do contrato |
| Tipo de contrato | Deve ser X12 |
| Parceiro de Host |Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato |
| Identidade do Host |Um identificador para o parceiro host |
| Parceiro Convidado |Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host |
| Identidade do Convidado |Um identificador para o parceiro convidado |
| Configurações de Recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato |
| Configurações de Envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato |  

> [!NOTE]
> A resolução do contrato X12 depende da correspondência entre o identificador e qualificador de remetente e o identificador e qualificador do receptor definidos no parceiro e na mensagem de entrada.  Se houver alterações desses valores no parceiro, edite o contrato.
> 
> 

## <a name="receive-settings"></a>Configurações de Recebimento

1. Selecione **Configurações de Recebimento** para configurar como as mensagens recebidas por este contrato devem ser manipuladas.  
2. O controle de Configurações de Recebimento é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Envelopes, Números de Controle, Validações e Configurações Internas. Configure essas propriedades com base no seu contrato com o parceiro com o qual você trocará mensagens. Veja a seguir uma exibição desses controles e configure-os com base em como você deseja que este contrato identifique e manipule as mensagens recebidas  

### <a name="identifiers"></a>Identificadores

![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | Descrição |
| --- | --- |
| ISA1 (Qualificador de Autorização) |Selecione o valor do Qualificador de autorização na lista suspensa. |
| ISA2 |Opcional. Insira o valor de Informações de autorização. Se o valor inserido para ISA1 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |
| ISA3 (Qualificador de Segurança) |Selecione o valor do Qualificador de segurança na lista suspensa. |
| ISA4 |Opcional. Insira o valor de Informações de segurança. Se o valor inserido para ISA3 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |

### <a name="acknowledgments"></a>Agradecimentos

![](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Retorna uma confirmação técnica ao emissor de intercâmbio |
| FA esperado |Retorna uma confirmação funcional ao emissor de intercâmbio. Então, selecione se deseja as confirmações 997 ou 999, com base na versão de esquema |
| Incluir Loop AK2/IK2 |Habilita a geração de loops AK2 em confirmações funcionais para conjuntos de transação aceitos |


### <a name="schemas"></a>Esquemas
Escolha um esquema para cada tipo de transação (ST1) e o Aplicativo de Remetente (GS2). O pipeline de recebimento desmontará a mensagem recebida associando os valores de ST1 e GS2 na mensagem recebida aos valores definidos aqui e o esquema da mensagem recebida com o esquema definido aqui.

![](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| Aplicativo do Remetente (GS02) |Selecione o aplicativo do remetente |
| Esquema |Selecione o arquivo de esquema que você deseja usar. Os esquemas são adicionados em sua conta de integração |

> [!NOTE]
> Configurar o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) obrigatório a ser carregado na [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Envelopes

![](./media/logic-apps-enterprise-integration-x12/x12-34.png) 

| Propriedade | Descrição |
| --- | --- |
| Uso de ISA11 |Use este campo para especificar o separador em um conjunto de transações:</br></br>Selecione o identificador Padrão para usar a notação decimal "." em vez da notação decimal no documento de entrada no pipeline de recebimento EDI.</br></br>Selecione o separador de Repetição para especificar o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, (^) geralmente é usado como separador de repetição. Para esquemas HIPPA, só é possível usar (^). |

### <a name="control-numbers"></a>Números de Controle

![](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | Descrição |
| --- | --- |
| Recusar duplicatas de Números de Controle de Intercâmbio |Bloqueia intercâmbios duplicados. Verifica o número de controle de intercâmbio (ISA13) para o número de controle de intercâmbio recebido. Se uma correspondência for detectada, o pipeline de recebimento não processará o intercâmbio. Você pode especificar o número de dias em que a verificação é executada, fornecendo o valor apropriado para *Verificar ISA13 duplicados a cada x dias* |
| Recusar duplicatas de Números de controle de grupo |Bloqueia intercâmbios com números de controle de grupo duplicados |
| Recusar duplicatas de Números de controle de Conjuntos de transações |Bloqueia intercâmbios com números de controle de conjunto de transação duplicados |

### <a name="validations"></a>Validações

![](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Tipos de mensagem EDI, como uma Ordem de Compra 850 ou uma Confirmação de Implementação 999. |
| Validação de EDI |Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação Estendida |Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín./máx.). |
| Permitir Zeros à Esquerda/Direita |Qualquer caractere adicional de espaço e zeros à esquerda ou à direita serão mantidos. Eles não são removidos. |
| Política de Separador à Direita |Gera separadores à direita no intercâmbio recebido. As opções incluem Não Permitido, Opcional e Obrigatório. |

### <a name="internal-settings"></a>Configurações Internas

![](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | Descrição |
| --- | --- |
| Converter o formato decimal implícito Nn para o valor numérico de base 10 |Converte um número de EDI que é especificado no formato Nn em um valor numérico de base&10; |
| Criar marcas XML vazias se forem permitidos separadores à direita |Marque essa caixa de seleção para que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita. |
| Dividir Intercâmbio como conjuntos de transação – suspender conjuntos de transação com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transações. Suspende somente as transações em que a validação falhar |
| Dividir Intercâmbio como conjuntos de transação – suspender intercâmbio com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Suspenderá o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação | 
| Preservar Intercâmbio – suspender conjuntos transação com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende somente os conjuntos de transação com falha na validação, enquanto continua a processar todos os outros conjuntos de transação |
| Preservar Intercâmbio – suspender intercâmbio com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro.  Suspenderá o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação |

Depois de ter definido as propriedades das configurações de recebimento, selecione o botão **OK**  
   
O contrato está pronto para manipular mensagens recebidas que estão em conformidade com o esquema selecionado.

## <a name="send-settings"></a>Configurações de Envio

1. Selecione **Configurações de Envio** para configurar como as mensagens enviadas por este contrato devem ser manipuladas.  

2. O controle de Configurações de Envio é dividido nas seções a seguir, incluindo Identificadores, Confirmação, Esquemas, Envelopes, Números de Controle, Conjuntos de Caracteres e Separadores e Validação.  Configure essas propriedades com base no seu contrato com o parceiro com o qual você trocará mensagens. Veja a seguir uma exibição desses controles e configure-os com base em como você deseja que este contrato identifique e manipule as mensagens enviadas.


### <a name="identifiers"></a>Identificadores

![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | Descrição |
| --- | --- |
| Qualificador de Autorização (ISA1) |Selecione o valor do Qualificador de autorização na lista suspensa. |
| ISA2 |Insira o valor de Informações de autorização. Se esse valor é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |
| Qualificador de segurança (ISA3) |Selecione o valor do Qualificador de segurança na lista suspensa. |
| ISA4 |Insira o valor de Informações de segurança. Se esse valor é diferente de 00, insira no mínimo um valor alfanumérico e no máximo 10 na caixa de texto Valor (ISA4). |

### <a name="acknowledgment"></a>Confirmação

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Marque essa caixa de seleção para retornar uma confirmação técnica (TA1) ao remetente do intercâmbio. Essa configuração especifica que o parceiro host que está enviando a mensagem solicitou uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
| FA esperado |Marque essa caixa de seleção para retornar uma confirmação funcional (FA) ao remetente do intercâmbio e selecione se deseja as confirmações 997 ou 999 com base nas versões de esquema com as quais você está trabalhando. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
| Versão de FA |Selecione a versão de FA |

### <a name="schemas"></a>Esquemas

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| Esquema |Selecione o esquema a ser usado. Esquema estão localizados na sua conta de integração. Se você selecionar o esquema pela primeira vez, ele configurará automaticamente o tipo de transação e a versão  |

> [!NOTE]
> Configurar o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) obrigatório a ser carregado na [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Envelopes

![](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | Descrição |
| --- | --- |
| Uso de ISA11 |Use este campo para especificar o separador em um conjunto de transações |
| Identificador padrão |Selecione o identificador Padrão para usar a notação decimal "." |
| Separador de repetição |Selecione o separador de Repetição para especificar o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, (^) geralmente é usado como separador de repetição. Para esquemas HIPAA, só é possível usar (^) |

### <a name="control-numbers"></a>Números de Controle

![](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | Descrição |
| --- | --- |
| Número de Versão de Controle (ISA12) |Selecione a versão do padrão X12 |
| Indicador de Uso (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informações, dados de produção ou dados de teste |
| Esquema |Gera os segmentos GS e ST para um intercâmbio de codificação X12 que ele envia para o Pipeline de Envio |
| GS1 |Opcional, selecionar um valor para o código funcional na lista suspensa |
| GS2 |Opcional, remetente do aplicativo |
| GS3 |Opcional, receptor do aplicativo |
| GS4 |Opcional, selecionar CCYYMMDD ou YYMMDD |
| GS5 |Opcional, selecionar HHMM, HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecionar um valor para a agência responsável na lista suspensa |
| GS8 |Opcional, versão do documento |
| Número de Controle de Intercâmbio (ISA13) |Obrigatório, inserir um intervalo de valores para o número de controle de intercâmbio. Inserir um valor numérico entre 1 e 999999999 |
| Número de Controle de Grupo (GS06) |Obrigatório, inserir um intervalo de números para o número de controle de grupo. Inserir um valor numérico entre 1 e 999999999 |
| Número de Controle de Conjunto de Transações (ST02) |Obrigatório, inserir um intervalo de números para o número de controle de conjunto de transações. Insira um valor numérico entre 1 e 999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controle de conjuntos de transações usados na confirmação. Insira um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle |
| Suffix |Opcional, designado para o intervalo de números de controle de conjuntos de transações usados em uma confirmação. Inserir um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle |

### <a name="character-sets-and-separators"></a>Conjuntos de Caracteres e Separadores

Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a serem usados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

![](./media/logic-apps-enterprise-integration-x12/x12-9.png) 


| Propriedade | Descrição |
| --- | --- |
| Conjunto de Caracteres a ser usado |Selecione o conjunto de caracteres X12 para validar as propriedades.  As opções são básico, estendido e UTF8 |
| Esquema |Selecione um esquema na lista suspensa. Para o esquema selecionado, selecione o conjunto de separadores a ser usado |
| Tipo de entrada |Selecione um tipo de entrada na lista suspensa |
| Separador de elementos de componentes |Inserir um único caractere para separar os elementos de dados compostos |
| Separador de elemento de dados |Inserir um único caractere para separar os elementos de dados simples dentro dos elementos de dados compostos |
| Caractere de substituição |Inserir um caractere de substituição. Ao gerar a mensagem de saída X12, todas as instâncias de caracteres separadores no conteúdo são substituídas pelo caractere especificado |
| Terminador de segmento |Inserir um único caractere para indicar o final de um segmento EDI |
| Suffix |Selecionar o caractere a ser usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento ficar vazio, você deverá designar um sufixo. |

### <a name="validation"></a>Validação

![](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Selecionar o tipo de mensagem da lista |
| Validação de EDI |Essa opção habilita a validação no destinatário do intercâmbio. Essa validação realiza a validação de EDI em elementos de dados do conjunto de transação, validando tipos de dados, restrições de comprimento, elementos de dados vazios e separadores à direita |
| Validação Estendida |Essa opção permite a validação estendida de intercâmbios recebidos do remetente de intercâmbio. Isso inclui validação de comprimento do campo, campos opcionais e contagem repetida, além da validação de tipo de dados XSD. Você pode habilitar a validação estendida sem habilitar a validação de EDI ou vice-versa. |
| Permitir zeros à esquerda/direita |Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio EDI não atender ao requisito de comprimento devido a espaços à direita, mas estiver de acordo com esse requisito quando os espaços são removidos. |
| Separador à direita |Essa opção especifica que um intercâmbio de EDI recebido não falhará na validação se um elemento de dados em um intercâmbio de EDI não estiver atender ao requisito de comprimento devido a zeros à esquerda (ou direita) ou espaços à direita,mas estiver de acordo com esse requisito quando os espaços ou zeros são removidos.</br></br>Selecione Não Permitido se não quiser permitir delimitadores e separadores à direita em um intercâmbio recebido do remetente de intercâmbio. Se o intercâmbio tiver delimitadores e separadores à direita, ele será declarado inválido.</br></br>Escolha Opcional para aceitar intercâmbios com ou sem delimitadores e separadores à direita.</br></br>Selecione Obrigatório se o intercâmbio recebido precisa ter delimitadores e separadores à direita. |

Depois de ter definido as propriedades das configurações de envio, selecione o botão **OK**.  O contrato está pronto para manipular mensagens enviadas que estão em conformidade com o esquema selecionado.

Selecione OK para criar o contrato.

Selecione o bloco **Contratos** na folha da Conta de Integração e você verá o contrato recém-adicionado listado.  
![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  




<!--HONumber=Feb17_HO1-->


