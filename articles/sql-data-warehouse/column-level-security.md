---
title: Segurança de nível de coluna do SQL Data Warehouse do Azure | Microsoft Docs
description: Segurança de Nível de Coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de banco de dados com base no contexto de execução do usuário ou sua participação no grupo. A CLS simplifica o design e codificação de segurança em seu aplicativo. A CLS permite implementar restrições de acesso à linha de dados.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 5a916132f705f3c517ee6789b61a3972b2445b62
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939690"
---
# <a name="column-level-security"></a>Segurança de Nível da Coluna 
Segurança de Nível de Coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de banco de dados com base no contexto de execução do usuário ou sua participação no grupo.  

A CLS simplifica o design e codificação de segurança em seu aplicativo. A CLS permite implementar restrições no acesso da coluna para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas certas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso está localizada na camada do banco de dados, em vez de estar longe dos dados em outra camada de aplicativo. O banco de dados aplica as restrições de acesso sempre que houver uma tentativa de acessar esses dados em qualquer camada. Isso torna o sistema de segurança mais robusto e confiável, reduzindo a área de superfície do sistema de segurança. Além disso, isso também elimina a necessidade de introduzir modos de exibição para filtrar colunas para impor restrições de acesso em usuários. 

Você pode implementar CLS com a instrução T-SQL [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql). Com este mecanismo, há suporte para autenticação SQL e o Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintaxe 

```sql
GRANT <permission> [ ,...n ] ON    
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]   
    TO <database_principal> [ ,...n ]    
    [ WITH GRANT OPTION ]   
    [ AS <database_principal> ]   
<permission> ::=   
    SELECT 
  | UPDATE  
<database_principal> ::=    
      Database_user    
    | Database_role    
    | Database_user_mapped_to_Windows_User    
    | Database_user_mapped_to_Windows_Group    
```

## <a name="example"></a>Exemplo 
O exemplo a seguir mostra como restringir 'TestUser' de acessar a coluna 'SSN' da tabela 'Membership': 

Crie tabela de 'Membership' com coluna SSN usada para armazenar números de previdência social:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Permitir 'TestUser' para acessar todas as colunas, exceto a coluna SSN com dados confidenciais: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Consultas executadas como 'TestUser' irão falhar se elas incluírem a coluna SSN:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Casos de uso
Alguns exemplos de como CLS está sendo usado atualmente: 
- Uma empresa de serviços financeiros permite que a conta somente os gerentes tenham acesso aos números de previdência social de cliente (SSN), números de telefone e outras informações de identificação pessoal (PII).
- Um provedor de saúde permite apenas médicos e enfermeiros terem acesso a registros médicos confidenciais enquanto não permite membros do departamento de cobrança ver estes dados.
