最核心的架構就是「遊戲循環」（Game Loop）。無論你是用 Unity、Unreal、Godot 還是原生開發（如 HTML5 Canvas / JavaScript），幾乎所有遊戲都離不開以下幾個基本結構：

1. 遊戲循環 (Game Loop)
這是所有遊戲的心臟。它會不斷地重複執行，通常每秒跑 60 次（即 60 FPS）。一個標準的循環包含三個主要步驟：處理輸入、更新狀態、渲染畫面。

用 JavaScript（HTML5 Canvas）作為簡單示範：

// 初始化遊戲資源
function init() {
    // 載入圖片、音效、設定初始變數
    loadAssets();
    resetGame();
}

// 1. 處理玩家輸入 (Input)
function handleInput() {
    // 檢查鍵盤、觸控或滑鼠事件
}

// 2. 更新遊戲狀態 (Update / Logic)
function update(deltaTime) {
    // 更新角色位置、碰撞檢測、分數計算、敵人 AI
    player.x += player.vx * deltaTime;
}

// 3. 渲染畫面 (Render / Draw)
function render() {
    // 清空畫面
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // 畫出背景、角色、UI
    drawBackground();
    drawPlayer();
}

// 遊戲主循環 (Game Loop)
let lastTime = 0;
function gameLoop(timestamp) {
    let deltaTime = (timestamp - lastTime) / 1000; // 計算時間差
    lastTime = timestamp;

    handleInput();
    update(deltaTime);
    render();

    requestAnimationFrame(gameLoop); // 持續循環
}

// 啟動遊戲
init();
requestAnimationFrame(gameLoop);







2. 基本模組與架構拆解
除了主循環外，一個結構良好的 Game App 通常會把程式碼拆成以下幾個模組：

狀態管理 (State Manager / Scene Manager)

管理遊戲不同的畫面階段，例如：LoadingScene（載入畫面）、MenuScene（主選單）、PlayScene（遊戲中）、GameOverScene（結算畫面）。

資源管理器 (Asset Manager)

集中管理圖片、音效、字型等檔案的載入與釋放，避免重複載入造成記憶體爆滿。

物理與碰撞系統 (Physics & Collision)

負責判斷物件之間有沒有碰撞（例如子彈有沒有打中敵人、角色有沒有踩到地面）。

輸入管理器 (Input Manager)

統一收集滑鼠點擊、鍵盤按鍵或手機螢幕觸控（Touch）的訊號，再傳給角色去執行動作。

3. 現成引擎的框架簡化
如果你使用現成的遊戲引擎，它們已經幫你把「遊戲循環」包裝好了，你只需要寫生命週期函數：

