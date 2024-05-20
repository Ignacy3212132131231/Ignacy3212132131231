import pygame
import random

# Inicializar Pygame
pygame.init()

# Definir colores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Dimensiones de la pantalla
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Crear la pantalla del juego
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Juego 2D Básico")

# Definir el reloj para controlar la velocidad de actualización de la pantalla
clock = pygame.time.Clock()

# Clase para el jugador
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(BLACK)
        self.rect = self.image.get_rect()
        self.rect.center = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2)
        self.speed_x = 0
        self.speed_y = 0

    def update(self):
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y

        # Mantener al jugador dentro de la pantalla
        if self.rect.right > SCREEN_WIDTH:
            self.rect.right = SCREEN_WIDTH
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.bottom > SCREEN_HEIGHT:
            self.rect.bottom = SCREEN_HEIGHT
        if self.rect.top < 0:
            self.rect.top = 0

# Clase para los obstáculos
class Obstacle(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(RED)
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, SCREEN_WIDTH - self.rect.width)
        self.rect.y = random.randint(-100, -40)
        self.speed_y = random.randint(1, 8)

    def update(self):
        self.rect.y += self.speed_y
        if self.rect.top > SCREEN_HEIGHT:
            self.rect.x = random.randint(0, SCREEN_WIDTH - self.rect.width)
            self.rect.y = random.randint(-100, -40)
            self.speed_y = random.randint(1, 8)

# Crear grupos de sprites
all_sprites = pygame.sprite.Group()
obstacles = pygame.sprite.Group()

# Crear el jugador y agregarlo al grupo de sprites
player = Player()
all_sprites.add(player)

# Crear obstáculos y agregarlos a los grupos de sprites
for i in range(10):
    obstacle = Obstacle()
    all_sprites.add(obstacle)
    obstacles.add(obstacle)

# Función principal del juego
def main():
    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    player.speed_x = -5
                elif event.key == pygame.K_RIGHT:
                    player.speed_x = 5
                elif event.key == pygame.K_UP:
                    player.speed_y = -5
                elif event.key == pygame.K_DOWN:
                    player.speed_y = 5
            elif event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    player.speed_x = 0
                elif event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                    player.speed_y = 0

        # Actualizar los sprites
        all_sprites.update()

        # Verificar colisiones
        if pygame.sprite.spritecollideany(player, obstacles):
            print("¡Juego terminado!")
            running = False

        # Dibujar / Renderizar
        screen.fill(WHITE)
        all_sprites.draw(screen)

        # Actualizar la pantalla
        pygame.display.flip()

        # Controlar la velocidad del bucle
        clock.tick(30)

    pygame.quit()

if __name__ == "__main__":
    main()
