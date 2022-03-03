# 1. SQL의 설치

> MSSQL Server 2016 강의를 기록한 내용.

- 전체적인 설치 순서
    1. SQL Server Package Download (GB)
    2. SQL Server Install with SP2 (2014 with SP3)
    3. SSMS Install ( 2016 버전부터 2번, 3번 과정이 분리됨 )
    4. Sample Database Install (Reboot)

- SQL Server 패키지 용량만 3G 이상, 총 15GB 이상 여유 필요

### SQL Server Version 1

- alpha : 개발 막 끝난 내부용 버전

- beta : 알파 이후 테스트 버전

- CTP : Community Technology Preview 약자 

- 관련 커뮤니티, 그룹에 테스트 배포

- RC : Release Candidate 약자

- 약간의 버그 수정용 버전 RC0 > RC1 > RC2 ...

### version 2

- Preview : 정식 버전 직전의 버전

- RTM : Release To Manufacture 약자

- 제조업체에 최종 공급할 최종 정식 버전

- 일반 소비자 시판 버전보다 조금 일찍 출시(거의 같은 버전)

- 평가판 사이트 다운로드도 보통 RTM 버전

### version 3

- Retail : 정식 판매버전



# 2. SQL Server 주요 에디션

- 크게 봤을 때
    - enterprise : 기업이 사용하는 등 모든 기능 탑재 
    - standard : 일부 기능 탑재
- 세부 서비스로 나웠을 때
    - Web
    - Express : 무료버전
- 기타 개발용과 평가용 에디션
    - Developer
    - Evaluation : enterprise 에디션과 동일



# 3. SQL Server 인증 모드

- Windows 인증모드 / 혼합 모드(SQL server 인증 및 windows 인증)

- 설치 플로우

    : ~~ >인스턴스 구성> 서버구성 > 데이터베이스 엔진 구성(인증모드)

    - Windows 인증모드

        - 윈도우 사용자만 SQL Server에 접속 가능
        - 윈도우 사용자 중에서 별도로 지정(가급적 관리자 권한으로)
        - 사용자 추가시 SQL server 관리자로 지정
        - 별도의 사용자계정 및 PW 필요 X

    - 혼합모드

        - 윈도우 사용자 아니어도 사용자 등록 가능

        - SQL Server에 접속할 사용자를 별도 지정 가능

            >  계정만 있으면 접속가능

        - 보안은 취약하나 외부 PC에서 서버에 접속하기엔 좋다

    > 혼합모드 보단 `Windows 인증 모드`가 더 강력하다



# 4. SA계정

- #### Sa

    - System Administrator의 약자 > `관리자 계정`
    - SQL 서버의 `최상위 관리자` 계정
    - 혼합모드 설치시  생성, Sa계정에 비밀번호 등록 가능(찾기 기능 없다..까먹으면 안돼)

> 처음 사용하는 초보의 경우 윈도우 인증모드로 사용
>
> > 추후 혼합모드로 변경



# 5. 설치과정 - 인스턴스

[평가판 설치 링크](https://www.microsoft.com/ko-kr/evalcenter/evaluate-sql-server-2016)

>  현재 본인은 D드라이브에  설치 하였음, 추후 오류시 재설치 예정

- 설치
    -  새 SQL Server 독립실행형 설치 또는 기존설치에 기능 추가
        - ~~ > 기능선택 \ 데이터베이스엔진서비스 \ 검색을 위한 전체 택스트 의미체계
    - SQL Server 관리도구 설치

- ## 인스턴스 구성

    - SQL Server 인스턴스이름과 인스턴스 ID 지정
    - sql 서버 하나가 설치되면 그냥 인스턴스(대상,SQLDB) 하나가 생긴다고 보면 됨

- 서버구성

    - SQL Server 데이터베이스 엔진만 자동으로 설정 됨

- ### 데이터베이스 엔진 구성

    - DB구성, **인증모드**
    - 윈도우 인증모드 > 현재사용자추가

> 설치가 끝나면 재부팅 실시



# 6. 설치완료 후 관리도구 설정

1. ~~SQL Server Package Download (GB)~~ 데이터베이스, 서버 설치 완료 
2. ~~SQL Server Install with SP2 (2014 with SP3)~~ 
3. **SSMS Install** > **관리도구 툴 : SSMS 설치**
4. Sample Database Install (Reboot) 



- 설치된 폴더 > Evaluation_KOR > setup > 설치 > **SQL Server 관리도구 설치**

---