Unity (C#)


void Start() {
    // 相當於 init()，遊戲開始時執行一次
}

void Update() {
    // 相當於 update()，每一幀執行一次（處理輸入與邏輯）
}




Godot (GDScript)

func _ready():
    # 相當於 init()，節點進入樹狀結構時執行一次
    pass

func _process(delta):
    # 相當於 update()，每一幀執行一次
    pass





Python Pygame

用 Python 開發遊戲，最標準、最普及的函式庫是 Pygame。它已經幫你封裝好了視窗管理、繪圖、音效與事件處理。

以下是一個最基本、結構完整的 Pygame 遊戲框架。你可以直接複製這段程式碼來執行（前提是需要先安裝 Pygame：在終端機輸入 pip install pygame）。

Python Pygame 基礎框架程式碼

import pygame
import sys

# 1. 初始化 Pygame
pygame.init()

# 設定視窗大小與標題
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("My Python Game")

# 設定時脈（控制 FPS 畫面更新率）
clock = pygame.time.Clock()
FPS = 60

# 定義顏色 (RGB)
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# 遊戲變數（例如玩家初始位置）
player_x = 400
player_y = 300
player_speed = 5

# ==================== 遊戲主循環 (Game Loop) ====================
running = True
while running:
    
    # 2. 處理輸入與事件 (Input / Events)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False  # 點擊視窗關閉按鈕時離開遊戲

    # 取得連續按鍵狀態（適合處理角色移動）
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_x -= player_speed
    if keys[pygame.K_RIGHT]:
        player_x += player_speed
    if keys[pygame.K_UP]:
        player_y -= player_speed
    if keys[pygame.K_DOWN]:
        player_y += player_speed

    # 3. 更新遊戲邏輯 (Update / Logic)
    # 這裡可以寫敵人移動、碰撞檢測、分數計算等
    # (範例中玩家座標已經在上方按鍵時更新了)

    # 4. 渲染畫面 (Render / Draw)
    # A. 清空畫面（用白色填滿背景，蓋掉上一幀的殘影）
    screen.fill(WHITE)

    # B. 繪製遊戲物件（例如畫一個紅色的方塊代表玩家）
    pygame.draw.rect(screen, RED, (player_x, player_y, 50, 50))

    # C. 更新顯示畫面
    pygame.display.flip()

    # 5. 控制幀率 (Clock Tick)
    clock.tick(FPS)  # 確保遊戲以固定的 60 FPS 運行

# 離開遊戲
pygame.quit()
sys.exit()




框架重點解析
pygame.init() 與 pygame.quit()：分別負責啟動和安全關閉 Pygame 的所有模組。

pygame.time.Clock()：非常重要！沒有它，遊戲會以電腦最高效能狂奔，導致CPU過載且遊戲速度快到看不清。clock.tick(60) 會強迫畫面維持在每秒 60 幀。

pygame.event.get()：用來捕捉鍵盤點擊、滑鼠移動、視窗關閉等「瞬間事件」。

pygame.key.get_pressed()：用來偵測「持續按著」某個鍵不放（例如按住方向鍵移動角色）。

pygame.display.flip()：將剛剛畫好的所有畫面一次性更新到螢幕上（雙緩衝技術）。



食金幣遊戲完整框架（Python + Pygame）
這是一個包含玩家移動、金幣隨機生成、碰撞檢測與計分系統的「食金幣」遊戲完整程式碼。

你可以直接複製並執行（記得先安裝 Pygame：pip install pygame）。

完整程式碼


import pygame
import random
import sys

# 1. 初始化 Pygame
pygame.init()

# 設定視窗大小
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("食金幣小遊戲")

# 設定時脈與字體
clock = pygame.time.Clock()
FPS = 60
font = pygame.font.SysFont(None, 36) # 用於顯示分數

# 定義顏色 (RGB)
WHITE = (255, 255, 255)
BLUE = (0, 120, 255)
GOLD = (255, 215, 0)
BLACK = (0, 0, 0)

# 初始化玩家 (使用 Rect 來處理位置與碰撞)
player = pygame.Rect(400, 300, 40, 40)
player_speed = 6

# 初始化金幣
coin = pygame.Rect(0, 0, 20, 20)

def respawn_coin():
    """隨機生成金幣位置，避免超出視窗邊界"""
    coin.x = random.randint(50, SCREEN_WIDTH - 70)
    coin.y = random.randint(50, SCREEN_HEIGHT - 70)

respawn_coin() # 遊戲開始時生成第一枚金幣

score = 0

# ==================== 遊戲主循環 (Game Loop) ====================
running = True
while running:
    
    # 2. 處理輸入與事件 (Input / Events)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 取得連續按鍵狀態（控制玩家移動）
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player.left > 0:
        player.x -= player_speed
    if keys[pygame.K_RIGHT] and player.right < SCREEN_WIDTH:
        player.x += player_speed
    if keys[pygame.K_UP] and player.top > 0:
        player.y -= player_speed
    if keys[pygame.K_DOWN] and player.bottom < SCREEN_HEIGHT:
        player.y += player_speed

    # 3. 更新遊戲邏輯 (Update / Logic)
    # 碰撞檢測：檢查玩家是否碰到金幣 (colliderect 是 Pygame 內建的好用功能)
    if player.colliderect(coin):
        score += 1          # 分數加 1
        respawn_coin()      # 重新隨機生成金幣

    # 4. 渲染畫面 (Render / Draw)
    screen.fill(WHITE) # 清空背景

    # 繪製金幣 (黃色圓形)
    pygame.draw.circle(screen, GOLD, (coin.x + 10, coin.y + 10), 10)

    # 繪製玩家 (藍色方塊)
    pygame.draw.rect(screen, BLUE, player)

    # 渲染並顯示分數文字
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (20, 20))

    # 更新顯示畫面
    pygame.display.flip()

    # 5. 控制幀率
    clock.tick(FPS)

