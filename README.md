# iitdreamer
# =============================
# 4K PYTHON GAME
# SPACE SHOOTER (Pygame)
# =============================

import pygame
import random
import math

# Initialize pygame
pygame.init()

# -----------------------------
# SCREEN SETTINGS (4K)
# -----------------------------
WIDTH = 3840
HEIGHT = 2160

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("4K Space Shooter")

clock = pygame.time.Clock()

# -----------------------------
# COLORS
# -----------------------------
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 50, 50)
GREEN = (50, 255, 50)
BLUE = (50, 150, 255)
YELLOW = (255, 255, 0)

# -----------------------------
# PLAYER
# -----------------------------
player_x = WIDTH // 2
player_y = HEIGHT - 200
player_speed = 15
player_size = 80

# -----------------------------
# BULLETS
# -----------------------------
bullets = []
bullet_speed = 25

# -----------------------------
# ENEMIES
# -----------------------------
enemies = []

for i in range(15):
    enemies.append([
        random.randint(100, WIDTH - 100),
        random.randint(50, 1000),
        random.randint(5, 12)
    ])

enemy_size = 70

# -----------------------------
# SCORE
# -----------------------------
score = 0
font = pygame.font.SysFont("Arial", 80)

# -----------------------------
# GAME LOOP
# -----------------------------
running = True

while running:

    clock.tick(120)

    # Background
    screen.fill(BLACK)

    # -------------------------
    # EVENTS
    # -------------------------
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Shoot bullet
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bullets.append([player_x, player_y])

    # -------------------------
    # MOVEMENT
    # -------------------------
    keys = pygame.key.get_pressed()

    if keys[pygame.K_LEFT]:
        player_x -= player_speed

    if keys[pygame.K_RIGHT]:
        player_x += player_speed

    if keys[pygame.K_UP]:
        player_y -= player_speed

    if keys[pygame.K_DOWN]:
        player_y += player_speed

    # Keep player inside screen
    player_x = max(0, min(WIDTH - player_size, player_x))
    player_y = max(0, min(HEIGHT - player_size, player_y))

    # -------------------------
    # DRAW PLAYER
    # -------------------------
    pygame.draw.rect(
        screen,
        BLUE,
        (player_x, player_y, player_size, player_size)
    )

    # -------------------------
    # BULLETS
    # -------------------------
    for bullet in bullets[:]:
        bullet[1] -= bullet_speed

        pygame.draw.rect(
            screen,
            YELLOW,
            (bullet[0] + 30, bullet[1], 20, 50)
        )

        # Remove off-screen bullets
        if bullet[1] < 0:
            bullets.remove(bullet)

    # -------------------------
    # ENEMIES
    # -------------------------
    for enemy in enemies:

        enemy[1] += enemy[2]

        pygame.draw.circle(
            screen,
            RED,
            (enemy[0], enemy[1]),
            enemy_size
        )

        # Respawn enemy
        if enemy[1] > HEIGHT:
            enemy[0] = random.randint(100, WIDTH - 100)
            enemy[1] = random.randint(-500, -100)
            enemy[2] = random.randint(5, 12)

        # Collision with bullets
        for bullet in bullets[:]:

            distance = math.sqrt(
                (enemy[0] - bullet[0]) ** 2 +
                (enemy[1] - bullet[1]) ** 2
            )

            if distance < enemy_size:

                score += 1

                enemy[0] = random.randint(100, WIDTH - 100)
                enemy[1] = random.randint(-500, -100)
                enemy[2] = random.randint(5, 12)

                if bullet in bullets:
                    bullets.remove(bullet)

    # -------------------------
    # SCORE DISPLAY
    # -------------------------
    score_text = font.render(
        f"Score: {score}",
        True,
        WHITE
    )

    screen.blit(score_text, (50, 50))

    # -------------------------
    # UPDATE DISPLAY
    # -------------------------
    pygame.display.update()

# Quit
pygame.quit()
