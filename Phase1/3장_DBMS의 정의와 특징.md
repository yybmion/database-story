# DBMS의 세계: IT 회사의 성장기

## 주인공
정유진 (스타트업 'TechFlow'의 시스템 관리자)

## 초기 상황
TechFlow는 급격한 성장 중인 IT 서비스 회사입니다. 유진은 최근 입사했으며, 회사의 데이터 관리 시스템을 개선하는 임무를 맡았습니다.

## 문제 상황
개발팀장: "유진 씨, 우리 회사가 커지면서 데이터 관리가 점점 더 복잡해지고 있어요. DBMS 도입을 검토해봐야 할 것 같은데..."

유진: "네, 제가 DBMS에 대해 조사해보고 보고드리겠습니다."

## DBMS의 정의와 도입 과정

### 1. 정의 이해하기
유진의 조사 노트:
```
DBMS(Database Management System)란?
- 데이터베이스를 관리하는 통합 소프트웨어 시스템
- 데이터의 저장, 수정, 삭제, 검색 등을 효율적으로 수행
- 예시: MySQL, Oracle, PostgreSQL 등
```

### 2. 주요 기능 파악
시니어 개발자와의 대화:

시니어: "DBMS가 제공하는 주요 기능들을 살펴볼까요?"

#### 데이터 정의 기능 (DDL)
```sql
-- 테이블 생성
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    hire_date DATE
);

-- 테이블 구조 수정
ALTER TABLE employees ADD COLUMN salary DECIMAL(10,2);
```

#### 데이터 조작 기능 (DML)
```sql
-- 데이터 입력
INSERT INTO employees VALUES (1, '김철수', 'IT', '2024-01-15');

-- 데이터 수정
UPDATE employees SET department = 'Development' WHERE id = 1;

-- 데이터 검색
SELECT * FROM employees WHERE department = 'Development';
```

#### 데이터 제어 기능 (DCL)
```sql
-- 접근 권한 부여
GRANT SELECT, INSERT ON employees TO 'hr_team';

-- 트랜잭션 제어
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;
COMMIT;
```

## DBMS의 특징

### 1. 데이터 독립성
상황: 시스템 업그레이드 중
```sql
-- 물리적 저장 구조 변경
ALTER TABLE employees MODIFY name VARCHAR(150);
```
유진: "응용 프로그램을 수정하지 않고도 데이터 구조를 변경할 수 있네요!"

### 2. 동시성 제어
상황: 여러 부서의 동시 접근
```sql
-- 락을 통한 동시성 제어
LOCK TABLE employees IN EXCLUSIVE MODE;
UPDATE employees SET salary = salary * 1.1;
UNLOCK TABLE;
```

### 3. 데이터 보안
상황: 부서별 접근 권한 설정
```sql
-- 역할 기반 접근 제어
CREATE ROLE hr_manager;
GRANT ALL PRIVILEGES ON employees TO hr_manager;
GRANT SELECT ON employees TO general_staff;
```

### 4. 백업과 복구
상황: 시스템 장애 대비
```sql
-- 백업 생성
BACKUP DATABASE company_db TO DISK = 'D:\backup\company_20240103.bak';

-- 복구 수행
RESTORE DATABASE company_db FROM DISK = 'D:\backup\company_20240103.bak';
```

### 5. 데이터 무결성
상황: 데이터 품질 관리
```sql
-- 무결성 제약조건 설정
ALTER TABLE employees
ADD CONSTRAINT salary_check CHECK (salary >= 0),
ADD CONSTRAINT dept_fk FOREIGN KEY (department_id) 
    REFERENCES departments(id);
```

## 도입 결과
1. 데이터 관리 효율성 향상
    - 중앙 집중식 데이터 관리
    - 데이터 중복 최소화
    - 일관된 데이터 접근 방법

2. 보안 강화
    - 세밀한 접근 제어
    - 데이터 암호화
    - 감사 추적 가능

3. 시스템 안정성 개선
    - 자동 백업 및 복구
    - 장애 대응 능력 향상
    - 데이터 일관성 유지

## 결론
유진의 보고서:
"DBMS 도입으로 우리 회사는 다음과 같은 이점을 얻을 수 있습니다:
- 효율적인 데이터 관리
- 강화된 데이터 보안
- 향상된 데이터 일관성
- 신뢰성 있는 백업/복구
- 효과적인 동시성 제어"

개발팀장: "훌륭해요! 이제 DBMS 도입을 본격적으로 진행해보죠."

## 유진의 소감
"DBMS가 단순한 데이터 저장소가 아니라, 기업의 데이터를 안전하고 효율적으로 관리하는 핵심 인프라라는 것을 이해하게 되었습니다. 이번 프로젝트를 통해 데이터 관리의 중요성을 실감했어요!"