# 離開遊戲
pygame.quit()
sys.exit()


核心功能亮點解析
pygame.Rect 物件：

Pygame 的 Rect 非常適合用來處理 2D 遊戲。它內建了 x, y, width, height，還可以直接讀取邊界（如 player.left, player.right），用來做邊界限制非常方便。

內建碰撞檢測 (colliderect)：

player.colliderect(coin) 可以直接判斷兩個方塊有沒有重疊。如果有，就代表玩家「食」左金幣。

隨機生成 (random.randint)：

每次食完金幣後，呼叫 respawn_coin() 在畫面的隨機座標重新定位金幣。

文字渲染 (pygame.font)：

使用 font.render() 把分數轉成圖像，再用 screen.blit() 畫到畫面上，實現即時計分。






升級版：避敵食金幣生存戰


import pygame
import random
import sys

# 1. 初始化 Pygame
pygame.init()

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("避敵食金幣生存戰")

clock = pygame.time.Clock()
FPS = 60
font = pygame.font.SysFont(None, 36)

# 顏色定義 (RGB)
WHITE = (255, 255, 255)
BLUE = (0, 120, 255)
GOLD = (255, 215, 0)
RED = (230, 50, 50)
BLACK = (0, 0, 0)

# 玩家設定
player = pygame.Rect(400, 300, 35, 35)
player_speed = 5

# 金幣設定
coin = pygame.Rect(0, 0, 20, 20)
def respawn_coin():
    coin.x = random.randint(50, SCREEN_WIDTH - 70)
    coin.y = random.randint(50, SCREEN_HEIGHT - 70)
respawn_coin()

# 敵人設定（建立多個敵人，有各自既移動速度同方向）
enemies = []
for _ in range(4): # 初始生成 4 個敵人
    enemy_rect = pygame.Rect(random.randint(100, 700), random.randint(100, 500), 30, 30)
    # 隨機速度 (vx, vy)
    vx = random.choice([-3, 3])
    vy = random.choice([-3, 3])
    enemies.append({"rect": enemy_rect, "vx": vx, "vy": vy})

score = 0
game_over = False

