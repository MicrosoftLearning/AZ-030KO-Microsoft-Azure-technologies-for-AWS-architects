﻿# 랩: Azure SQL Database 기반 애플리케이션 구현

## 랩 시나리오

Adatum Corporation은 .NET Core 기반 프런트 엔드 및 SQL Server 기반 백 엔드가 있는 2단계 애플리케이션을 보유하고 있습니다. Adatum 엔터프라이즈 아키텍처 팀은 Azure SQL Database를 데이터 계층으로 활용하여 이러한 애플리케이션을 구현할 수 있는 가능성을 모색하고 있습니다. 기존 SQL Server 백 엔드의 일시적이고 예측할 수 없는 사용과 프런트 엔드 앱에 내장된 대기 시간에 대한 허용 오차가 상대적으로 높기 때문에 Adatum은 Azure SQL Database의 서버리스 계층을 고려하고 있습니다. 

서버리스는 초당 사용되는 컴퓨팅에 대한 워크로드 수요 및 청구서에 따라 컴퓨팅을 자동으로 확장하는 개별 Azure SQL Database 인스턴스의 컴퓨팅 계층입니다. 또한 서버리스 컴퓨팅 계층은 스토리지만 청구되는 비활성 기간 동안 데이터베이스를 자동으로 일지 중지하고 활동이 반환될 때 데이터베이스를 자동으로 재개할 수 있습니다.

Adatum 엔터프라이즈 아키텍처 팀은 또한 Azure SQL Databases에서 제공하는 네트워크 수준 보안을 평가하는 데 관심이 있으며, 앱이 사이트 간 VPN 또는 ExpressRoute를 통한 하이브리드 연결에 의존하지 않고 온-프레미스 위치에서 연결할 수 있어야 하는 시나리오에서 특정 IP 주소 범위에 대한 인바운드 연결을 제한할 수 있도록 합니다. 
  
이러한 목표를 달성하기 위해 Adatum 아키텍처 팀은 다음과 같은 Azure SQL Database 기반 애플리케이션을 테스트합니다.

-  Azure SQL Database에서 서버리스 계층 구현

-  Azure SQL Database를 데이터 저장소로 사용하는 .NET Core 콘솔 앱 구현


## 목표
  
이 랩을 마치면 다음과 같은 역량을 갖추게 됩니다.

-  Azure SQL Database의 서버리스 계층 구현

-  Azure SQL Database를 데이터 저장소로 사용하는 .NET Core 기반 콘솔 앱 구성


## 랩 환경
  
Windows Server 관리자 자격 증명

-  사용자 이름: **Student**

-  암호: **Pa55w.rd1234**

예상 시간: 60분


## 랩 파일

-  없음


### 실습 1: Azure SQL Database 구현
  
이 연습의 주요 작업은 다음과 같습니다.

1. Azure SQL Database 만들기 

1. Azure SQL Database 연결 및 쿼리


#### 작업 1: Azure SQL Database 만들기 

1. 랩 컴퓨터에서 웹 브라우저를 시작하고 [Azure Portal](https://portal.azure.com)로 이동하며 이 랩에서 사용할 구독에서 소유자 역할로 사용자 계정의 자격 증명을 제공하여 로그인합니다.

1. Azure Portal에서 **SQL Database**를 검색 및 선택하고 **SQL Database** 블레이드에서 **+ 만들기**를 선택합니다.

1. **SQL Database 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 남겨둠).

    | 설정 | 값 | 
    | --- | --- |
    | 구독 | 이 랩에서 사용할 Azure 구독의 이름 |
    | 리소스 그룹 | 새 리소스 그룹 **az30303a-LabRG**의 이름 |
    | 데이터베이스 이름 | **az30303a-db1** | 

1. **서버** 드롭다운 목록 바로 아래에서 **새로 만들기**를 선택하고 **새 서버** 블레이드에서 다음 설정을 지정하고 **확인**을 선택합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 | 
    | --- | --- |
    | 서버 이름 | 유효하며 전역적으로 고유한 이름 | 
    | 서버 관리자 로그인 | **sqladmin** |
    | 암호 | **Pa55w.rd1234** |
    | 위치 | SQL 데이터베이스를 프로비전할 수 있는 Azure 지역 이름 |

1. **컴퓨팅 + 스토리지** 레이블 옆에 있는 **데이터베이스 구성** 링크를 선택합니다.

