author: Jonathan Wong
summary:
id: TetrisCodeLab
tags:
categories:
environments: Web
status: Published
feedback link: https://github.com/SolaceDev/solace-dev-codelabs/blob/master/markdown/TetrisCodeLab

# Title of codelab goes here

## What you'll learn: Overview

Duration: 0:05:00

Enter a codelab overview here: what & why and github repo link where you can find related code if applicable

### Info Boxes
Plain Text followed by green & yellow info boxes 

> aside negative
> This will appear in a yellow info box.

> aside positive
> This will appear in a green info box.

### Bullets
Plain Text followed by bullets
* Hello
* CodeLab
* World

### Numbered List
1. List
1. Using
1. Numbers

### Add an Image or a GIF

![Soly Image Caption](img/soly.gif)

## What you need: Prerequisites

Duration: 0:07:00

Enter environment setup & prerequisites here

### Add a Link
Add a link!
[Example of a Link](https://www.google.com)

### Embed an iframe

![https://codepen.io/tzoght/embed/yRNZaP](https://en.wikipedia.org/wiki/File:Example.jpg "Try Me Publisher")

## Custom Step 1

### Step 1
```
import pygame
import sys
from copy import deepcopy

WIDTH, HEIGHT = 10, 20;
TILE = 45;
GAME_RES = (WIDTH * TILE, HEIGHT * TILE)

pygame.init()
screen = pygame.display.set_mode(GAME_RES)
pygame.display.set_caption("Tetris")

while True:
    screen.fill(pygame.Color("black"))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    pygame.display.update()
    CLOCK.tick(60)
```

### Step 2 making grid
```
pygame.init()
screen = pygame.display.set_mode(GAME_RES)
pygame.display.set_caption("Tetris")

# NEW CODE HERE
GRID = [pygame.Rect(x * TILE, y * TILE, TILE, TILE) for x in range(WIDTH) for y in range(HEIGHT)] 
```

```
for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
    # NEW CODE HERE
    [pygame.draw.rect(screen, (40,40,40), i_rect, 1) for i_rect in GRID]
```

### Step 3 Making Blocks
Empty 2D array for Tetromino
```
tetrominos_pos = [[(),(),(),()],
              [(),(),(),()],
              [(),(),(),()],
              [(),(),(),()],
              [(),(),(),()],
              [(),(),(),()],
              [(),(),(),()]]
```
Filled 2D array of Tetromino
```
tetrominos_pos = [[(-1,0),(-2,0),(0,0),(1,0)], # I
              [(0,-1),(-1,-1),(-1,0),(0,0)], # O
              [(-1,-1),(0,-1),(0,0),(1,0)], # Z
              [(-1,0),(0,0),(0,-1),(1,-1)], # S
              [(-1,0),(0,0),(1,0),(1,-1)], # L
              [(-1,-1),(-1,0),(0,0),(1,0)], # J
              [(-1,0),(0,0),(1,0),(0,-1)]] # T

while True:
```

```
tetrominos_pos = [[...]]

# NEW CODE HERE
tetrominos = [[pygame.Rect(x + WIDTH // 2, y +1, 1, 1) for x, y in block_pos] for block_pos in tetrominos_pos]
tetromino_rect = pygame.Rect(0,0, TILE -2, TILE -2)

tetromino = tetrominos[0]
#NEW CODE END

while True:
```
### Drawing the Tetromoino
```
# NEW CODE HERE
for i in range(4):
        tetromino_rect.x = tetromino[i].x * TILE
        tetromino_rect.y = tetromino[i].y * TILE
        pygame.draw.rect(screen, pygame.Color("white"), tetromino_rect)
# NEW CODE END

pygame.display.update()
    CLOCK.tick(60)
```

### Step 4 Moving Blocks
```
while True:
    screen.fill(pygame.Color("black"))
    
    dir_x = 0       # NEW CODE HERE

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        # NEW CODE HERE
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                dir_x = -1
            elif event.key == pygame.K_RIGHT:
                dir_x = 1

        for i in range(4):
            tetromino[i].x += dir_x
        # NEW CODE END
```

### Step 5 adding borders
Add new function
```
def check_borders():
    if tetromino[i].x < 0 or tetromino[i].x > WIDTH-1:
        return False
    return True
```

Because tetromino is not a real python class, we are making a psudo-class with deepcopy and, using the , load the copy when we hit it and removing the tetromino that passed the borders, disallowing it 
```
tetrominos = [[pygame.Rect(x + WIDTH // 2, y +1, 1, 1) for x, y in block_pos] for block_pos in tetrominos_pos]
tetromino_rect = pygame.Rect(0,0, TILE -2, TILE -2)

tetromino = deepcopy(tetrominos[0])     # MODIFED CODE HERE

while True:
```

```
tetromino_old = deepcopy(tetromino)
for i in range(4):
    tetromino[i].x += dir_x
    if not check_borders():
        tetromino = deepcopy(tetromino_old)
        break
```

### STEP whatever Moving tetromino downwards
```
tetrominos = [[pygame.Rect(x + WIDTH // 2, y +1, 1, 1) for x, y in block_pos] for block_pos in tetrominos_pos]
tetromino_rect = pygame.Rect(0,0, TILE -2, TILE -2)

tetromino = deepcopy(tetrominos[0])     

anim_count, anim_speed, anim_limit = 0, 60, 2000        # NEW CODE HERE

def check_borders():
```

```
anim_count += anim_speed
    if anim_count > anim_limit:
        anim_count = 0
        tetromino_old = deepcopy(tetromino)
        for i in range(4):
            tetromino[i].y += 1
```

adding increased drop speed
```
if event.type == pygame.KEYDOWN:
    if event.key == pygame.K_LEFT:
        dir_x = -1
    elif event.key == pygame.K_RIGHT:
        dir_x = 1
    
    # NEW CODE HERE
    elif event.key == pygame.K_DOWN:
        anim_limit = 100
```

### Re-applying anim limit on key up
```
    elif event.key == pygame.K_DOWN:
                anim_limit = 100
# NEW CODE HERE
elif event.type == pygame.KEYUP:
    if event.key == pygame.K_DOWN:
        anim_limit = 2000
```

Making tetrominos random
```
import pygame
import sys
from copy import deepcopy
from random import choice, randrange      # NEW CODE
```

```
tetrominos = [[pygame.Rect(x + WIDTH // 2, y +1, 1, 1) for x, y in block_pos] for block_pos in tetrominos_pos]
tetromino_rect = pygame.Rect(0,0, TILE -2, TILE -2)
tetromino = deepcopy(choice(tetrominos))    # MODIFIED CODE
```

### STOPING FIGURE at bottom

```
tetrominos = [[pygame.Rect(x + WIDTH // 2, y +1, 1, 1) for x, y in block_pos] for block_pos in tetrominos_pos]
tetromino_rect = pygame.Rect(0,0, TILE -2, TILE -2)
tetromino = deepcopy(choice(tetrominos))
# NEW CODE HERE
feild = [[0 for i in range(WIDTH)] for j in range(HEIGHT)]      
```

In Y movement
```
for i in range(4):
    tetromino[i].y += 1

    # NEW CODE HERE
    if not check_borders():
        tetromino = deepcopy(choice(tetromins))
        break
```

modify border check
```
def check_borders():
    if tetromino[i].x < 0 or tetromino[i].x > WIDTH-1:
        return False
    elif tetromino[i].y > HEIGHT -1 or feild[tetromino[i].y][tetromino[i].x]:
        return False
    return True
```

In Y movement
```
for i in range(4):
    tetromino[i].y += 1
    if not check_borders():
        # NEW CODE HERE
        for i in range(4):
            feild[tetromino[i].y][tetromino[i].x] = pygame.Color('white')
        # NEW CODE END
        tetromino = deepcopy(choice(tetrominos))
        break
```

```
# Drawing Feild (the bottom the tetrominos that is touching bottom)
for y, row in enumerate(feild):
    for x, col in enumerate(row):
        if col:
            tetromino_rect.x, tetromino_rect.y = x * TILE, y * TILE
            pygame.draw.rect(screen, col, tetromino_rect)

pygame.display.update()
CLOCK.tick(60)
```

### ROTATING 
```
rotate = False      # Create a new var

for event in pygame.event.get(): ...
    if event.type == pygame.KEYDOWN: ...
        elif event.key == pygame.K_DOWN:
            anim_limit = 100
        # NEW CODE HERE
        elif event.key == pygame.K_UP:      
            rotate = True
```

Rotating logic
```
center = tetromino[0]
tetromino_old = deepcopy(tetromino)
if rotate:
    for i in range(4):
        x = tetromino[i].y - center.y
        y = tetromino[i].x - center.x
        tetromino[i].x = center.x - x
        tetromino[i].y = center.y + y
        if not check_borders():
            tetromino = deepcopy(tetromino_old)
            break
```

### Removing full lines

```
line = HEIGHT - 1
for row in range(HEIGHT - 1, -1, -1):
    count = 0
    for i in range(WIDTH):
        if feild[row][i]:
            count += 1
        feild[line][i] = feild[row][i]
    if count < WIDTH:
        line -= 1
```
Tetris core game done

## make it more good looking
### Modify game window
Change screen to a surface and make a new screen
```
WIDTH, HEIGHT = 10, 18;
TILE = 45;
GAME_RES = (WIDTH * TILE, HEIGHT * TILE)
RES = 750, 940          # NEW CODE HERE
```

```
sc = pygame.display.set_mode(RES)
screen = pygame.Surface(GAME_RES)
```

adding backgrounds
```
anim_count, anim_speed, anim_limit = 0, 60, 2000

# NEW CODE HERE
bg = pygame.image.load('img/bg.jpg').convert()
game_bg = pygame.image.load('img/bg2.jpg').convert()


def check_borders():
```

```
while True:
    // screen.fill(pygame.Color("black"))  # REMOVE THIS LINE
    dir_x = 0
    rotate = False

    # NEW CODE HERE
    sc.blit(bg, (0,0))
    sc.blit(screen, (20,20))
    screen.blit(game_bg, (0,0))
```

### Coloring tiles
add a 5th element for the tiles, new element represent the RGB value of the block shape
```
tetrominos_pos = [[(-1,-1),(-2,-1),(0,-1),(1,-1), (0,173,238)], # I
              [(0,-1),(-1,-1),(-1,0),(0,0), (255,241,0)], # O
              [(0,0),(-1,-1),(0,-1),(1,0), (236,27,36)], # Z
              [(0,0),(-1,0),(0,-1),(1,-1), (139,197,63)], # S
              [(0,0),(-1,0),(1,0),(1,-1), (246, 146, 30)], # L
              [(0,0),(-1,-1),(-1,0),(1,0), (27, 116, 187)], # J
              [(0,0),(-1,0),(1,0),(0,-1), (101,45,144)]] # T

# Change block_pos to block_pos[:-1], and add + [block_pos[-1]]
tetrominos = [
    [pygame.Rect(x + WIDTH // 2, y + 1, 1, 1) for x, y in block_pos[:-1]] + [block_pos[-1]]
    for block_pos in tetrominos_pos
]

```

Adjust code, instead of drawing white use the color value
Under Y movement
```
if not check_borders():
    for i in range(4):
        feild[tetromino_old[i].y][tetromino_old[i].x] = tetromino[4]        # MODIFY HERE
    tetromino = deepcopy(choice(tetrominos))
    break
```

Under Drawing Block
```
for i in range(4):
        tetromino_rect.x = tetromino[i].x * TILE
        tetromino_rect.y = tetromino[i].y * TILE
        pygame.draw.rect(screen, tetromino[4], tetromino_rect)
```

## Custom Step 2
## Custom Step 3

## Takeaways

Duration: 0:07:00

✅ < Fill IN TAKEAWAY 1>   
✅ < Fill IN TAKEAWAY 2>   
✅ < Fill IN TAKEAWAY 3>   

![Soly Image Caption](img/soly.gif)

Thanks for participating in this codelab! Let us know what you thought in the [Solace Community Forum](https://solace.community/)! If you found any issues along the way we'd appreciate it if you'd raise them by clicking the Report a mistake button at the bottom left of this codelab.
