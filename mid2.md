```
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include "sqlite3.h"

// 데이터 조회 결과를 화면에 이쁘게 출력해주는 콜백 함수
static int callback(void* NotUsed, int argc, char** argv, char** azColName) {
    for (int i = 0; i < argc; i++) {
        printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
    }
    printf("\n");
    return 0;
}

int main(void) {
    sqlite3* db = NULL;
    char* errMsg = NULL;
    int rc;

    // 1. 데이터베이스 열기
    rc = sqlite3_open("student.db", &db);
    if (rc) {
        fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
        return 0;
    }

    // 2. 기존 테이블이 있으면 지우고 새로 깔끔하게 생성
    const char* sqlCreate = "DROP TABLE IF EXISTS Student;"
        "CREATE TABLE Student(ID INT, NAME TEXT, MAJOR TEXT);";
    sqlite3_exec(db, sqlCreate, 0, 0, &errMsg);

    // 3. 학생 데이터 3명 삽입
    const char* sqlInsert = "INSERT INTO Student VALUES (1, 'Alice', 'CS');"
        "INSERT INTO Student VALUES (2, 'Bob', 'EE');"
        "INSERT INTO Student VALUES (3, 'Charlie', 'Math');";
    sqlite3_exec(db, sqlInsert, 0, 0, &errMsg);

    // 4. 데이터 조회 및 출력
    printf("--- Student Database Results ---\n\n");
    const char* sqlSelect = "SELECT * FROM Student;";
    sqlite3_exec(db, sqlSelect, callback, 0, &errMsg);

    // 5. 데이터베이스 닫기
    sqlite3_close(db);

    // 창이 바로 꺼지지 않도록 대기
    printf("\nPress any key to exit...\n");
    getchar();

    return 0;
}
```
<img width="1118" height="625" alt="image" src="https://github.com/user-attachments/assets/a7e9fa37-6c07-430a-af27-4056f092c825" />
