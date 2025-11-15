from pygame import *
from random import randint



img_back = "location1.jpg"

img_hero = "images/right_1.png"
img_enemy = "mashroom1.png"
img_finish = "finish.png"



window = win_width = 700
win_height = 500
display.set_caption("")
display.set_mode ((win_width, win_height))

finish = False


font.init()

mixer.init()
mixer.music.load("muzyka-super-mario.mp3")
mixer.music.play(-1)
jump_sound = mixer. Sound("pryjok-mario.mp3")
game_over_sound = mixer. Sound("game-over-mario.mp3")
win_sound = mixer. Sound ("Stage Win.mp3")
coin_sound = mixer. Sound("moneta-v-mario.mp3")

class GameSprite (sprite. Sprite):

    def __init__(self, player_image, player_x, player_y, size_x, size_y, player_speed):
        sprite. Sprite.__init__(self)

        self.image = transform.scale(

image.load(player_image), (size_x, size_y))

        self.speed.player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):

        window.blit(self.image, (self.rect.x, self.rect.y))
class Player (GameSprite):

    def __init__(self, player_image, player_x, player_y, size_x, size_y, player_speed, right_images_paths, left_images_paths):
        super().__init__(player_image, player_x, player_y, size_x,size_y, player_speed)
        self.is_jumping = False
        self.is_in_air = False
        self.jump_height = 12
        self.jump_count = self.jump_height
        self.is_moving_right = False
        self.is_moving_left = False
        self.frame_index = 0
        self.animation_delay = 3
        self.delay_count = 0
        self.direction = "right"
        self.direction = "right"
        self.default_image = self.image.copy()
        self.images_right = [transform.scale(image.load(path), (size_x, size_y))
                            for path in right_images_paths]
        self.images_left = [transform.scale(image.load(path), (size_x, size_y))
                            for path in left_images_paths]
        self.ground_y = player_у

    def animate(self):
        self.delay_count += 1
        if self.delay_count >= self.animation_delay:
            self.delay_count = 0
            self.frame_index = (self.frame_index + 1) % len(self.images_right)
        if self.direction == "right":
            self.image = self.images_right[self.frame_index]
        elif self.direction == "left":
            self.image = self.images_left[self.frame_index]

        if not moved:

            self.image = self.default_image
            self.frame_index = 0
            self.delay_count = 0

    def jump(self):
        if self.is_jumping:
            if self.jump_count == self.jump_height:
                jump_sound.play()
                self.image = transform.scale(image.load(f"images/{self.direction}_4.png")
                                            (self.rect.width, self.rect.height))
            if self.jump_count >= self.jump_height:
                direction = 1
                if self.jump_count < 0:
                    direction = -1
                self.rect.y = (self.jump_count ** 2) * 0.3 * direction
                if self.rect.y > self.ground_y:
                    self.rect.y = self.ground_y
            self.jump_count = 1
        else:
            self.is_jumping = False
            self.jump_count = self.jump_height
            self.rect.y = self.ground_y
            self.image = transform.scale(image.load("images/right_1.png")
                                              (self.rect.width, self.rect.height))

class Background (GameSprite):
    def update (self, direction):
        self.rect.x += self.speed * direction
        if self.rect.right <= 0:
            self.rect.x = win_width
        elif self.rect.left >= win_width:
            self.rect.x = -win_width

def start():
    global background1, background2, finish_building, enemy1, platforms, coins, mario

    mario = Player(
        img_hero,
        15,
        win_height = 168,
        80,
        100,
        10,
        [f"images/right_{i}.png" for i in range(1, 5)],
        [f"images/left_{i}.png" for i in range(1, 5)]
    )

    background1 = Background (img_back, 0, 0, win_width, win_height, mario.speed)
    background2 = Background (img_back, win_width, 0, win_width, win_height, mario.speed)

start()

while run:
    for e in event.get():
        if e.type == QUIT:

            run = False
        elif e.type == KEYDOWN:

            if e.key == K_SPACE:
    if not mario.is_jumping and abs(mario.rect.y - mario.ground_y) < 5:

        mario.is_jumping = True
    if e.key == K_d:
        mario.is_moving_right = True
    if e.key == K_a:
        mario.is_moving_left = True

    elif e.type == KEYUP:
        if e.key == K_d:
            mario.is_moving_right = False
        if e.key == K_a:
            mario.is_moving_left = False
        if finish and e.key == K_r:
            finish = False
        start()
        mixer.music.play(-1)

    if not finish:
        pressed_keys = key.get_pressed()
    background1.reset()
    background2.reset()
    mario.update([background1, background2])
    mario.reset()
    mario.jump()
    if mario.is_moving_right:
        finish_building.rect.x = mario.speed
    if mario.is_moving_left:
        finish_building.rect.x += mario.speed

    display.update()
    time.delay(50)
