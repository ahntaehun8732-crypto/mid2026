#include "raylib.h"
#include <stdio.h>

#define SCREEN_WIDTH 800
#define SCREEN_HEIGHT 600

#define NUM_BLOCK_ROWS 6
#define NUM_BLOCK_COLS 10
#define TOTAL_BLOCKS (NUM_BLOCK_ROWS * NUM_BLOCK_COLS)

typedef struct Block {
    Rectangle rect;
    bool active;
    Color color;
} Block;

typedef struct Paddle {
    Rectangle rect;
    float speed;
} Paddle;

typedef struct Ball {
    Vector2 position;
    Vector2 speed;
    float radius;
    bool active;
} Ball;

int main(void) {
    InitWindow(SCREEN_WIDTH, SCREEN_HEIGHT, "Rainbow Block Breaker");
    SetTargetFPS(60);

    Paddle paddle = {
        .rect = { SCREEN_WIDTH / 2.0f - 60, SCREEN_HEIGHT - 30, 120, 15 },
        .speed = 8.0f
    };

    Ball ball = {
        .position = { SCREEN_WIDTH / 2.0f, SCREEN_HEIGHT / 2.0f },
        .speed = { 5.0f, -5.0f },
        .radius = 8.0f,
        .active = true
    };

    Color rainbowColors[NUM_BLOCK_ROWS] = {
        RED, ORANGE, YELLOW, GREEN, BLUE, PURPLE
    };

    Block blocks[TOTAL_BLOCKS];
    float blockWidth = (SCREEN_WIDTH - 40) / NUM_BLOCK_COLS;
    float blockHeight = 20.0f;

    for (int i = 0; i < NUM_BLOCK_ROWS; i++) {
        for (int j = 0; j < NUM_BLOCK_COLS; j++) {
            int index = i * NUM_BLOCK_COLS + j;
            blocks[index].rect = (Rectangle){
                20 + j * blockWidth,
                50 + i * (blockHeight + 5),
                blockWidth - 5,
                blockHeight
            };
            blocks[index].active = true;
            blocks[index].color = rainbowColors[i];
        }
    }

    int score = 0;
    bool gameOver = false;
    bool gameWon = false;

    while (!WindowShouldClose()) {
        if (!gameOver && !gameWon) {
            if (IsKeyDown(KEY_LEFT) && paddle.rect.x > 0)
                paddle.rect.x -= paddle.speed;
            if (IsKeyDown(KEY_RIGHT) && (paddle.rect.x + paddle.rect.width) < SCREEN_WIDTH)
                paddle.rect.x += paddle.speed;

            ball.position.x += ball.speed.x;
            ball.position.y += ball.speed.y;

            if (ball.position.x - ball.radius <= 0 || ball.position.x + ball.radius >= SCREEN_WIDTH)
                ball.speed.x *= -1;
            if (ball.position.y - ball.radius <= 0)
                ball.speed.y *= -1;

            if (ball.position.y + ball.radius >= SCREEN_HEIGHT) {
                gameOver = true;
            }

            if (CheckCollisionCircleRec(ball.position, ball.radius, paddle.rect)) {
                if (ball.speed.y > 0) {
                    ball.speed.y *= -1;
                    ball.speed.x = (ball.position.x - (paddle.rect.x + paddle.rect.width / 2.0f)) / 10.0f;
                }
            }

            int activeBlocks = 0;
            for (int i = 0; i < TOTAL_BLOCKS; i++) {
                if (blocks[i].active) {
                    activeBlocks++;
                    if (CheckCollisionCircleRec(ball.position, ball.radius, blocks[i].rect)) {
                        blocks[i].active = false;
                        ball.speed.y *= -1;
                        score += 10;
                        break;
                    }
                }
            }

            if (activeBlocks == 0) gameWon = true;
        }

        BeginDrawing();
        ClearBackground(BLACK);

        if (!gameOver && !gameWon) {
            DrawRectangleRec(paddle.rect, LIGHTGRAY);
            DrawCircleV(ball.position, ball.radius, WHITE);

            for (int i = 0; i < TOTAL_BLOCKS; i++) {
                if (blocks[i].active) {
                    DrawRectangleRec(blocks[i].rect, blocks[i].color);
                }
            }

            DrawText(TextFormat("SCORE: %d", score), 20, 10, 20, RAYWHITE);
        } else if (gameOver) {
            DrawText("GAME OVER", SCREEN_WIDTH / 2 - 100, SCREEN_HEIGHT / 2 - 20, 40, RED);
            DrawText("Press ESC to Exit", SCREEN_WIDTH / 2 - 100, SCREEN_HEIGHT / 2 + 30, 20, GRAY);
        } else if (gameWon) {
            DrawText("YOU WIN!", SCREEN_WIDTH / 2 - 80, SCREEN_HEIGHT / 2 - 20, 40, GREEN);
            DrawText(TextFormat("FINAL SCORE: %d", score), SCREEN_WIDTH / 2 - 90, SCREEN_HEIGHT / 2 + 30, 20, WHITE);
        }

        EndDrawing();
    }

    CloseWindow();

    return 0;
}
