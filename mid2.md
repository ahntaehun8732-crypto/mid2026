# SQLite를 활용한 학생 정보 관리 시스템 (C언어)

## 1. 시나리오 설명
- **주제**: 대학생 정보 관리 및 검색 프로그램
- **데이터 내용**: 3명 이상의 학생 데이터(학번, 이름, 전공)를 SQLite 데이터베이스에 삽입(`INSERT`)한 후, 특정 이름을 기준으로 검색(`SELECT`)하여 결과를 출력하는 시나리오입니다.

## 2. 소스 코드 (`main.c`)

```c
#include <stdio.h>
#include <stdlib.h>
#include <sqlite3.h>

// 데이터 검색 결과를 처리하기 위한 콜백 함수
static int callback(void *NotUsed, int argc, char **argv, char **azColName) {
    for (int i = 0; i < argc; i++) {
        printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
    }
    printf("--------------------\n");
    return 0;
}

int main(void) {
    sqlite3 *db;
    char *errMsg = 0;
    int rc;

    // 1. 데이터베이스 연결 (파일이 없으면 자동 생성)
    rc = sqlite3_open("student.db", &db);
    if (rc) {
        fprintf(stderr, "데이터베이스 오픈 실패: %s\n", sqlite3_errmsg(db));
        return (0);
    } else {
        fprintf(stdout, "데이터베이스 연결 성공!\n");
    }

    // 2. 테이블 생성 (학번, 이름, 전공)
    const char *sql_create = "CREATE TABLE IF NOT EXISTS STUDENTS("  \
                             "ID INT PRIMARY KEY     NOT NULL," \
                             "NAME           TEXT    NOT NULL," \
                             "MAJOR          TEXT    NOT NULL);";

    rc = sqlite3_exec(db, sql_create, callback, 0, &errMsg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "테이블 생성 에러: %s\n", errMsg);
        sqlite3_free(errMsg);
    } else {
        fprintf(stdout, "테이블 생성 완료\n");
    }

    // 3. 3명 이상의 데이터 삽입 (INSERT)
    const char *sql_insert = "INSERT INTO STUDENTS (ID, NAME, MAJOR) VALUES (202601, '안태훈', '컴퓨터공학');" \
                             "INSERT INTO STUDENTS (ID, NAME, MAJOR) VALUES (202602, '김철수', '소프트웨어');" \
                             "INSERT INTO STUDENTS (ID, NAME, MAJOR) VALUES (202603, '박영희', '인공지능');";

    rc = sqlite3_exec(db, sql_insert, callback, 0, &errMsg);
    if (rc != SQLITE_OK) {
        // 이미 데이터가 들어있을 경우의 중복 에러는 무시하고 진행 가능
        fprintf(stderr, "데이터 삽입 (또는 중복): %s\n", errMsg);
        sqlite3_free(errMsg);
    } else {
        fprintf(stdout, "학생 데이터 3건 삽입 완료\n");
    }

    // 4. 데이터 검색 (SELECT - 이름이 '안태훈'인 학생 검색)
    const char *sql_select = "SELECT * FROM STUDENTS WHERE NAME = '안태훈';";
    
    fprintf(stdout, "\n--- 검색 결과 ('안태훈' 조회) ---\n");
    rc = sqlite3_exec(db, sql_select, callback, 0, &errMsg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "검색 에러: %s\n", errMsg);
        sqlite3_free(errMsg);
    }

    // 5. 데이터베이스 닫기
    sqlite3_close(db);
    return 0;
}
<img width="1110" height="628" alt="{355CE351-122F-4672-8B77-CEEAFBCB4797}" src="https://github.com/user-attachments/assets/42555f36-f11c-4ce9-9eee-6ea0561731a3" />
