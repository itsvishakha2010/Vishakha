
from tkinter import *
import random

GAME_WIDTH = 500
GAME_HEIGHT = 500
SPEED = 100
SPACE_SIZE = 50
BODY_PARTS = 3

SNAKE_COLOUR = "#1010E0"
FOOD_COLOUR = "#D61212"
BACKGROUND_COLOUR = "#D6C615"

direction = "down"
score = 0

window = Tk()
window.title("Snake Game")
window.resizable(False, False)

label = Label(window, text="Score: 0", font=('consolas', 40))
label.pack()

canvas = Canvas(window, bg=BACKGROUND_COLOUR,
                height=GAME_HEIGHT, width=GAME_WIDTH)
canvas.pack()

class Snake:
    def __init__(self):
        self.coordinates = []
        self.squares = []
        start_x = GAME_WIDTH // 2
        start_y = GAME_HEIGHT // 2        

        for i in range(BODY_PARTS):
            self.coordinates.append([start_x,start_y+i*SPACE_SIZE ])

        for x, y in self.coordinates:
            square = canvas.create_rectangle(
                x, y, x + SPACE_SIZE, y + SPACE_SIZE,
                fill=SNAKE_COLOUR
            )
            self.squares.append(square)

class Food:
    def __init__(self):
        x = random.randint(0, (GAME_WIDTH // SPACE_SIZE) - 1) * SPACE_SIZE
        y = random.randint(0, (GAME_HEIGHT // SPACE_SIZE) - 1) * SPACE_SIZE

        self.coordinates = [x, y]

        canvas.create_oval(
            x, y, x + SPACE_SIZE, y + SPACE_SIZE,
            fill=FOOD_COLOUR, tag="food"
        )

def next_turn(snake, food):
    global score

    x, y = snake.coordinates[0]

    if direction == "up":
        y -= SPACE_SIZE
    elif direction == "down":
        y += SPACE_SIZE
    elif direction == "left":
        x -= SPACE_SIZE
    elif direction == "right":
        x += SPACE_SIZE

    snake.coordinates.insert(0, [x, y])
    square = canvas.create_rectangle(
        x, y, x + SPACE_SIZE, y + SPACE_SIZE,
        fill=SNAKE_COLOUR
    )
    snake.squares.insert(0, square)

    if x == food.coordinates[0] and y == food.coordinates[1]:
        score += 1
        label.config(text=f"Score: {score}")
        canvas.delete("food")
        food = Food()
    else:
        del snake.coordinates[-1]
        canvas.delete(snake.squares[-1])
        del snake.squares[-1]

    window.after(SPEED, next_turn, snake, food)

def change_direction(new_direction):
    global direction

    if new_direction == "left" and direction != "right":
        direction = new_direction
    elif new_direction == "right" and direction != "left":
        direction = new_direction
    elif new_direction == "up" and direction != "down":
        direction = new_direction
    elif new_direction == "down" and direction != "up":
        direction = new_direction

window.bind('<Left>', lambda e: change_direction("left"))
window.bind('<Right>', lambda e: change_direction("right"))
window.bind('<Up>', lambda e: change_direction("up"))
window.bind('<Down>', lambda e: change_direction("down"))

snake = Snake()
food = Food()

next_turn(snake, food)

window.mainloop()
