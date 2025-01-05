# RDBMS의 세계: 도서관 시스템 구축기

## 주인공
임수진 (시립도서관 데이터베이스 시스템 개발자)

## 초기 상황
시립도서관은 오래된 파일 기반 도서 관리 시스템을 RDBMS로 전환하는 프로젝트를 시작했습니다. 수진은 이 프로젝트의 담당자로서 RDBMS의 장점을 활용한 새로운 시스템을 구축하게 됩니다.

## 문제 상황
도서관장: "수진 씨, 우리 도서관이 점점 커지면서 기존 시스템으로는 한계가 있어요. RDBMS라는 걸 도입하면 좋다고 하던데..."

수진: "네, RDBMS의 특징들을 잘 활용해서 문제를 해결해보겠습니다."

## RDBMS의 주요 특징 적용

### 1. 관계형 데이터 모델

```sql
-- 도서 테이블
CREATE TABLE books (
    book_id INT PRIMARY KEY,
    title VARCHAR(200),
    author VARCHAR(100),
    isbn VARCHAR(13),
    publisher VARCHAR(100),
    publication_year INT,
    category_id INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

-- 회원 테이블
CREATE TABLE members (
    member_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    phone VARCHAR(20),
    join_date DATE
);

-- 대출 테이블
CREATE TABLE loans (
    loan_id INT PRIMARY KEY,
    book_id INT,
    member_id INT,
    loan_date DATE,
    due_date DATE,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (member_id) REFERENCES members(member_id)
);
```

사서: "와! 이제 도서와 회원 정보가 체계적으로 연결되어 있네요."

### 2. 데이터 무결성

```sql
-- 도메인 무결성
ALTER TABLE books
ADD CONSTRAINT valid_year 
    CHECK (publication_year BETWEEN 1000 AND 2024),
ADD CONSTRAINT valid_isbn 
    CHECK (LENGTH(isbn) = 13);

-- 참조 무결성
ALTER TABLE loans
ADD CONSTRAINT fk_book
    FOREIGN KEY (book_id) REFERENCES books(book_id)
    ON DELETE RESTRICT,
ADD CONSTRAINT fk_member
    FOREIGN KEY (member_id) REFERENCES members(member_id)
    ON DELETE RESTRICT;

-- 개체 무결성
ALTER TABLE books
ADD CONSTRAINT unique_isbn UNIQUE (isbn);
```

### 3. SQL 지원

```sql
-- 도서 검색 쿼리
SELECT b.title, b.author, c.category_name
FROM books b
JOIN categories c ON b.category_id = c.id
WHERE b.author LIKE '%김%'
  AND c.category_name = '소설';

-- 대출 현황 분석
SELECT 
    m.name,
    COUNT(*) as loan_count,
    MAX(l.loan_date) as last_loan_date
FROM loans l
JOIN members m ON l.member_id = m.member_id
GROUP BY m.member_id, m.name
HAVING COUNT(*) > 5;
```

### 4. 트랜잭션 관리

```sql
-- 도서 대출 처리
BEGIN TRANSACTION;

-- 도서 상태 확인
IF EXISTS (
    SELECT 1 FROM books 
    WHERE book_id = 1001 
    AND status = 'AVAILABLE'
) THEN
    -- 대출 기록 생성
    INSERT INTO loans (book_id, member_id, loan_date, due_date)
    VALUES (1001, 2001, CURRENT_DATE, CURRENT_DATE + 14);
    
    -- 도서 상태 업데이트
    UPDATE books 
    SET status = 'LOANED'
    WHERE book_id = 1001;
    
    COMMIT;
ELSE
    ROLLBACK;
END IF;
```

### 5. 동시성 제어

```sql
-- 락을 통한 동시성 제어
SELECT * FROM books 
WHERE book_id = 1001 
FOR UPDATE;

-- 트랜잭션 격리 수준 설정
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- 데드락 방지
SET LOCK_TIMEOUT 10000;
```

### 6. 백업과 복구

```sql
-- 전체 백업
BACKUP DATABASE library_db
TO DISK = 'D:\backup\library_full.bak'
WITH INIT;

-- 증분 백업
BACKUP DATABASE library_db
TO DISK = 'D:\backup\library_diff.bak'
WITH DIFFERENTIAL;

-- 특정 시점으로 복구
RESTORE DATABASE library_db
FROM DISK = 'D:\backup\library_full.bak'
WITH NORECOVERY;
```

## RDBMS 도입 효과

### 1. 데이터 관리 개선
```sql
-- 중복 도서 검사
SELECT isbn, COUNT(*) as duplicate_count
FROM books
GROUP BY isbn
HAVING COUNT(*) > 1;

-- 데이터 정합성 검증
SELECT l.loan_id, l.return_date, b.status
FROM loans l
JOIN books b ON l.book_id = b.book_id
WHERE l.return_date IS NULL 
  AND b.status = 'AVAILABLE';
```

### 2. 업무 효율성 향상
```sql
-- 연체 도서 자동 알림
CREATE VIEW overdue_loans AS
SELECT 
    m.name, m.email,
    b.title,
    l.due_date,
    CURRENT_DATE - l.due_date as days_overdue
FROM loans l
JOIN members m ON l.member_id = m.member_id
JOIN books b ON l.book_id = b.book_id
WHERE l.return_date IS NULL
  AND l.due_date < CURRENT_DATE;
```

### 3. 데이터 분석 지원
```sql
-- 인기 도서 분석
SELECT 
    b.title,
    b.author,
    COUNT(*) as loan_count
FROM loans l
JOIN books b ON l.book_id = b.book_id
WHERE l.loan_date >= CURRENT_DATE - 30
GROUP BY b.title, b.author
ORDER BY loan_count DESC
LIMIT 10;
```

## 결론
수진의 보고서:
"RDBMS 도입을 통해 다음과 같은 이점을 얻었습니다:
- 체계적인 데이터 관리
- 데이터 정합성 보장
- 효율적인 검색과 분석
- 안정적인 트랜잭션 처리
- 강력한 보안 기능"

## 수진의 소감
"RDBMS는 단순한 데이터 저장소가 아니라, 도서관 업무의 근간이 되는 핵심 시스템이라는 것을 알게 되었습니다. 특히 관계형 모델과 SQL의 강력함, 그리고 트랜잭션 관리 기능이 실무에서 얼마나 중요한지 깨달았어요!"