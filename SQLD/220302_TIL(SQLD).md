# SQL Server 관리도구 설치

### 관리도구 웹사이트

> SSMS 다운로드

SQL Server Management Studio 실행

과정

1.  SQL 서버에 연결
    - 윈도우 인증모드 or 호환모드 Sa 계정
2. 서버 management 메뉴 설정 및 구성관리자 사용



## 서버 management studio

![image-20220303213104699](220302_TIL(SQLD).assets/image-20220303213104699.png)





## 구성관리자

![image-20220303213042926](220302_TIL(SQLD).assets/image-20220303213042926.png)



## SQL Server 설치폴더 확인

`D:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL`

- 80, 90, 100 등의 폴더는 기존 버전과의 호환성을 위해 존재하는 폴더
- MSSQL13~ 은 130버전임을 의미

- 130폴더(참고용)
    - COM 개체파일
    - 보안, 인증관련 keyfile
    - 소프트웨어 개발 라이브러리 SDK
    - 설치관련 도움말 Setup Bootstrap
    - 공유구성요소 Shared

- MSSQL13.MSSQLSERVER 폴더는 중요한 내용들이 들어있음
    - 백업 기본폴더, 실행관련 DLL파일(Binn), 데이터 로그파일(DATA), 운영로그(Log) 등



# SQL Server Sample DB

- 학습을 위해 제공되는 샘플 데이터베이스
    - Pubs, Adventure Works ( 별도 다운 필요 )
    - **Pubs**
        - 가상의 출판 데이터
        - instpubs.sql 형태로 제공되므로 sqlcmd를 사용해 인스톨
        - DB를 생성해보는 과정을 이해할 수 있음
    - **Adventure Works**
        - 자전거 DB
        - bak 형태로 제공되므로 다운후 임포트 시켜 생성



### Pubs 샘플 - SQLCMD 명령어로 DB 생성하기

- Pubs 데이터베이스 다운로드

- 위치

    `C:\SQL Server 2000 Sample Databases\instpubs.sql`



- SSMD의 데이터베이스 탭에 샘플데이터를 생성하기 위한 작업
    - cmd > dir (디렉토리 목록)
        - (상위폴더로 이동 cd ..)
    - cd sql server 2000 sample Databases (폴더로 이동)
    - sqlcmd -i instpubs.sql (설치)
    - SSMS에서 새로고침하면 원통모양 아이콘의 데이터베이스 생성 확인

![image-20220303215657112](220302_TIL(SQLD).assets/image-20220303215657112.png)

> 설치하는 방법은 다양함 (db파일이든 백업파일이든)

> .sql 형태의 파일은 지금처럼 설치



### Pubs 삭제

- 그냥 우클릭, 삭제
    - 생성한지 얼마 안됐을 때 이렇게 삭제하면 삭제 오류
    - cmd랑 ssms 껐다 키면 정상적으로 삭제됨
        - (SQL Server 설정을 자동으로 했기 때문에 자동으로 업데이트 된다)
        - 구성관리자 보면 확인 가능



#### AdventureWorks 데이터 설치

- .bak 파일 -> 다운 후 임포트
- [다운링크](https://docs.microsoft.com/ko-kr/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms)
- Server 버전과 같은 버전 파일 설치 필요
- MSSQL13.MSSQLSERVER/MSSQL/Backup 폴더에 이동(복원준비 끝)
    - 혹은 다운받은 폴더를 찾아서 추가하면됨
- 데이터베이스 우클릭 -> 복원 -> 디바이스 ...선택 -> 추가버튼 클릭 -> .bak 추가
    - 다운받은 경로가 다른 경우 추가버튼 누르고 해당 폴더 찾기
- 삭제는 우클릭 삭제



## 샘플데이터 DB 호환성 및 버전관련 주의사항

- AdventureWorks 파일은 Server 버전과 통일 시킬 것(최소한 하위버전이라도)
    - 복원시 백업파일이 Server버전보다 상위버전에서 작업되었으면 conflict

- 백업파일 복원 때 충돌 발생