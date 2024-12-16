# Bochkom
Andrew Kravtsiv
import pygame
import random
import sys

# Ініціалізація Pygame
pygame.init()

# Параметри вікна
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Piggy's Adventure")

# Кольори
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
PINK = (255, 182, 193)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# FPS і таймер
FPS = 60
clock = pygame.time.Clock()

# Свинка (гравець)
pig_size = 50
pig_x = WIDTH // 2
pig_y = HEIGHT - pig_size - 10
pig_speed = 5

# Яблука
apple_size = 30
apples = [pygame.Rect(random.randint(0, WIDTH - apple_size), random.randint(-500, -50), apple_size, apple_size) for _ in range(10)]
apple_speed = 3

# Пастки
trap_size = 40
traps = [pygame.Rect(random.randint(0, WIDTH - trap_size), random.randint(-500, -50), trap_size, trap_size) for _ in range(5)]
trap_speed = 4

# Рахунок і життя
score = 0
lives = 3
font = pygame.font.SysFont(None, 35)

def display_stats():
    score_text = font.render(f"Score: {score}  Lives: {lives}", True, BLACK)
    screen.blit(score_text, (10, 10))

# Основний цикл гри
def game_loop():
    global pig_x, pig_y, score, lives

    running = True
    while running:
        screen.fill(WHITE)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Керування свинкою
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            pig_x -= pig_speed
        if keys[pygame.K_RIGHT]:
            pig_x += pig_speed

        # Обмеження руху свинки
        if pig_x < 0:
            pig_x = 0
        if pig_x > WIDTH - pig_size:
            pig_x = WIDTH - pig_size

        # Рух яблук
        for apple in apples:
            apple.y += apple_speed
            if apple.y > HEIGHT:
                apple.y = random.randint(-500, -50)
                apple.x = random.randint(0, WIDTH - apple_size)

        # Рух пасток
        for trap in traps:
            trap.y += trap_speed
            if trap.y > HEIGHT:
                trap.y = random.randint(-500, -50)
                trap.x = random.randint(0, WIDTH - trap_size)

        # Перевірка на зіткнення з яблуками
        pig_rect = pygame.Rect(pig_x, pig_y, pig_size, pig_size)
        for apple in apples[:]:
            if pig_rect.colliderect(apple):
                apples.remove(apple)
                apples.append(pygame.Rect(random.randint(0, WIDTH - apple_size), random.randint(-500, -50), apple_size, apple_size))
                score += 10

        # Перевірка на зіткнення з пастками
        for trap in traps[:]:
            if pig_rect.colliderect(trap):
                traps.remove(trap)
                traps.append(pygame.Rect(random.randint(0, WIDTH - trap_size), random.randint(-500, -50), trap_size, trap_size))
                lives -= 1

        # Кінець гри, якщо життя = 0
        if lives <= 0:
            running = False

        # Малювання свинки
        pygame.draw.rect(screen, PINK, pig_rect)

        # Малювання яблук
        for apple in apples:
            pygame.draw.ellipse(screen, GREEN, apple)

        # Малювання пасток
        for trap in traps:
            pygame.draw.rect(screen, RED, trap)

        # Відображення статистики
        display_stats()

        # Оновлення екрану
        pygame.display.flip()
        clock.tick(FPS)

    # Кінець гри
    screen.fill(WHITE)
    game_over_text = font.render("Game Over! Press R to Restart or Q to Quit", True, BLACK)
    screen.blit(game_over_text, (WIDTH // 2 - 200, HEIGHT // 2))
    pygame.display.flip()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    main()
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

def main():
    game_loop()

if __name__ == "__main__":
    main()
