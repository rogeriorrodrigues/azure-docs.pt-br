---
title: Encaminhamento de syslog de pilha do Azure
description: Saiba como integrar o Azure Stack com soluções de monitoramento usando o encaminhamento de syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "42139349"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integração do datacenter do Azure Stack - encaminhamento de syslog

Este artigo mostra como usar o syslog para integrar a infraestrutura do Azure Stack com soluções de segurança externa já implantadas em seu datacenter. Por exemplo, um sistema de gerenciamento de eventos de informações de segurança (SIEM). O canal de syslog expõe auditorias, alertas e logs de segurança de todos os componentes da infraestrutura do Azure Stack. Usar o encaminhamento de syslog para integrar soluções de monitoramento de segurança e/ou para recuperar todas as auditorias, alertas e segurança logs para armazená-los para a retenção. 

Começando com a atualização 1805, o Azure Stack tem um cliente integrado de syslog que, uma vez configurado, emite mensagens de syslog com a carga no formato de evento comum (CEF). 

> [!IMPORTANT]
> Encaminhamento de syslog está em visualização. Ele não deve ser usado em ambientes de produção. 

O diagrama a seguir mostra os principais componentes que participam na integração do syslog.

![Diagrama de encaminhamento de syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Configuração do encaminhamento de syslog

O cliente de syslog no Azure Stack oferece suporte as seguintes configurações:

1. **Syslog sobre TCP, com a autenticação mútua (cliente e servidor) e criptografia de TLS 1.2:** nessa configuração, o servidor syslog e o cliente de syslog podem verificar a identidade de uns aos outros por meio de certificados. As mensagens são enviadas por um canal criptografado do TLS 1.2.

2. **Syslog sobre TCP com autenticação de servidor e criptografia de TLS 1.2:** nessa configuração, o cliente de syslog pode verificar a identidade do servidor syslog por meio de um certificado. As mensagens são enviadas por um canal criptografado do TLS 1.2.

3. **Syslog sobre TCP, sem criptografia:** nessa configuração, nem o syslog cliente nem o servidor syslog verifica a identidade do outro. As mensagens são enviadas em texto não criptografado por meio de TCP.

4. **Syslog através de UDP, sem criptografia:** nessa configuração, nem o syslog cliente nem o servidor syslog verifica a identidade do outro. As mensagens são enviadas em texto não criptografado via UDP.

> [!IMPORTANT]
> A Microsoft recomenda usar o TCP usando a autenticação e criptografia (configuração #1 ou, em que o mínimo, #2) para ambientes de produção proteger contra ataques man-in-the-middle e interceptação de mensagens.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdlets para configurar o encaminhamento de syslog
Configuração do encaminhamento de syslog requer acesso ao ponto de extremidade com privilégios (PEP). Foram adicionados dois cmdlets do PowerShell para o PEP para configurar o encaminhamento de syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parâmetros de cmdlets

Parâmetros para *SyslogServer conjunto* cmdlet:

| Parâmetro | DESCRIÇÃO | Tipo |
|---------|---------| ---------|
| *ServerName* | Endereço IP ou FQDN do servidor syslog | Cadeia de caracteres |
|*NoEncryption*| Forçar o cliente a enviar mensagens do syslog em texto não criptografado | Sinalizador | 
|*SkipCertificateCheck*| Ignorar a validação do certificado fornecida pelo servidor syslog durante o handshake TLS inicial | Sinalizador |
|*SkipCNCheck*| Ignorar a validação do valor de nome comum do certificado fornecido pelo servidor syslog durante o handshake TLS inicial | Sinalizador |
|*UseUDP*| Usar o syslog com UDP como protocolo de transporte |Sinalizador |
|*Remover*| Remover a configuração do servidor do cliente e interromper o encaminhamento de syslog| Sinalizador |

Parâmetros para *SyslogClient conjunto* cmdlet:
| Parâmetro | DESCRIÇÃO | Tipo |
|---------|---------| ---------|
| *pfxBinary* | arquivo PFX que contém o certificado a ser usado pelo cliente como a identidade para autenticar o servidor syslog  | Byte[] |
| *CertPassword* |  Senha para importar a chave privada que está associada com o arquivo pfx | SecureString |
|*RemoveCertificate* | Remover o certificado do cliente | Sinalizador|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configuração do encaminhamento de syslog com TCP, a autenticação mútua e criptografia de TLS 1.2

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, com a criptografia de TLS 1.2. Durante o handshake inicial, o cliente verificará que o servidor fornece um certificado válido e confiável; da mesma forma, o cliente também fornece um certificado para o servidor como prova de sua identidade. Essa configuração é o mais seguro como ele fornece uma validação completa da identidade do cliente e o servidor e envia mensagens em um canal criptografado. 

> [!IMPORTANT]
> A Microsoft recomenda fortemente para usar essa configuração em ambientes de produção. 

Para configurar o encaminhamento de syslog com TCP, a autenticação mútua e criptografia de TLS 1.2, execute os dois estes cmdlets:
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
O certificado do cliente deve ter a mesma raiz daquele fornecido durante a implantação do Azure Stack. Ele também deve conter uma chave privada.

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configuração do encaminhamento de syslog com TCP, autenticação de servidor e criptografia de TLS 1.2

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, com a criptografia de TLS 1.2. Durante o handshake inicial, o cliente também verifica que o servidor fornece um certificado válido e confiável. Isso impede que o cliente para enviar mensagens para destinos não confiáveis.
Usando a autenticação e criptografia de TCP é a configuração padrão e representa o nível mínimo de segurança que a Microsoft recomenda para um ambiente de produção. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

Caso você deseje testar a integração do seu servidor syslog com o cliente do Azure Stack, usando um certificado autoassinado e/ou não confiável, você pode usar esses sinalizadores para ignorar a validação executada pelo cliente durante o handshake inicial do servidor.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Microsoft não recomenda o uso do sinalizador - SkipCertificateCheck para ambientes de produção. 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configurando o encaminhamento de syslog com TCP e nenhuma criptografia

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog sobre TCP, sem criptografia. O cliente verifica a identidade do servidor nem fornece sua própria identidade para o servidor para verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> A Microsoft recomenda não usar essa configuração para ambientes de produção. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Configurando o encaminhamento de syslog com UDP e nenhuma criptografia

Nessa configuração, o cliente do syslog no Azure Stack encaminha as mensagens para o servidor syslog através de UDP, sem criptografia. O cliente verifica a identidade do servidor nem fornece sua própria identidade para o servidor para verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
Embora UDP sem criptografia seja mais fácil de configurar, ele não fornece nenhuma proteção contra ataques man-in-the-middle e interceptação de mensagens. 

> [!IMPORTANT]
> A Microsoft recomenda não usar essa configuração para ambientes de produção. 


## <a name="removing-syslog-forwarding-configuration"></a>Removendo a configuração de encaminhamento do syslog

Para remover a configuração do servidor syslog completamente e interromper o encaminhamento syslog:

**Remover a configuração do servidor syslog do cliente**

```PowerShell  
Set-SyslogServer -Remove
```

**Remover o certificado do cliente do cliente**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Verificando a configuração de syslog

Se você conectou com êxito o cliente de syslog para o servidor syslog, você deve começar assim que receber eventos. Se você não vir quaisquer eventos, verifique se a configuração do seu cliente de syslog, executando os seguintes cmdlets:

**Verifique se a configuração do servidor no cliente de syslog**

```PowerShell  
Get-SyslogServer
```

**Verifique se a configuração de certificado no cliente de syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Esquema de mensagem do syslog

O encaminhamento de syslog da infraestrutura do Azure Stack envia mensagens formatadas em comum (CEF) do formato de evento.
Cada mensagem do syslog é estruturada com base nesse esquema: 

```Syslog
<Time> <Host> <CEF payload>
```

O conteúdo CEF é baseado na estrutura abaixo, mas o mapeamento para cada campo varia dependendo do tipo de mensagem (eventos do Windows, alerta criado, alerta fechada).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>Mapeamento de CEF para eventos do Windows
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela de gravidade para eventos do Windows: 
| Valor de severidade CEF | Nível de evento do Windows | Valor numérico |
|--------------------|---------------------| ----------------|
|0|Indefinido|Valor: 0. Indica os logs em todos os níveis|
|10|Crítico|Valor: 1. Indica os logs para um alerta crítico|
|8|Erro| Valor: 2. Indica os logs de erro|
|5|Aviso|Valor: 3. Indica os logs para um aviso|
|2|Informações|Valor: 4. Indica os logs para uma mensagem informativa|
|0|Detalhado|Valor: 5. Indica os logs em todos os níveis|

Tabela de extensão personalizada para eventos do Windows no Azure Stack:
| Nome de extensão personalizada | Exemplo de evento do Windows | 
|-----------------------|---------|
|MasChannel | Sistema|
|MasComputer | Test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| As configurações de diretiva de grupo do usuário foram processadas com êxito. Não houve nenhuma alteração detectada desde o último processamento da diretiva de grupo.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Êxito na auditoria|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |informações|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<SID do Windows\> |
|MasTask |0|
|MasTaskCategory| Criação de processo|
|MasUserData|KB4093112!! 5112!! Instalado!! 0x0!! WindowsUpdateAgent Xpath: /Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapeamento de CEF para os alertas criados
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
Tabela de gravidade de alertas:
| Severity | Nível |
|----------|-------|
|0|Indefinido|
|10|Crítico|
|5|Aviso|

Tabela de extensão personalizada para os alertas criados no Azure Stack:
| Nome de extensão personalizada | Exemplo | 
|-----------------------|---------|
|MasEventDescription|Descrição: Uma conta de usuário do \<TestUser\> foi criado para \<TestDomain\>. É um risco de segurança. – CORREÇÃO: entre em contato com o suporte. Assistência ao cliente é necessária para resolver esse problema. Não tente resolver esse problema sem sua assistência. Antes de abrir uma solicitação de suporte, iniciar o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mapeamento de CEF para alertas fechados
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

O exemplo a seguir mostra uma mensagem de syslog com carga CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>Próximas etapas

[Política de manutenção](azure-stack-servicing-policy.md)