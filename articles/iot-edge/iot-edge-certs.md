---
title: Entenda os certificados do Azure IoT Edge | Microsoft Docs
description: Aprenda sobre os certificados do Azure IoT Edge e como eles são usados.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ff85f2c9ef708c7cbac9be4933541f063a6c95b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568583"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Detalhes do uso do certificado do Azure IoT Edge

Os certificados IoT Edge são usados para os módulos e dispositivos de IoT downstream para verificar a identidade e a legitimidade do módulo de tempo de execução do [Edge Hub](iot-edge-runtime.md#iot-edge-hub) ao qual eles estão se conectando. Essas verificações permitem uma conexão segura TLS (segurança da camada de transporte) entre o tempo de execução, os módulos e os dispositivos IoT. Como o Hub IoT, o IoT Edge requer uma conexão segura e criptografada a partir de dispositivos IoT downstream (ou folha) e módulos IoT Edge. Para estabelecer uma conexão TLS segura, o módulo Edge Hub apresenta uma cadeia de certificados de servidor para conectar clientes para que eles verifiquem sua identidade.

Para esclarecer qualquer confusão sobre o uso de certificados da IoT Edge, este artigo explica como os certificados da IoT Edge podem funcionar em cenários em escala, bem como em cenários de desenvolvimento e teste. Enquanto os scripts são diferentes (Powershell vs. bash), os conceitos são os mesmos entre o Linux e o Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Na maioria dos casos, os fabricantes são entidades independentes dos usuários finais de um dispositivo Edge. Às vezes, o único relacionamento entre o fabricante e o operador é a compra de um dispositivo Edge comercialmente disponível. Outras vezes, o fabricante trabalha sob contrato para construir um dispositivo Edge em nome do operador. O design do certificado IoT Edge tenta levar em conta os dois cenários.

A figura a seguir ilustra o uso de certificados da IoT Edge. Pode haver zero, um ou muitos certificados de assinatura intermediários entre o certificado da CA raiz e o certificado da CA do dispositivo. Dependendo do número de entidades envolvidas, aqui mostramos um caso.

![Diagrama de relacionamentos típicos de certificados](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificação ou 'CA' para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificação atua como um terceiro confiável entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de confiança funciona inicialmente emitindo um certificado raiz, que é a base da confiança em todos os certificados emitidos pela autoridade. Depois disso, o proprietário pode usar o certificado raiz para emitir certificados intermediários adicionais (certificados 'leaf').

### <a name="root-ca-certificate"></a>Certificado de AC raiz

Um certificado CA raiz é a raiz da confiança de todo o processo. Em cenários de produção, esse geralmente será um certificado CA adquirido de uma autoridade de certificação comercial confiável, como Baltimore, Verisign, DigiCert etc. Se você tiver controle total sobre os dispositivos que se conectam ao (s) seu (s) dispositivo (s) Edge, é possível usar uma autoridade de certificação de nível corporativo. Em qualquer um dos casos, toda a cadeia de certificados do Edge Hub é acumulada até ela, portanto, os dispositivos de folha de IoT devem confiar no certificado raiz. Você pode armazenar o certificado de CA raiz no armazenamento de autoridade de certificação raiz confiável ou fornecer os detalhes do certificado de CA raiz em seu código quando o cliente de IoT se conecta ao IoT Edge.

### <a name="intermediate-certificates"></a>Certificados intermediários

Em um processo de fabricação típico para criar dispositivos seguros, os certificados de CA raiz raramente são usados diretamente, principalmente devido ao risco de vazamento ou exposição. Geralmente, um ou mais certificados CA intermediários são criados e assinados digitalmente pelo certificado CA raiz. Pode haver apenas um, ou pode haver uma cadeia desses certificados intermediários. Cenários que exigem uma cadeia de certificados intermediários incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente comprando uma CA raiz e obtendo um certificado de assinatura para o fabricante assinar os dispositivos que eles fazem em nome desse cliente.

Em qualquer caso, o fabricante usa um certificado CA intermediário no final dessa cadeia para assinar o certificado CA do dispositivo colocado no dispositivo final. Geralmente, esses certificados intermediários são guardados de perto na fábrica. Eles passam por processos rigorosos, tanto físicos quanto eletrônicos, para seu uso.

### <a name="device-ca-certificate"></a>Certificado de autoridade de certificação de dispositivo

O certificado de CA do dispositivo é gerado e assinado pelo certificado de CA intermediário final no processo. Esse certificado é instalado no próprio dispositivo Edge, preferencialmente em armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado CA de dispositivo identifica exclusivamente um dispositivo IoT Edge. Para o IoT Edge, o certificado CA do dispositivo é capaz de emitir outros certificados. Por exemplo, o certificado de CA do dispositivo emite certificados de dispositivo de folha que são usados para autenticar dispositivos no [Serviço de Provisionamento de Dispositivo do IoT do Azure](..\iot-dps\about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Carga de trabalho do IoT Edge da autoridade de certificação

Esse certificado, o primeiro no lado "operador" do processo, é gerado pelo [IoT Edge Security Manager](iot-edge-security-manager.md) quando o IoT Edge é iniciado pela primeira vez. Este certificado é gerado e assinado pelo "certificado da CA do dispositivo". Esse certificado, que é apenas outro certificado de assinatura intermediário, é usado para gerar e assinar quaisquer outros certificados usados pelo tempo de execução do IoT Edge. Atualmente, trata-se principalmente do certificado de servidor do Edge Hub discutido na seção a seguir, mas no futuro pode incluir outros certificados para autenticar os componentes do IoT Edge.

### <a name="edge-hub-server-certificate"></a>Certificado de servidor de Hub do Edge

O certificado do servidor Edge Hub é o certificado real apresentado para dispositivos e módulos de folha para verificação de identidade durante o estabelecimento da conexão TLS exigida pelo IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura usados para gerá-lo até o certificado de CA raiz, ao qual o dispositivo de IoT de folha deve confiar. Quando gerado pelo IoT Edge Security Manager, o nome comum (CN), desse certificado do Edge Hub, é definido com o nome fornecido pela propriedade 'hostname' no arquivo config.yaml após a conversão para letras minúsculas. Essa é uma fonte comum de confusão com o IoT Edge.

## <a name="production-implications"></a>Implicações de produção

Uma pergunta razoável pode ser "por que a IoT Edge precisa do certificado extra de 'CA de carga de trabalho'? Não foi possível usar o certificado da CA do dispositivo para gerar diretamente o certificado do servidor do Edge Hub? " Tecnicamente, era possível. No entanto, o objetivo deste certificado intermediário de "carga de trabalho" é separar as preocupações entre o fabricante do dispositivo e o operador do dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Você provavelmente desejaria que o certificado CA do dispositivo fornecido pelo fabricante fosse imutável. No entanto, os certificados de "carga de trabalho" específicos da operação do dispositivo devem ser limpos e recriados para a nova implantação.

Como os processos do fabricante e do operador são separados, há algumas implicações que precisam ser consideradas relacionadas à implantação do IoT Edge para dispositivos de produção.

* Com qualquer processo baseado em certificado, o certificado de CA raiz e todos os certificados de CA intermediários devem ser protegidos e monitorados durante todo o processo de implantação de um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter processos robustos para armazenamento e uso adequados de seus certificados intermediários. Além disso, o certificado de CA do dispositivo deve ser mantido o mais seguro possível no próprio dispositivo, de preferência um módulo de segurança de hardware.

* Como o certificado de servidor do Edge Hub será apresentado pelo Edge Hub aos dispositivos e módulos do cliente de conexão, o CN do certificado da CA do dispositivo **não deve ser** o mesmo que o "hostname" que será usado na configuração. yaml no dispositivo IoT Edge. O nome usado pelos clientes para se conectar ao IoT Edge (por exemplo, por meio do parâmetro GatewayHostName da cadeia de conexão ou do comando CONECTAR no MQTT) **não pode ser** o mesmo nome comum usado no certificado CA do dispositivo. Isso ocorre porque o Edge Hub apresenta toda a sua cadeia de certificados para verificação pelos clientes. Se o certificado do servidor do Edge Hub e o certificado da CA do dispositivo tiverem o mesmo CN, você entrará em um loop de verificação e o certificado será invalidado.

* Como o certificado da CA do dispositivo é usado pelo daemon de IoT Edge Security para gerar os certificados finais da IoT Edge, ele deve ser um certificado de assinatura, o que significa que ele possui recursos de assinatura de certificado. Para tornar isso um certificado de CA de assinatura, aplicar "CA Basic: CA: True" às solicitações da V3 Basic ao certificado CA do dispositivo configurará automaticamente as propriedades de uso de chave necessárias.

>[!Tip]
> Se você já passou pela configuração do IoT Edge como um gateway transparente em um cenário de desenvolvimento / teste usando nossos "scripts de conveniência" (consulte a próxima seção) e usou o mesmo nome de host ao criar o certificado CA do dispositivo como fez para o hostname em config.yaml, você pode estar se perguntando por que funcionou. Em um esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência acrescentam um ".ca" ao final do nome que você passa para o script. Portanto, por exemplo, se você usou "mygateway" para o nome do seu dispositivo nos scripts e no nome do host em config.yaml, o primeiro será transformado em mygateway.ca antes de ser usado como CN para o certificado da CA do dispositivo.

## <a name="devtest-implications"></a>Implicações de desenvolvimento/teste

Para facilitar o desenvolvimento e os cenários de teste, a Microsoft fornece um conjunto de [scripts de conveniência](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) para gerar certificados de não produção adequados para o IoT Edge no cenário de gateway transparente. Para obter exemplos de como funcionam os scripts, consulte [configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).

Esses scripts geram certificados que seguem a estrutura da cadeia de certificados explicada neste artigo. Os comandos a seguir geram o certificado de autoridade de certificação"raiz" e um único "intermediários da autoridade de certificação certificate".

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Da mesma forma, esses comandos geram o certificado de autoridade de certificação"dispositivo".

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* O **\<nome do dispositivo de gateway\>** passado para esses scripts **não deve** ser o mesmo que o parâmetro "hostname" no config.yaml. Como mencionado, os scripts ajudam a evitar qualquer problema anexando uma cadeia ".ca" ao **\<nome do dispositivo de gateway\>** para evitar a colisão de nomes no caso de um usuário configurar o Edge usando o mesmo nome em ambos os lugares, no entanto, é uma boa prática evitar usar o mesmo nome.

>[!Tip]
> Para conectar seus dispositivos e aplicativos "folha" de IoT do dispositivo que usam nosso SDK de dispositivo de IoT por meio do IoT Edge, você deve adicionar o parâmetro GatewayHostName opcional ao final da string de conexão do dispositivo. Quando o Certificado de Servidor do Edge Hub é gerado, ele é baseado em uma versão com menos letras do nome do host de config.yaml, portanto, para que os nomes correspondam e a verificação de certificado TLS seja bem-sucedida, insira o parâmetro GatewayHostName em minúsculas.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo de hierarquia de certificados do IoT Edge

Para ilustrar um exemplo desse caminho de certificado, a captura de tela a seguir é de um dispositivo IoT Edge de trabalho configurado como um gateway transparente. O OpenSSL é usado para conectar ao Edge Hub, validar e despejar os certificados.

![Captura de tela da hierarquia de certificados em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Você pode ver a hierarquia da profundidade do certificado representada na captura de tela:

| Certificado de AC raiz         | Teste de Cert de autoridade de certificação do Hub IoT do Azure apenas                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado de autoridade de certificação intermediária | Somente o teste do certificado intermediário Hub IoT do Azure                                                                 |
| Certificado de autoridade de certificação de dispositivo       | iotgateway.ca ("iotgateway" foi passado como o <nome do host do gateway> para os scripts de conveniência)      |
| Certificado de CA da carga de trabalho     | carga de trabalho iotedge ca                                                                                       |
| Certificado de servidor de Hub do Edge | iotedgegw.local (corresponde a 'hostname' do config. YAML)                                                |

## <a name="next-steps"></a>Próximas etapas

[Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)