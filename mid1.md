```
#include "raylib.h"
#include <stdio.h>

#define SCREEN_WIDTH 800
#define SCREEN_HEIGHT 600
#define ROWS 4
#define COLS 8

typedef struct {
    Rectangle rect;
    bool active;
    Color color;
} Block;

int main(void) {
    InitWindow(SCREEN_WIDTH, SCREEN_HEIGHT, "Rainbow Block Breaker");
    SetTargetFPS(60);

    Rectangle paddle = { SCREEN_WIDTH / 2.0f - 50, SCREEN_HEIGHT - 40, 100, 15 };
    Vector2 ballPos = { SCREEN_WIDTH / 2.0f, SCREEN_HEIGHT / 2.0f };
    Vector2 ballSpeed = { 5.0f, -5.0f };
    float ballRadius = 8.0f;

    Block blocks[ROWS][COLS];
    Color colors[ROWS] = { RED, ORANGE, YELLOW, GREEN };
    float bWidth = SCREEN_WIDTH / (float)COLS;
    float bHeight = 25.0f;

    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            blocks[i][j] = (Block){ { j * bWidth + 5, i * (bHeight + 5) + 40, bWidth - 10, bHeight }, true, colors[i] };
        }
    }

    int score = 0;
    int lives = 5; // 5회 실패(목숨) 시스템

    while (!WindowShouldClose()) {
        // Paddle Movement
        if (IsKeyDown(KEY_LEFT) && paddle.x > 0) paddle.x -= 7.0f;
        if (IsKeyDown(KEY_RIGHT) && paddle.x + paddle.width < SCREEN_WIDTH) paddle.x += 7.0f;

        // Ball Movement
        ballPos.x += ballSpeed.x;
        ballPos.y += ballSpeed.y;

        // Wall Collision (Left, Right, Top)
        if (ballPos.x - ballRadius <= 0 || ballPos.x + ballRadius >= SCREEN_WIDTH) ballSpeed.x *= -1;
        if (ballPos.y - ballRadius <= 0) ballSpeed.y *= -1;

        // Bottom Collision (Fail / Lose Life)
        if (ballPos.y + ballRadius >= SCREEN_HEIGHT) {
            lives--;
            if (lives <= 0) {
                // Reset Game after 5 fails
                lives = 5;
                score = 0;
                for (int i = 0; i < ROWS; i++) {
                    for (int j = 0; j < COLS; j++) blocks[i][j].active = true;
                }
            }
            ballPos = (Vector2){ SCREEN_WIDTH / 2.0f, SCREEN_HEIGHT / 2.0f };
            ballSpeed = (Vector2){ 5.0f, -5.0f };
        }

        // Paddle Collision
        if (CheckCollisionCircleRec(ballPos, ballRadius, paddle)) {
            ballSpeed.y *= -1;
            ballSpeed.x = (ballPos.x - (paddle.x + paddle.width / 2.0f)) / 10.0f;
        }

        // Block Collision
        for (int i = 0; i < ROWS; i++) {
            for (int j = 0; j < COLS; j++) {
                if (blocks[i][j].active && CheckCollisionCircleRec(ballPos, ballRadius, blocks[i][j].rect)) {
                    blocks[i][j].active = false;
                    ballSpeed.y *= -1;
                    score += 10;
                }
            }
        }

        // Render
        BeginDrawing();
        ClearBackground(BLACK);

        DrawRectangleRec(paddle, WHITE);
        DrawCircleV(ballPos, ballRadius, YELLOW);

        for (int i = 0; i < ROWS; i++) {
            for (int j = 0; j < COLS; j++) {
                if (blocks[i][j].active) {
                    DrawRectangleRec(blocks[i][j].rect, blocks[i][j].color);
                }
            }
        }

        DrawText(TextFormat("Score: %d", score), 20, 10, 20, WHITE);
        DrawText(TextFormat("Lives: %d", lives), SCREEN_WIDTH - 100, 10, 20, RED);

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
```