1. **구성** 블레이드에서 **서버리스**를 선택하고 해당 하드웨어 구성 및 자동 일시 중지 지연 설정을 검토하고 **자동 일시 중지 사용** 체크박스를 사용하도록 설정하고 **적용**을 선택합니다.

1. **SQL Database 만들기** 블레이드의 **기본** 탭으로 돌아가서 **다음**을 선택합니다. **네트워킹 >**. 

1. **SQL Database 만들기** 블레이드의 **네트워킹** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 | 
    | --- | --- |
    | 연결 방법 | **공용 엔드포인트** |  
    | Azure 서비스 및 리소스에서 이 서버에 액세스할 수 있도록 허용 | **아니요** |
    | 현재 클라이언트 IP 주소 추가 | **예** |

1. **다음: 보안 >** 을 선택합니다.

1. **SQL Database 만들기** 블레이드의 **보안** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 | 
    | --- | --- |
    | Azure Defender for SQL 사용 | **나중에** |

1. **다음: 추가 설정 >**. 

1. **SQL Database 만들기** 블레이드의 **추가 설정** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 | 
    | --- | --- |
    | 기존 데이터 사용 | **샘플** |

1. **검토 + 만들기**를 선택하고 **만들기**를 선택합니다. 

    >**참고**: SQL Database가 만들어질 때까지 기다립니다. 프로비전에는 약 2분이 소요됩니다. 


#### 작업 2: Azure SQL Database 연결 및 쿼리

1. Azure Portal에서 **SQL Database**를 검색 및 선택하고 **SQL Database** 블레이드에서 새로 만든 **az30303a-db1** Azure SQL Database를 나타내는 항목을 선택합니다.

1. SQL Database 블레이드에서 **쿼리 편집기(미리 보기)** 를 선택합니다.

1. **SQL Server 인증** 섹션의 **암호** 텍스트 상자에 **Pa55w.rd1234**를 입력하고 **확인**을 선택합니다.

1. **쿼리 편집기(미리 보기)** 창의 **쿼리 1** 탭에 다음 쿼리를 입력하고 **실행**을 선택합니다.

    ```SQL
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
    ```

1. **결과** 탭을 검토하여 쿼리가 성공적으로 완료되었는지 확인합니다.


### 연습 2: Azure SQL Database를 데이터 저장소로 사용하는 .NET Core 콘솔 앱 구현
  
이 연습의 주요 작업은 다음과 같습니다.

1. Azure SQL Database의 ADO.NET 연결 정보 식별

1. .NET Core 콘솔 앱 만들기 및 구성

1. .NET Core 콘솔 앱 테스트

1. Azure SQL Database 방화벽 구성

1. .NET Core 콘솔 앱의 기능 확인

1. 랩에서 배포한 Azure 리소스 제거


#### 작업 1: Azure SQL Database의 ADO.NET 연결 정보 식별

1. Azure Portal에서 이전 연습에서 배포한 Azure SQL Database 블레이드의 **설정** 섹션에서 **연결 문자열**을 선택합니다.

1. **ADO.NET** 탭에서 SQL 인증을 위한 ADO.NET 연결 문자열을 기록합니다.


#### 작업 2: .NET Core 콘솔 앱 만들기 및 구성

1. Azure Portal의 검색 텍스트 상자의 오른쪽에 있는 도구 모음 아이콘에서 직접 선택하여 **Cloud Shell** 창을 엽니다.

1. **Bash** 또는 **PowerShell**을 선택하라는 메시지가 표시되면 **Bash**를 선택합니다. 

    >**참고**: **Cloud Shell**을 처음 시작했는데 **탑재된 스토리지가 없습니다** 메시지가 표시되면 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다. 

1. Cloud Shell 창에서 다음을 실행하여 **az30303a1**이라는 새 폴더를 만들고 현재 디렉터리로 설정합니다.

   ```sh
   mkdir az30303a1
   cd az30303a1/
   ```

1. Cloud Shell 창에서 다음을 실행하여 데스크톱 템플릿을 기반으로 한 .NET Core 기반 앱에 대한 새 앱 프로젝트 파일을 만듭니다.

   ```sh
   dotnet new console
   ```

