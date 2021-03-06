---
title: Gerar certificados de infraestrutura de chave pública do Azure Stack para implantação de sistemas integrados do Azure Stack | Microsoft Docs
description: Descreve o processo de implantação de certificado PKI de pilha do Azure para sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 698e044aea6bbd78847cb209160c1fa6b2edcdbf
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023412"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Geração de solicitação de assinatura de certificados de pilha do Azure

A ferramenta de verificador de preparação do Azure Stack descrita neste artigo está disponível [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker). A ferramenta cria solicitações de assinatura de certificado (SAC) adequado para uma implantação do Azure Stack. Certificados devem ser solicitados, gerados e validados com tempo suficiente para testar antes da implantação.

A ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) realiza as seguintes solicitações de certificado:

 - **Solicitações de certificado padrão**  
    Solicitação de acordo com a [gerar certificados de PKI para implantação do Azure Stack](azure-stack-get-pki-certs.md).
 - **Plataforma como serviço**  
    Solicitar, opcionalmente, os nomes do plataforma como serviço (PaaS) aos certificados conforme especificado na [requisitos de certificado de infra-estrutura de chave pública do Azure Stack - certificados de PaaS opcionais](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Pré-requisitos

O sistema deve atender aos seguintes pré-requisitos antes de gerar o CSR(s) para certificados PKI para uma implantação do Azure Stack:

 - Verificador de preparação do Microsoft Azure Stack
 - Atributos de certificado:
    - Nome da região
    - Nome de domínio totalmente qualificado (FQDN) externo
    - Assunto
 - Windows 10 ou Windows Server 2016
 
  > [!NOTE]
  > Quando você receber seus certificados de volta da autoridade de certificação as etapas em [certificados PKI de pilha do Azure preparar](azure-stack-prepare-pki-certs.md) precisará ser concluída no mesmo sistema!

## <a name="generate-certificate-signing-requests"></a>Gerar certificado de assinatura de solicitação (ões)

Use estas etapas para preparar e validar os certificados PKI de pilha do Azure: 

1.  Instale AzsReadinessChecker em um prompt do PowerShell (5.1 ou superior), executando o seguinte cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Declare a **assunto** como um dicionário ordenado. Por exemplo:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Se for fornecido um nome comum (CN) será substituído pelo primeiro nome DNS da solicitação de certificado.

3.  Declare um diretório de saída que já existe. Por exemplo: 

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Declarar identificar sistema

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Serviços de Federação do Active Directory (AD FS)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Declarar **nome da região** e uma **FQDN externo** destinado para a implantação do Azure Stack.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` constitui a base na qual todos os nomes DNS externos no Azure Stack são criados, neste exemplo, o portal seria `portal.east.azurestack.contoso.com`.  

6. Para gerar solicitações para cada nome DNS de assinatura de certificado:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Para incluir serviços de PaaS especificar a opção ```-IncludePaaS```

7. Como alternativa, para ambientes de desenvolvimento/teste. Para gerar uma solicitação de certificado único com vários nomes alternativos de entidade, adicione **- RequestType SingleCSR** parâmetro e valor (**não** recomendado para ambientes de produção):

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Para incluir serviços de PaaS especificar a opção ```-IncludePaaS```
    
8. Examine a saída:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Enviar o **. REQ** arquivo gerado para a sua autoridade de certificação (interna ou pública).  O diretório de saída de **AzsReadinessChecker início** contém o CSR(s) necessárias ao envio de uma autoridade de certificação.  Ele também contém um diretório filho que contém o arquivo INF (s) usada durante a geração de solicitação de certificado, como uma referência. Certifique-se de que sua autoridade de certificação gera certificados usando sua solicitação gerada que atendem a [requisitos de PKI do Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Próximas etapas

[Preparar certificados PKI de pilha do Azure](azure-stack-prepare-pki-certs.md)
