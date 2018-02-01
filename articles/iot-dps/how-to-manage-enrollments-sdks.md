---
title: "Gerenciar registros de dispositivo com SDKs de Serviço de Provisionamento de Dispositivos do Azure | Microsoft Docs"
description: "Como gerenciar registros de dispositivo no Serviço de Provisionamento de Dispositivos no Hub IoT com os SDKs de Serviço"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 14e353af82342bc7a580e1a0a02b8b4e29514fb9
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Como gerenciar registros de dispositivo com SDKs do Serviço de Provisionamento de Dispositivos do Azure
Um *registro de dispositivo* cria um registro de um único dispositivo ou um grupo de dispositivos que pode em algum momento registrar com o Serviço de Provisionamento de Dispositivos. O registro contém a configuração inicial desejada para os dispositivos como parte desse registro, incluindo o Hub IoT desejado. Este artigo mostra como gerenciar registros de dispositivo para seu serviço de provisionamento programaticamente usando os SDKs de Serviço de Provisionamento do Azure IoT.  Os SDKs do estão disponíveis no GitHub no mesmo repositório dos SDKs do Azure IoT.

## <a name="samples"></a>Exemplos
Este artigo examina os conceitos de alto nível para gerenciar registros de dispositivo para seu serviço de provisionamento programaticamente usando os SDKs de Serviço de Provisionamento do Azure IoT.  Chamadas à API exatas podem ser diferentes devido a diferenças de linguagem.  Examine os exemplos fornecidos no GitHub para obter detalhes:
* [Exemplos de cliente de serviço de provisionamento do Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [Exemplos de cliente de serviço de provisionamento do Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
* [Exemplos de cliente de serviço de provisionamento do .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>pré-requisitos
* Cadeia de conexão de uma instância do Serviço de Provisionamento de Dispositivos
* Artefatos de segurança do dispositivo:
    * [**TPM**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Registro individual: ID do Registro e Chave de Endosso de TPM de um dispositivo físico ou do Simulador de TPM.
        * O grupo de registros não é aplicável ao atestado de TPM.
    * [**X.509**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Registro individual: o [Certificado de folha](https://docs.microsoft.com/azure/iot-dps/concepts-security#leaf-certificate) do dispositivo físico ou do Emulador DICE.
        * Grupo de registros: o [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermediário](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), usado para gerar o certificado do dispositivo em um dispositivo físico.  Ele também pode ser gerado no Emulador DICE.

## <a name="create-a-device-enrollment"></a>Criar um registro do dispositivo

Há duas maneiras em que você pode registrar seus dispositivos com o serviço de provisionamento:

* Um **Grupo de registros** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum de certificados X.509, assinados pelo [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou pelo [certificado intermediário](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário. Observe que você só pode registrar dispositivos que usam o mecanismo de atestado X.509 como *grupos de registro*. 

    Você pode criar um grupo de registro com os SDKs seguindo este fluxo de trabalho:

    1. Para o grupo de registros, o mecanismo de atestado usa o certificado raiz X.509.  Chame a API do SDK de Serviço ```X509Attestation.createFromRootCertificate``` com o certificado raiz para criar o atestado para o registro.  O certificado raiz X.509 é fornecido em um arquivo PEM ou como uma cadeia de caracteres.
    1. Crie uma nova variável ```EnrollmentGroup``` usando o ```attestation``` criado e um ```enrollmentGroupId``` exclusivo.  Opcionalmente, você pode definir parâmetros como ```Device ID```, ```IoTHubHostName``` e ```ProvisioningStatus```.
    2. Chame a API do SDK de Serviço ```createOrUpdateEnrollmentGroup``` em seu aplicativo de back-end com ```EnrollmentGroup``` para criar um grupo de registros.

* Um **registro Individual** é uma entrada para um único dispositivo que pode registrar. Inscrições individuais podem usar tokens de certificados X.509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual como o mecanismo de Atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

    Você pode criar um registro individual com os SDKs seguindo este fluxo de trabalho:
    
    1. Escolha seu mecanismo ```attestation```, que pode ser TPM ou X.509.
        1. **TPM**: usando a Chave de Endosso de um dispositivo físico ou do Simulador de TPM como a entrada, você pode chamar a API do SDK de Serviço ```TpmAttestation``` para criar o atestado para o registro. 
        2. **X.509**: usando o certificado de cliente como a entrada, você pode chamar a API do SDK de Serviço ```X509Attestation.createFromClientCertificate``` para criar o atestado para registro.
    2. Crie uma nova variável ```IndividualEnrollment``` usando o ```attestation``` criado e um ```registrationId``` exclusivo como entrada, que está em seu dispositivo ou gerado do Simulador de TPM.  Opcionalmente, você pode definir parâmetros como ```Device ID```, ```IoTHubHostName``` e ```ProvisioningStatus```.
    3. Chame a API do SDK de Serviço ```createOrUpdateIndividualEnrollment``` em seu aplicativo de back-end com ```IndividualEnrollment``` para criar um registro individual.

Depois que um registro foi criado com êxito, o Serviço de Provisionamento do Dispositivo deve retornar um resultado de registro.

Este fluxo de trabalho é demonstrado no [exemplos](#samples).

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro

Depois de criar uma entrada de registro, convém atualizar o registro.  Os cenários possíveis incluem a atualização da propriedade desejada, a atualização do método de atestado ou a revogação do acesso do dispositivo.  Há diferentes APIs para o registro individual e para o registro de grupo, mas não um mecanismo de atestação distinto.

Você pode atualizar uma entrada de registro seguindo este fluxo de trabalho:
* **Registro individual**:
    1. Obtenha o registro mais recente do serviço de provisionamento primeiro com a API do SDK de Serviço ```getIndividualEnrollment```.
    2. Modifique o parâmetro de registro mais recente, conforme necessário. 
    3. Usando o registro mais recente, chame a API do SDK de Serviço ```createOrUpdateIndividualEnrollment``` para atualizar a entrada de registro.
* **Registro de grupo**:
    1. Obtenha o registro mais recente do serviço de provisionamento primeiro com a API do SDK de Serviço ```getEnrollmentGroup```.
    2. Modifique o parâmetro de registro mais recente, conforme necessário.
    3. Usando o registro mais recente, chame a API do SDK de Serviço ```createOrUpdateEnrollmentGroup``` para atualizar a entrada de registro.

Este fluxo de trabalho é demonstrado no [exemplos](#samples).

## <a name="remove-an-enrollment-entry"></a>Remover uma entrada de registro

* O **registro individual** pode ser excluído chamando a API do SDK de Serviço ```deleteIndividualEnrollment``` usando ```registrationId```.
* O **registro de grupo** pode ser excluído chamando a API do SDK de Serviço ```deleteEnrollmentGroup``` usando ```enrollmentGroupId```.

Este fluxo de trabalho é demonstrado no [exemplos](#samples).

## <a name="bulk-operation-on-individual-enrollments"></a>Operação em massa em registros individuais

Você pode executar a operação em massa para criar, atualizar ou remover vários registros individuais seguindo este fluxo de trabalho:

1. Crie uma variável que contém vários ```IndividualEnrollment```.  A implementação dessa varável é diferente para cada linguagem.  Examine o exemplo de operação em massa no GitHub para obter detalhes.
2. Chame a API do SDK de Serviço ```runBulkOperation``` com um ```BulkOperationMode``` para a operação desejada e sua variável para registros individuais. Quatro modos são compatíveis: criar, atualizar, updateIfMatchEtag e excluir.

Depois de executar uma operação com êxito, o Serviço de Provisionamento de Dispositivos retornará o resultado de uma operação em massa.

Este fluxo de trabalho é demonstrado no [exemplos](#samples).
