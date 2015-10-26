<properties 
   pageTitle="Criar um acordo entre parceiros comerciais no Serviço de Aplicativo do Microsoft Azure" 
   description="Criar acordos entre parceiros comerciais" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/01/2015"
   ms.author="rajram"/>


# Criando um acordo entre parceiros comerciais   

Parceiros comerciais são as entidades envolvidas em comunicações B2B (Entre Empresas). Quando dois parceiros estabelecem uma relação, isso é chamado de um *Acordo*. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo. Os diferentes transportes e protocolos B2B com suporte do Serviço de Aplicativo do Azure incluem:

- AS2 (Applicability Statement 2)
- EDIFACT (Nações Unidas/Intercâmbio Eletrônico de Dados para Administração, Comércio e Transporte (UN/EDIFACT))
- X12 (ASC X12)

### Aplicativos de API do BizTalk que dão suporte a cenários B2B
Os seguintes aplicativos de API habilitam esses recursos usando uma experiência rica e intuitiva no Portal do Azure:


## TPM (Gerenciamento de Parceiros Comerciais) do BizTalk
- Criação e gerenciamento de Parceiros, Perfis e Identidades
- Armazenamento e gerenciamento de Esquemas EDI
- Armazenamento e gerenciamento de certificados (usado no protocolo AS2)
- Criação e gerenciamento de Acordos AS2
- Criação e gerenciamento de Acordos EDIFACT (inclui envio em lote no lado do envio)
- Criação e gerenciamento de Acordos X12 (inclui processamento em lote no lado do envio)

![][1]


## Conector AS2
- Executa Acordos AS2 conforme definido na instância do aplicativo de API de TPM relacionada
- Revela informações de processamento/acompanhamento de AS2 para solução de problemas


## BizTalk EDIFACT
- Executa Acordos EDIFACT conforme definido na instância do aplicativo de API de TPM relacionada
- Revela informações de processamento/acompanhamento de EDIFACT para solução de problemas
- Fornece gerenciamento de estado em lotes (início e parada) conforme definido nos Acordos EDIFACT na instância do aplicativo de API TPM relacionado


## BizTalk X12
- Executa Acordos X12 conforme definido na instância do aplicativo de API de TPM relacionada 
- Revela informações de processamento/acompanhamento de X12 para solução de problemas
- Fornece gerenciamento de estado em lotes (início e parada) conforme definido nos Acordos X12 na instância do aplicativo de API TPM relacionado

Como mencionado anteriormente, aplicativos de API AS2, X12 e EDIFACT precisam de um aplicativo de API TPM para funcionar como esperado.


## Introdução
Para criar acordos entre parceiros comerciais:

1. Crie uma instância do conector do **Gerenciamento de Parceiros Comerciais do BizTalk**. Isso requer um Banco de Dados SQL em branco para funcionar. Antes de iniciar, certifique-se ter um banco de dados em branco disponível e pronto para uso.
2. Carregue esquemas e certificados conforme exigido pelos acordos. Faça isso procurando a instância de TPM criada e acessando a parte “Esquemas” e/ou “Certificados”
3. Navegue até a instância de TPM criada e acesse a parte **Parceiros**
4. Crie os parceiros conforme desejado. Edite os perfis conforme apropriado e adicione as identidades necessárias
5. Agora, use a seção **Acordos** para criar acordos. Ao criar um Acordo, você precisa selecionar o protocolo que será usado. As opções de configuração restantes têm base no protocolo selecionado.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.jpg
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.jpg
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.jpg
 

<!---HONumber=Oct15_HO3-->