# ==================== 遊戲主循環 ====================
running = True
while running:
    
    # 2. 處理事件
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if game_over and event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r: # 按 R 鍵重新開始
                player.topleft = (400, 300)
                score = 0
                game_over = False
                respawn_coin()

    if not game_over:
        # 玩家移動控制
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player.left > 0:
            player.x -= player_speed
        if keys[pygame.K_RIGHT] and player.right < SCREEN_WIDTH:
            player.x += player_speed
        if keys[pygame.K_UP] and player.top > 0:
            player.y -= player_speed
        if keys[pygame.K_DOWN] and player.bottom < SCREEN_HEIGHT:
            player.y += player_speed

        # 3. 更新遊戲邏輯
        # A. 檢查金幣碰撞
        if player.colliderect(coin):
            score += 10 # 食中金幣加 10 分
            respawn_coin()

        # B. 更新敵人位置與邊界反彈
        for e in enemies:
            e["rect"].x += e["vx"]
            e["rect"].y += e["vy"]
            
            # 撞到左右牆壁就反彈
            if e["rect"].left <= 0 or e["rect"].right >= SCREEN_WIDTH:
                e["vx"] *= -1
            # 撞到上下牆壁就反彈
            if e["rect"].top <= 0 or e["rect"].bottom >= SCREEN_HEIGHT:
                e["vy"] *= -1

            # C. 檢查玩家有冇撞到敵人 (Game Over)
            if player.colliderect(e["rect"]):
                game_over = True

    # 4. 渲染畫面
    screen.fill(WHITE)

    # 畫金幣
    pygame.draw.circle(screen, GOLD, (coin.x + 10, coin.y + 10), 10)

    # 畫敵人
    for e in enemies:
        pygame.draw.rect(screen, RED, e["rect"])

    # 畫玩家
    pygame.draw.rect(screen, BLUE, player)

    # 顯示分數
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (20, 20))

    # Game Over 畫面提示
    if game_over:
        over_text = font.render("GAME OVER! Press 'R' to Restart", True, RED)
        screen.blit(over_text, (SCREEN_WIDTH // 2 - 180, SCREEN_HEIGHT // 2))

    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()








經典打磚塊遊戲框架

import pygame
import sys

# 1. 初始化 Pygame
pygame.init()

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("經典打磚塊遊戲")

clock = pygame.time.Clock()
FPS = 60
font = pygame.font.SysFont(None, 36)

# 顏色定義
WHITE = (255, 255, 255)
BLUE = (0, 120, 255)
RED = (230, 50, 50)
BLACK = (0, 0, 0)
GRAY = (200, 200, 200)

# 玩家底板 (Paddle)
paddle = pygame.Rect(350, 550, 100, 15)
paddle_speed = 8

# 小球 (Ball)
ball = pygame.Rect(395, 300, 15, 15)
ball_vx = 4
ball_vy = -4

# 建立磚塊陣列 (Bricks)
bricks = []
for row in range(5):
    for col in range(8):
        brick = pygame.Rect(col * 95 + 40, row * 30 + 50, 85, 20)
        bricks.append(brick)

score = 0
game_over = False
game_win = False

# ==================== 遊戲主循環 ====================
running = True
while running:
    
    # 2. 處理事件
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if (game_over or game_win) and event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r: # 按 R 鍵重新開始
                paddle.topleft = (350, 550)
                ball.topleft = (395, 300)
                ball_vx = 4
                ball_vy = -4
                score = 0
                game_over = False
                game_win = False
                # 重新生成磚塊
                bricks = []
                for row in range(5):
                    for col in range(8):
                        bricks.append(pygame.Rect(col * 95 + 40, row * 30 + 50, 85, 20))

    if not game_over and not game_win:
        # 底板左右移動
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and paddle.left > 0:
            paddle.x -= paddle_speed
        if keys[pygame.K_RIGHT] and paddle.right < SCREEN_WIDTH:
            paddle.x += paddle_speed

        # 3. 更新遊戲邏輯
        # 小球移動
        ball.x += ball_vx
        ball.y += ball_vy

        # 左右牆壁反彈
        if ball.left <= 0 or ball.right >= SCREEN_WIDTH:
            ball_vx *= -1
        # 上方牆壁反彈
        if ball.top <= 0:
            ball_vy *= -1

        # 掉落底部 = Game Over
        if ball.bottom >= SCREEN_HEIGHT:
            game_over = True

        # 小球碰撞底板 (Paddle)
        if ball.colliderect(paddle):
            ball_vy *= -1
            # 稍微調整反彈角度（令遊戲更有趣）
            if ball.centerx < paddle.centerx:
                ball_vx = -abs(ball_vx)
            else:
                ball_vx = abs(ball_vx)

        # 小球碰撞磚塊 (Bricks)
        hit_index = ball.collidelist(bricks)
        if hit_index != -1:
            bricks.pop(hit_index) # 消除被撞到的磚塊
            ball_vy *= -1         # 球反彈
            score += 10

            # 檢查是否清空所有磚塊 (Win)
            if len(bricks) == 0:
                game_win = True

    # 4. 渲染畫面
    screen.fill(WHITE)

    # 畫底板
    pygame.draw.rect(screen, BLUE, paddle)

    # 畫小球
    pygame.draw.ellipse(screen, BLACK, ball)

    # 畫磚塊
    for b in bricks:
        pygame.draw.rect(screen, RED, b)

    # 顯示分數
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (20, 20))

    # 遊戲結束或勝利提示
    if game_over:
        over_text = font.render("GAME OVER! Press 'R' to Restart", True, RED)
        screen.blit(over_text, (SCREEN_WIDTH // 2 - 180, SCREEN_HEIGHT // 2))
    elif game_win:
        win_text = font.render("YOU WIN! Press 'R' to Restart", True, (0, 200, 0))
        screen.blit(win_text, (SCREEN_WIDTH // 2 - 160, SCREEN_HEIGHT // 2))

    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()







迷你食鬼

import pygame
import random
import sys

# 1. 初始化 Pygame
pygame.init()

SCREEN_WIDTH = 640
SCREEN_HEIGHT = 480
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Python 順暢版食鬼 (Pac-Man)")

clock = pygame.time.Clock()
FPS = 60
font = pygame.font.SysFont(None, 36)

# 顏色定義 (RGB)
BLACK = (0, 0, 0)
YELLOW = (255, 255, 0)
BLUE = (30, 30, 200)
WHITE = (240, 240, 240)
RED = (255, 50, 50)
CYAN = (0, 255, 255)
ORANGE = (255, 165, 0)

# 網格設定（40 像素一格，速度設為 2，非常滑順且好操控）
CELL_SIZE = 40

# 地圖代號：0 = 小豆子, 1 = 牆壁, 2 = 大力豆
map_grid = [
    [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
    [1, 2, 0, 0, 1, 0, 0, 1, 0, 0, 2, 1],
    [1, 0, 1, 0, 1, 0, 0, 1, 0, 1, 0, 1],
    [1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 0, 1],
    [1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1],
    [1, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 1],
    [1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
    [1, 2, 0, 0, 0, 0, 0, 0, 0, 0, 2, 1],
    [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
]

# 初始化豆子與大力豆清單
dots = []
power_pellets = []
for r in range(len(map_grid)):
    for c in range(len(map_grid[0])):
        if map_grid[r][c] == 0:
            dots.append([c, r])
        elif map_grid[r][c] == 2:
            power_pellets.append([c, r])

# 食鬼初始化
pacman_x = 1 * CELL_SIZE
pacman_y = 1 * CELL_SIZE
pacman_speed = 2  # 速度調慢，操控更得心應手
pacman_dx, pacman_dy = 0, 0
next_dx, next_dy = 0, 0

# 鬼魂初始化
ghost_x = 10 * CELL_SIZE
ghost_y = 7 * CELL_SIZE
ghost_speed = 2
ghost_dx, ghost_dy = -ghost_speed, 0

score = 0
power_timer = 0
game_over = False
game_win = False

def is_wall(pixel_x, pixel_y):
    """檢查某個像素座標是否會撞牆"""
    c = pixel_x // CELL_SIZE
    r = pixel_y // CELL_SIZE
    if 0 <= c < len(map_grid[0]) and 0 <= r < len(map_grid):
        return map_grid[r][c] == 1
    return True

# ==================== 遊戲主循環 ====================
running = True
while running:
    
    # 2. 處理事件
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if (game_over or game_win) and event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:  # 按 R 重啟
                pacman_x, pacman_y = 1 * CELL_SIZE, 1 * CELL_SIZE
                pacman_dx, pacman_dy = 0, 0
                next_dx, next_dy = 0, 0
                ghost_x, ghost_y = 10 * CELL_SIZE, 7 * CELL_SIZE
                score = 0
                power_timer = 0
                game_over = False
                game_win = False
                dots, power_pellets = [], []
                for r in range(len(map_grid)):
                    for c in range(len(map_grid[0])):
                        if map_grid[r][c] == 0:
                            dots.append([c, r])
                        elif map_grid[r][c] == 2:
                            power_pellets.append([c, r])

    if not game_over and not game_win:
        # 鍵盤輸入
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            next_dx, next_dy = -pacman_speed, 0
        elif keys[pygame.K_RIGHT]:
            next_dx, next_dy = pacman_speed, 0
        elif keys[pygame.K_UP]:
            next_dx, next_dy = 0, -pacman_speed
        elif keys[pygame.K_DOWN]:
            next_dx, next_dy = 0, pacman_speed

        # 3. 更新遊戲邏輯
        # 關鍵修正：如果玩家想「立即回頭」（按相反方向），直接允許轉向，唔使等格仔中心！
        if next_dx == -pacman_dx and next_dy == -pacman_dy and next_dx != 0 or next_dy == -pacman_dy and next_dy != 0:
            pacman_dx, pacman_dy = next_dx, next_dy

        # 當處於格仔正中心時，檢查是否可以轉向新方向
        if pacman_x % CELL_SIZE == 0 and pacman_y % CELL_SIZE == 0:
            test_x = pacman_x + next_dx
            test_y = pacman_y + next_dy
            if not is_wall(test_x, test_y):
                pacman_dx, pacman_dy = next_dx, next_dy
            
            # 如果目前前進方向係牆壁，停低
            if is_wall(pacman_x + pacman_dx, pacman_y + pacman_dy):
                pacman_dx, pacman_dy = 0, 0

        # 移動食鬼
        pacman_x += pacman_dx
        pacman_y += pacman_dy

        # 吃豆子檢測
        p_c = (pacman_x + CELL_SIZE // 2) // CELL_SIZE
        p_r = (pacman_y + CELL_SIZE // 2) // CELL_SIZE
        if [p_c, p_r] in dots:
            dots.remove([p_c, p_r])
            score += 10
            if len(dots) == 0 and len(power_pellets) == 0:
                game_win = True

        if [p_c, p_r] in power_pellets:
            power_pellets.remove([p_c, p_r])
            score += 50
            power_timer = 360  # 約 6 秒無敵時間
            if len(dots) == 0 and len(power_pellets) == 0:
                game_win = True

        if power_timer > 0:
            power_timer -= 1

        # 鬼魂移動
        if ghost_x % CELL_SIZE == 0 and ghost_y % CELL_SIZE == 0:
            possible_dirs = [(-ghost_speed, 0), (ghost_speed, 0), (0, -ghost_speed), (0, ghost_speed)]
            valid_dirs = [d for d in possible_dirs if not is_wall(ghost_x + d[0], ghost_y + d[1])]
            if valid_dirs:
                ghost_dx, ghost_dy = random.choice(valid_dirs)

        ghost_x += ghost_dx
        ghost_y += ghost_dy

        # 碰撞檢測
        pacman_rect = pygame.Rect(pacman_x + 6, pacman_y + 6, CELL_SIZE - 12, CELL_SIZE - 12)
        ghost_rect = pygame.Rect(ghost_x + 6, ghost_y + 6, CELL_SIZE - 12, CELL_SIZE - 12)

        if pacman_rect.colliderect(ghost_rect):
            if power_timer > 0:
                ghost_x = 10 * CELL_SIZE
                ghost_y = 7 * CELL_SIZE
                score += 200
            else:
                game_over = True

    # 4. 渲染畫面
    screen.fill(BLACK)

    for r in range(len(map_grid)):
        for c in range(len(map_grid[0])):
            if map_grid[r][c] == 1:
                pygame.draw.rect(screen, BLUE, (c * CELL_SIZE, r * CELL_SIZE, CELL_SIZE, CELL_SIZE))

    for d in dots:
        pygame.draw.circle(screen, WHITE, (d[0] * CELL_SIZE + CELL_SIZE // 2, d[1] * CELL_SIZE + CELL_SIZE // 2), 5)

    for p in power_pellets:
        pygame.draw.circle(screen, ORANGE, (p[0] * CELL_SIZE + CELL_SIZE // 2, p[1] * CELL_SIZE + CELL_SIZE // 2), 12)

    pygame.draw.circle(screen, YELLOW, (pacman_x + CELL_SIZE // 2, pacman_y + CELL_SIZE // 2), CELL_SIZE // 2 - 4)

    ghost_color = CYAN if power_timer > 0 else RED
    pygame.draw.circle(screen, ghost_color, (ghost_x + CELL_SIZE // 2, ghost_y + CELL_SIZE // 2), CELL_SIZE // 2 - 4)

    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (20, 10))
    if power_timer > 0:
        power_text = font.render(f"POWER TIME! ({power_timer // 60}s)", True, CYAN)
        screen.blit(power_text, (220, 10))

    if game_over:
        over_text = font.render("GAME OVER! Press 'R' to Restart", True, RED)
        screen.blit(over_text, (SCREEN_WIDTH // 2 - 190, SCREEN_HEIGHT // 2 - 20))
    elif game_win:
        win_text = font.render("YOU WIN! Press 'R' to Restart", True, (0, 255, 0))
        screen.blit(win_text, (SCREEN_WIDTH // 2 - 160, SCREEN_HEIGHT // 2 - 20))

    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()