1. Cloud Shell 창에서 기본 제공된 편집기를 사용하여 `<Project>` 태그 사이에 다음과 같은 XML 요소를 추가함으로써 **az30303a1.csproj** 파일을 열고 수정합니다. 

   ```xml
   <ItemGroup>
       <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
   </ItemGroup>
   ```

1. **az30303a1.csproj** 파일을 저장하고 닫습니다.

1. Cloud Shell 창에서 기본 제공된 편집기를 사용하여 콘텐츠를 다음 코드로 대체함으로써 **Program.cs** 파일을 열고 수정합니다. 

   ```cs
   using System;
   using System.Data.SqlClient;
   using System.Text;

   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.ConnectionString="<your_ado_net_connection_string>";

                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
        
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();

                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.WriteLine("\nDone. Press enter.");
               Console.ReadLine(); 
           }
       }
   }
   ```

1. 편집기 창을 열어 둡니다. 

1. Azure Portal의 **az30303a-db1** 데이터베이스에 대한 연결 문자열을 표시하는 블레이드에서 ADO.NET 연결 문자열을 복사합니다. 

1. 편집기 창으로 다시 전환하고 자리 표시자 `<your_ado_net_connection_string>`을 이전 단계에서 복사한 연결 문자열의 값으로 바꿉니다.

1. 편집기 창에 복사한 연결 문자열에서 자리 표시자 `{your_password}` 를 **Pa55w.rd1234**로 바꿉니다.

1. **Program.cs** 파일을 저장하고 닫습니다.


#### 작업 3: .NET Core 콘솔 앱 테스트

1. Cloud Shell 창에서 다음을 실행하여 새로 만든 .NET Core 기반 콘솔 앱을 컴파일합니다.

   ```sh
   dotnet restore
   ```

1. Cloud Shell 창에서 다음을 실행하여 새로 만든 .NET Core 기반 콘솔 앱을 실행합니다.

   ```sh
   dotnet run
   ```

1. 콘솔 앱의 실행은 오류를 트리거합니다. 

    >**참고**: Cloud Shell 세션을 실행하는 가상 머신에 할당된 IP 주소의 연결이 명시적으로 허용되어야 하므로 이 작업이 예상됩니다.


#### 작업 4: Azure SQL Database 방화벽 구성

1. Cloud Shell 창에서 다음을 실행하여 Cloud Shell 세션을 실행하는 가상 머신의 공용 IP 주소를 식별합니다.

   ```sh
   curl -s checkip.dyndns.org | sed -e 's/.*Current IP Address: //' -e 's/<.*$//'
   ```

1. Azure Portal의 **az30303a-db1** 데이터베이스에 대한 연결 문자열을 표시하는 블레이드에서 **개요**를 선택하고 도구 모음에서 **서버 방화벽 설정**을 선택합니다.

1. **방화벽 설정** 블레이드에서 다음 항목을 설정하고 **저장**을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 규칙 이름 | **cloudshell** |
    | 시작 IP | 앞서 이 작업에서 식별한 IP 주소 |
    | 종료 IP | 앞서 이 작업에서 식별한 IP 주소 |

    >**참고**: 랩에서만 사용하기 위한 것입니다. Cloud Shell 세션을 다시 시작한 후에는 IP 주소가 변경되기 때문입니다.


#### 작업 5: .NET Core 콘솔 앱의 기능 확인

1. Cloud Shell 창에서 다음을 실행하여 새로 만든 .NET Core 기반 콘솔 앱을 실행합니다.

   ```sh
   dotnet run
   ```

1. 콘솔 앱의 실행은 이번에는 성공할 것이며 Azure Portal SQL Database 블레이드 내의 쿼리 편집기에 표시되는 것과 동일한 결과를 반환합니다. 


#### 작업 6: 랩에서 배포한 Azure 리소스 제거

1. Cloud Shell 창에서 다음을 실행하여 이 실습에서 만든 리소스 그룹을 나열합니다.

   ```sh
   az group list --query "[?starts_with(name,'az30303')]".name --output tsv
   ```

    > **참고**: 이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 이 작업에서 이 그룹은 삭제됩니다.

1. Cloud Shell 창에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 삭제합니다.

   ```sh
   az group list --query "[?starts_with(name,'az30303')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. Cloud Shell 창에서 다음 명령을 실행하여 **az30303a1** 폴더를 제거합니다.

   ```sh
   rm -r ~/az30303a1
   ```

1. Cloud Shell 창을 닫습니다.
