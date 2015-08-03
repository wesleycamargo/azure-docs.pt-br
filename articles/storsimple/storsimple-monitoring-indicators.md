<properties 
    pageTitle="Indicadores de monitoramento do StorSimple"
    description="Descreve os LEDs e alarmes usados para monitorar o status do dispositivo StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="06/11/2015"
    ms.author="v-sharos" />

# Indicadores de monitoramento do StorSimple   

## Visão geral

O seu dispositivo StorSimple inclui diodos emissores de luz (LEDs) e alarmes que você pode usar para monitorar os módulos e o status geral do dispositivo StorSimple. Os indicadores de monitoramento podem ser encontrados nos componentes de hardware do compartimento primário do dispositivo e o do compartimento EBOD. Os indicadores de monitoramento podem ser LEDs ou alarmes audíveis.

Há três estados de LED usados para indicar o status de um módulo: verde, verde intermitente a vermelho-âmbar ou vermelho-âmbar.

- LEDs verdes representam o status de funcionamento adequado.  
- Verde intermitente a vermelho-âmbar representam a presença de condições não críticas que podem exigir a intervenção do usuário.  
- LEDs vermelho-âmbar indicam que há uma falha crítica presente no módulo.  

O restante deste artigo descreve os vários LEDs indicadores de monitoramento, seus locais no dispositivo StorSimple, o status do dispositivo com base em estados do LED e quaisquer alarmes audíveis associados.

## LEDs indicadores no painel frontal

O painel frontal, também conhecido como o *painel de operações* ou *painel ops*, exibe o status agregado de todos os módulos no sistema. O painel frontal é idêntico no dispositivo StorSimple e o compartimento EBOD é ilustrado abaixo.

   ![][1]

**Figura 1: Painel frontal do dispositivo**
 
O painel frontal contém os seguintes indicadores:

1. Botão liga / desliga
2. LED Indicador de energia (verde/vermelho-âmbar)
3. LED indicador de falha de módulo (aceso vermelho-âmbar/apagado)
4. LED indicador de falha lógica (aceso vermelho-âmbar/apagado)
5. Exibição da ID da unidade  

