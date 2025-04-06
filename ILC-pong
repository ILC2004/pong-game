import pygame
import random

#initialize pygame
pygame.init()

#screen settings
WIDTH, HEIGHT = 1000, 600
BALL_SPEED = 6
PADDLE_SPEED = 7
AI_SPEED = 5
WINNING_SCORE = 11  #first to 11 with a 2-point difference

WHITE = (255, 255, 255)
BLACK = (20, 20, 20)
GREEN = (34, 177, 76)
RED = (200, 50, 50)
HOVER_GREEN = (50, 200, 100)
HOVER_RED = (255, 80, 80)
BLUE = (50, 150, 255)

#create game window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Table Tennis Game")

#fonts
title_font = pygame.font.Font(None, 60)
button_font = pygame.font.Font(None, 40)
score_font = pygame.font.Font(None, 50)
message_font = pygame.font.Font(None, 80)

#buttons
button_width, button_height = 240, 60
player_vs_player_button = pygame.Rect(WIDTH // 2 - button_width // 2, 250, button_width, button_height)
player_vs_ai_button = pygame.Rect(WIDTH // 2 - button_width // 2, 350, button_width, button_height)

#paddle and ball
PADDLE_WIDTH, PADDLE_HEIGHT = 20, 100
BALL_SIZE = 20
left_paddle = pygame.Rect(50, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
right_paddle = pygame.Rect(WIDTH - 70, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
ball = pygame.Rect(WIDTH // 2 - BALL_SIZE // 2, HEIGHT // 2 - BALL_SIZE // 2, BALL_SIZE, BALL_SIZE)

#scores
left_score = 0
right_score = 0

# AI Mmde selection
single_player = None

#game loop control
running = True
game_started = False
game_over = False
clock = pygame.time.Clock()

def draw_gradient_background():
    """Creates a smooth gradient effect for the background."""
    for y in range(HEIGHT):
        color = (y // 5, y // 5, y // 5)  #dark to light gradient
        pygame.draw.line(screen, color, (0, y), (WIDTH, y))

def draw_text_centered(text, font, color, rect):
    """Renders text and centers it within the given rectangle."""
    text_surface = font.render(text, True, color)
    text_rect = text_surface.get_rect(center=rect.center)
    screen.blit(text_surface, text_rect)

def show_start_menu():
    """Displays the game start menu."""
    screen.fill(BLACK)
    draw_gradient_background()

    #title text
    title_text = title_font.render("Table Tennis - Choose Mode", True, WHITE)
    screen.blit(title_text, (WIDTH // 2 - title_text.get_width() // 2, 100))

    mouse_x, mouse_y = pygame.mouse.get_pos()

    #player vs player button
    button_color_pvp = HOVER_GREEN if player_vs_player_button.collidepoint((mouse_x, mouse_y)) else GREEN
    pygame.draw.rect(screen, button_color_pvp, player_vs_player_button, border_radius=10)
    draw_text_centered("Player vs Player", button_font, WHITE, player_vs_player_button)

    #player vs AI button
    button_color_ai = HOVER_RED if player_vs_ai_button.collidepoint((mouse_x, mouse_y)) else RED
    pygame.draw.rect(screen, button_color_ai, player_vs_ai_button, border_radius=10)
    draw_text_centered("Player vs AI", button_font, WHITE, player_vs_ai_button)

    pygame.display.flip()

def reset_game():
    """Resets ball and scores for a new round."""
    global ball_speed_x, ball_speed_y, game_over
    ball.x, ball.y = WIDTH // 2 - BALL_SIZE // 2, HEIGHT // 2 - BALL_SIZE // 2
    ball_speed_x = BALL_SPEED * random.choice((1, -1))
    ball_speed_y = BALL_SPEED * random.choice((1, -1))
    game_over = False

def ai_movement():
    """Moves the AI paddle to track the ball's movement."""
    if ball.centery > right_paddle.centery:
        right_paddle.y += AI_SPEED
    elif ball.centery < right_paddle.centery:
        right_paddle.y -= AI_SPEED
    right_paddle.y = max(0, min(HEIGHT - PADDLE_HEIGHT, right_paddle.y))

def check_winner():
    """Checks if a player has won with the 11-point rule."""
    global game_over
    if left_score >= WINNING_SCORE and left_score - right_score >= 2:
        game_over = True
        return "Player 1 Wins!"
    if right_score >= WINNING_SCORE and right_score - left_score >= 2:
        game_over = True
        return "Player 2 Wins!" if not single_player else "AI Wins!"
    return None

def display_winner_message(message):
    """Displays the winning message on screen."""
    screen.fill(BLACK)
    text_surface = message_font.render(message, True, WHITE)
    text_rect = text_surface.get_rect(center=(WIDTH // 2, HEIGHT // 2))
    screen.blit(text_surface, text_rect)
    pygame.display.flip()
    pygame.time.delay(3000)  #pause for 3 seconds before restarting

#start menu loop
while single_player is None:
    show_start_menu()
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = event.pos
            if player_vs_player_button.collidepoint(mouse_x, mouse_y):
                single_player = False  #multiplayer mode
            elif player_vs_ai_button.collidepoint(mouse_x, mouse_y):
                single_player = True  #single Player Mode (AI)

#reset game before starting
reset_game()

#game loop
while running:
    screen.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    #paddle & ball movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_w] and left_paddle.top > 0:
        left_paddle.y -= PADDLE_SPEED
    if keys[pygame.K_s] and left_paddle.bottom < HEIGHT:
        left_paddle.y += PADDLE_SPEED

    if not single_player:
        if keys[pygame.K_UP] and right_paddle.top > 0:
            right_paddle.y -= PADDLE_SPEED
        if keys[pygame.K_DOWN] and right_paddle.bottom < HEIGHT:
            right_paddle.y += PADDLE_SPEED
    else:
        ai_movement()

    #ball movement & collision
    ball.x += ball_speed_x
    ball.y += ball_speed_y
    if ball.top <= 0 or ball.bottom >= HEIGHT:
        ball_speed_y *= -1
    if ball.colliderect(left_paddle) or ball.colliderect(right_paddle):
        ball_speed_x *= -1.1

    if ball.left <= 0:
        right_score += 1
        reset_game()
    if ball.right >= WIDTH:
        left_score += 1
        reset_game()

    #checks winner
    winner_message = check_winner()
    if winner_message:
        display_winner_message(winner_message)
        left_score, right_score = 0, 0  #resets scores
        single_player = None  #returns to main menu

    #displays elements
    pygame.draw.rect(screen, WHITE, left_paddle)
    pygame.draw.rect(screen, WHITE, right_paddle)
    pygame.draw.ellipse(screen, BLUE, ball)
    pygame.draw.aaline(screen, WHITE, (WIDTH // 2, 0), (WIDTH // 2, HEIGHT))

    #display score
    score_text = score_font.render(f"{left_score}   {right_score}", True, WHITE)
    screen.blit(score_text, (WIDTH // 2 - 30, 20))

    pygame.display.flip()
    clock.tick(60)

    pygame.quit()