A principal diferença entre os LEDs do painel frontal do dispositivo e do compartimento EBOD é o **Número de Identificação da Unidade de Sistema** mostrado na tela do LED. A unidade padrão exibida no dispositivo de ID é **00**, enquanto que a ID de unidade padrão exibida no compartimento EBOD é **01**. Isso permite que você possa diferenciar rapidamente entre o dispositivo e o compartimento EBOD quando o dispositivo está ativado. Se o dispositivo estiver desativado, use as informações fornecidas em [Ativando um novo dispositivo](https://msdn.microsoft.com/library/azure/dn772378.aspx) para diferenciar o dispositivo do compartimento EBOD.

## Status do LED do painel frontal  

Use a tabela a seguir para identificar o status indicado pelos LEDs no painel frontal do dispositivo ou do compartimento EBOD.

|Energia do sistema | Falha do módulo | Falha lógica | Alarme | Status|
|-------------|---------------|-----------------|-------|-------|
|Vermelho-âmbar | DESATIVADO | DESATIVADO | N/D | Perda de energia CA, operando com energia reserva ou energia CA ativada e os módulos do controlador foram removidos.|
|Verde | ATIVO | ATIVO | N/D | Estado de teste do painel de operações ligado (5s)|
|Verde | DESATIVADO | DESATIVADO | N/D | Energia ligada, todas as funções em boas condições|
|Verde | ATIVO |N/D | LEDs de falha do ventilador, LEDs de falha de PCM | Qualquer falha de PCM, falha do ventilador, temperatura acima ou abaixo do recomendado|
| Verde | ATIVO | N/D | LEDs de módulo de E/S | Qualquer falha do módulo do controlador|
| Verde | ATIVO | N/D | N/D | Falha lógica no compartimento|
| Verde | Piscando | N/D | LED de status de módulo no módulo do controlador. LEDs de falha do ventilador, LEDs de falha de PCM | Tipo de módulo controlador desconhecido instalado, falha de barramento I2C, erro de configuração dos dados vitais do produto (VPD) do módulo do controlador |

## LEDs indicadores de refrigeração do módulo de energia (PCM)   

LEDs indicadores de refrigeração do módulo de energia (PCM) podem ser encontrados na parte traseira do compartimento principal ou do compartimento EBOD em cada módulo PCM. Este tópico discute como usar os seguintes LEDs para monitorar o status do seu dispositivo StorSimple.

- LEDs de PCM para o compartimento principal
- LEDs de PCM para o compartimento EBOD

## LEDs de PCM para o compartimento principal  

O dispositivo StorSimple tem um módulo PCM de 764W com uma bateria adicional. A ilustração a seguir mostra o painel de LED para o dispositivo.

   ![][2]

**Figura 2: LEDs de PCM no compartimento principal**
 
Legenda do LED:

1. Falha de energia CA
2. Falha do ventilador
3. Falha de bateria
4. PCM OK
5. Falha de CC
6. Bateria boa  

O status do PCM é indicado no painel do LED O painel do LED do PCM do dispositvo possui seis LEDs. Quatro destes LEDs exibem o status da fonte de energia e do ventilador. Os dois LEDs restantes indicam o status do módulo de bateria de backup no PCM. Você pode usar as tabelas a seguir para determinar o status do PCM.

### LEDs indicadores do PCM para a fonte de energia e para o ventilador
| Status | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | Falha de DC (âmbar) |
|--------|----------------|-----------------------|------------------|----------------------|
| Sem energia de CA (para o compartimento) | DESATIVADO | DESATIVADO | DESATIVADO | DESATIVADO|
| Sem energia de CA (somente este PCM) | DESATIVADO | ATIVO | DESATIVADO | ATIVO |
| CA presente PCM ligado - OK | ATIVO | DESATIVADO | DESATIVADO | DESATIVADO |
| Falha no PCM (falha do ventilador) | DESATIVADO | DESATIVADO | ATIVO | N/D |
| Falha no PCM (excesso de amperagem, sobretensão, sobrecorrente) | DESATIVADO | ATIVO | ATIVO | ATIVO |
| PCM (ventilador fora da tolerância) | ATIVO | DESATIVADO | DESATIVADO | ATIVO |
| Modo standby | Piscando | DESATIVADO | DESATIVADO | DESATIVADO |
| Download de firmware do PCM | DESATIVADO | Piscando | Piscando | Piscando |

### LEDs indicadores do PCM para a bateria de backup  

| Status | Bateria boa (verde) | Falha na bateria (âmbar) |
|--------|----------------------|-----------------------|
| Bateria ausente | DESATIVADO | DESATIVADO |
| Bateria presente e carregada | ATIVO | DESATIVADO |
| Bateria carregando ou descarga de manutenção | Piscando | DESATIVADO |
| Falha “pequena” na bateria (recuperável) | DESATIVADO | Piscando |
| Falha “grave” na bateria (não recuperável) | DESATIVADO | ATIVO |
| Bateria desarmada | Piscando | DESATIVADO |

## LEDs de PCM para o compartimento EBOD  

O compartimento EBOD possui um PCM de 580W e nenhuma bateria adicional. O painel do PCM para o compartimento EBOD possui LEDs indicadores para as fontes de energia e para o ventilador. A ilustração a seguir mostra estes LEDs.

   ![][3]

**Figura 3: LEDs de PCM no compartimento EBOD**
 
Você pode usar a tabela a seguir para determinar o status do PCM.

| Status | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | Falha de DC (âmbar) |
|--------|---------------|------------------------|------------------|----------------------|
| Sem energia de CA (para o compartimento) | DESATIVADO | DESATIVADO | DESATIVADO | DESATIVADO |
| Sem energia de CA (somente este PCM) | DESATIVADO | ATIVO | DESATIVADO | ATIVO |
| CA presente PCM ligado - OK | ATIVO | DESATIVADO | DESATIVADO | DESATIVADO |
| Falha no PCM (falha do ventilador) | DESATIVADO | DESATIVADO | ATIVO | X |
| Falha no PCM (excesso de amperagem, sobretensão, sobrecorrente | DESATIVADO | ATIVO | ATIVO | ATIVO |
| PCM (ventilador fora da tolerância) | ATIVO | DESATIVADO | DESATIVADO | ATIVO |
| Modelo standby | Piscando | DESATIVADO | DESATIVADO | DESATIVADO |
| Download de firmware do PCM | DESATIVADO | Piscando | Piscando | Piscando |

## LEDs indicadores do módulo do controlador  

O dispositivo StorSimple contém LEDs para o controlador primário e para os módulos do controlador EBOD.

### LEDs de monitoramento para o controlador primário.
A ilustração a seguir ajuda a identificar os LEDs no controlador primário. (Todos os componentes são listados para ajudar na orientação.)

   ![][4]
 
**Figura 4: LEDs de monitoramento para o controlador primário**

Use a tabela a seguir para determinar se o módulo do controlador está operando corretamente.

### LEDs indicadores do controlador  

| LED | Descrição                                                                            
|---- | ----------- |
| LED de ID (azul) | Indica se o módulo está sendo identificado. Se o LED azul estiver piscando em um controlador em execução, então o controlador é o controlador ativo e o outro é o controlador standby. Para mais informações, consulte [Identificação de um controlador ativo no seu dispositivo](https://msdn.microsoft.com/library/azure/dn790262.aspx). |
| LED de falha (âmbar) | Indica uma falha no controlador.        
| LED de OK (verde) | Um verde estável indica que o controlador está OK. Verde piscando indica um erro de configuração de VPD do controlador. |
| LEDs de atividade de SAS (verde) | Verde estável indica uma conexão sem atividade. Verde piscando indica que a conexão possui atividade em andamento. |
| LEDs de status de Ethernet | O lado direito indica atividade do link/rede: (verde estável) link ativo, (verde piscando) atividade de rede. O lado esquerdo indica a velocidade da rede: (amarelo) 1000 Mb/s, (verde) 100 Mb/s e (OFF) 10 Mb/s Dependendo do modelo do componente essa luz pode piscar mesmos se a interface de rede não estiver habilitada. |
| LEDs de POST | Indica se o andamento da inicialização quando o controlador estiver ligado. Se o dispositivo StorSimple falhar durante a inicialização, esse LED ajudará a Microsoft a identificar o ponto no processo de incialização onde ocorreu a falha. |

>[AZURE.IMPORTANT]Se o LED de falha estiver aceso, há um problema com o módulo do controlador que pode ser resolvido através da reinicialização do controlador. Entre em contato com o Suporte da Microsoft se a reinicialização do controlador não resolver esse problema.


### Monitorando LEDs para o EBOD (compartimento EBOD)  

Cada um dos controladores EBOD de SAS de 6 Gb/s possui LEDs que indicam o seu status conforme mostrado na ilustração a seguir.

  ![][5]

**Figura 5: LEDs de monitoramento - compartimento EBOD**
 
Use a tabela a seguir para determinar se o módulo do controlador EBOD está operando normalmente.

### LEDs indicadores do módulo do controlador EBOD  

|Status | Módulo de E/S OK (verde) | Falha no módulo de E/S (âmbar) | Atividade da porta do host (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Módulo do controlador OK | ON | OFF | - | | Falha no módulo do controlador | OFF | ON | - | | Nenhuma conexão externa da porta do host | - | - | OFF | | Conexão externa da porta do host – sem atividade | - | - | ON | | Conexão externa da porta do host - atividade | - | - | Piscando | | Erro de metadados do módulo do controlador | Piscando | - | - |

## LEDs indicadores do drive de disco para o compartimento primário e para o compartimento EBOD

O dispositivo StorSimple possui drives de disco localizados no compartimento primário e no compartimento EBOD. Cada drive de disco contém LEDs de indicador de monitoramento, conforme descrito nesta seção.

Para os drives de disco, o status do drive é indicado por um LED verde e um LED vermelho-âmbar montado na parte da frente de cada módulo portador do drive. A ilustração a seguir mostra estes LEDs.

  ![][6]

**Figura 6: LEDs de unidade de disco**
 
Use a tabela a seguir para determinar o estado de cada drive de disco que, por sua vez, afeta o status geral do LED do painel frontal.

### LEDs do indicador do drive de disco para o compartimento EBOD  

| Status | LED de Atividade OK (verde) | LED de falha (vermelho) | LED do painel de operações associado |
|-------|--------------------------|----------------------|-------------------------|
| Nenhum driver instalado | DESATIVADO | DESATIVADO | Nenhum |
| Driver instalado e operacional | Piscando com atividade | X | Nenhum |
| Conjunto de identidades do dispositivo Enclosure Services do SCSI (SES) | ATIVO | Piscando 1 segundo ligado/1 segundo desligado | Nenhum |
| Conjunto de bits de falha do dispositivo SES | ATIVO | ATIVO | Falha lógica (vermelha) |
| Falha no circuito de controle de energia | DESATIVADO | ATIVO | Falha no módulo (vermelho) |

## Alarmes audíveis  

Um dispositivo StorSimple contém alarmes audíveis associados com o compartimento primário e com o compartimento EBOD. Um alarme audível é localizando no painel frontal (também chamado de painel de operações) de ambos os compartimentos. O alarme audível indica quando uma condição de falha está presente. As condições a seguir ativarão o alarme:

- Falha do ventilador
- Tensão fora dos limites
- Temperatura acima ou abaixo dos limites
- Saturação térmica
- Falha no sistema
- Falha lógica
- Falha no fornecimento de energia
- Remoção de um módulo de refrigeração de energia (PCM)  

A tabela a seguir descreve os vários estados de alarme.

### Estados de alarme.  

| Estado de alarme | Ação | Ação com o botão de mudo pressionado |
|------------|---------|---------------------------------|
| S0 | Modo normal: silencioso | Dois bipes |
| S1 | Modo de falha: 1 segundo ligado/1 segundo desligado | Transição para S2 ou S3 (consulte as observações) |
| S2 | Modo lembrete: bipe intermitente | Nenhum |
| S3 | Modo mudo: silencioso | Nenhum |
| S4 | Modo de falha crítica: alarme contínuo | Não disponível: mudo não ativo |

> [AZURE.NOTE]

>  - No estado de alarme S1, se você não apertar mudo em até 2 minutos, o estado faz uma transição automática para S2 ou S3.  
>  - Os estados de alarme S1 a S4 retornam para S0 após a condição de falha ter sido resolvida.  
>  - O estado de falha crítica S4 pode ser inserido a partir de qualquer outro estado.  

Você pode silenciar o alarme audível apertando o botão de mudo no painel de operações. Um silenciament automático ocorrerá após dois minutos se o botão de mudo não for manualmente acionado. Quando o alarme estiver mudo, ele continuará a soar através de bipes curtos e intermitentes para indicar que ainda existe um problema. O alarme será silenciado quando todos os problemas forem resolvidos.

A tabela a seguir descreve as várias condições de alarme.

### Condições de alarme.  

| Status | Severidade | Alarme | LED do painel de operações |
|--------|---------|--------|----------------|
| Alerta de PCM – perda de energia de CC de um único PCM | Falha – nenhuma perda de redundância | S1 | Falha do módulo|
|Alerta de PCM – perda de energia de CC de um único PCM | Falha – perda de redundância | S1 | Falha do módulo |
| Falha do ventilador do PCM | Falha – perda de redundância | S1 | Falha do módulo |
| Falha do PCM detectada no módulo do SBB | Falha | S1 | Falha do módulo |
| PCM removido | Erro de configuração | Nenhum | Falha do módulo |
| Erro de configuração do compartimento | Falha – crítica | S1 | Falha do módulo |
| Alerta aviso de temperatura baixa | Aviso | S1 | Falha do módulo |
| Alerta aviso de temperatura alta | Aviso | S1 | Falha do módulo |
| Alarme de sobretemperatura | Falha – crítica | S1 | Falha do módulo |
| Falha do barramento I2C | Falha – perda de redundância | S1 | Falha do módulo |
| Erro de comunicação do painel de operações (I2C) | Falha – crítica | S1 | Falha do módulo |
| Erro no controlador | Falha – crítica | S1 | Falha do módulo |
| Falha do módulo da interface SBB | Falha – crítica | S1 | Falha do módulo |
| Falha no módulo da interface SBB – Nenhum módulo em funcionamento restante | Falha – crítica | S4 | Falha do módulo |
| Módulo da interface SBB removido | Aviso | Nenhum | Falha do módulo |
| Falha de controle de energia do drive | Aviso – nenhuma perda de energia no drive | S1 | Falha do módulo |
| Falha de controle de energia do drive | Falha – crítica; perda de energia do drive | S1 | Falha do módulo |
| Drive removido | Aviso | Nenhum | Falha do módulo |
| Energia insuficiente disponível | Aviso | nenhum | Falha do módulo |

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 

<!---HONumber=July15_HO4-->