# 自製小遊戲

## 


```
# Tiny Tetris for NumWorks (kandinsky + ion)
import kandinsky as kd, ion, time, random, math

# ----- screen & board -----
W, H = 320, 222
MARGIN_X, MARGIN_Y = 10, 10
COLS, ROWS = 10, 20
CELL = 10  # 10 px -> board = 100x200
BOARD_W, BOARD_H = COLS*CELL, ROWS*CELL
PANEL_X = MARGIN_X + BOARD_W + 10

BG   = kd.color(245,245,245)
GRID = kd.color(220,220,220)
BORDER = kd.color(40,40,40)
TEXT = kd.color(0,0,0)

# colors for pieces
COLORS = [
  kd.color(0,0,0),            # empty
  kd.color(0,240,240),        # I
  kd.color(0,0,240),          # J
  kd.color(240,160,0),        # L
  kd.color(240,240,0),        # O
  kd.color(0,240,0),          # S
  kd.color(160,0,240),        # T
  kd.color(240,0,0)           # Z
]

# ----- pieces (rotation states as lists of (x,y)) -----
# each rotation within a 4x4 box
PIECES = {
  "I":[[(0,1),(1,1),(2,1),(3,1)],
       [(2,0),(2,1),(2,2),(2,3)],
       [(0,2),(1,2),(2,2),(3,2)],
       [(1,0),(1,1),(1,2),(1,3)]],
  "J":[[(0,0),(0,1),(1,1),(2,1)],
       [(1,0),(2,0),(1,1),(1,2)],
       [(0,1),(1,1),(2,1),(2,2)],
       [(1,0),(1,1),(0,2),(1,2)]],
  "L":[[(2,0),(0,1),(1,1),(2,1)],
       [(1,0),(1,1),(1,2),(2,2)],
       [(0,1),(1,1),(2,1),(0,2)],
       [(0,0),(1,0),(1,1),(1,2)]],
  "O":[[(1,0),(2,0),(1,1),(2,1)],
       [(1,0),(2,0),(1,1),(2,1)],
       [(1,0),(2,0),(1,1),(2,1)],
       [(1,0),(2,0),(1,1),(2,1)]],
  "S":[[(1,0),(2,0),(0,1),(1,1)],
       [(1,0),(1,1),(2,1),(2,2)],
       [(1,1),(2,1),(0,2),(1,2)],
       [(0,0),(0,1),(1,1),(1,2)]],
  "T":[[(1,0),(0,1),(1,1),(2,1)],
       [(1,0),(1,1),(2,1),(1,2)],
       [(0,1),(1,1),(2,1),(1,2)],
       [(1,0),(0,1),(1,1),(1,2)]],
  "Z":[[(0,0),(1,0),(1,1),(2,1)],
       [(2,0),(1,1),(2,1),(1,2)],
       [(0,1),(1,1),(1,2),(2,2)],
       [(1,0),(0,1),(1,1),(0,2)]],
}
NAMES = ["I","J","L","O","S","T","Z"]

# ----- board state -----
board = [[0 for _ in range(COLS)] for _ in range(ROWS)]
score = 0
lines_cleared = 0

def draw_cell(cx, cy, color):
  x = MARGIN_X + cx*CELL
  y = MARGIN_Y + cy*CELL
  kd.fill_rect(x, y, CELL, CELL, color)

def draw_board():
  kd.fill_rect(0,0,W,H,BG)
  # border
  kd.fill_rect(MARGIN_X-1, MARGIN_Y-1, BOARD_W+2, BOARD_H+2, BORDER)
  kd.fill_rect(MARGIN_X, MARGIN_Y, BOARD_W, BOARD_H, kd.color(255,255,255))
  # grid
  for c in range(COLS):
    for r in range(ROWS):
      kd.fill_rect(MARGIN_X + c*CELL, MARGIN_Y + r*CELL, CELL-1, CELL-1, GRID)
  # cells
  for r in range(ROWS):
    for c in range(COLS):
      if board[r][c]:
        draw_cell(c, r, COLORS[board[r][c]])
  # panel
  kd.draw_string("TETRIS", PANEL_X, 10, TEXT, BG)
  kd.draw_string("Score: "+str(score), PANEL_X, 30, TEXT, BG)
  kd.draw_string("Lines: "+str(lines_cleared), PANEL_X, 50, TEXT, BG)
  kd.draw_string("OK: rotate", PANEL_X, 80, TEXT, BG)
  kd.draw_string("< > move", PANEL_X, 95, TEXT, BG)
  kd.draw_string("v: soft drop", PANEL_X, 110, TEXT, BG)
  kd.draw_string("BACK: quit", PANEL_X, 125, TEXT, BG)

def collides(px, py, rot, name):
  shape = PIECES[name][rot]
  for (sx, sy) in shape:
    x = px + sx
    y = py + sy
    if x < 0 or x >= COLS or y >= ROWS:
      return True
    if y >= 0 and board[y][x]:
      return True
  return False

def lock_piece(px, py, rot, name, color_id):
  global board
  for (sx, sy) in PIECES[name][rot]:
    x = px + sx
    y = py + sy
    if 0 <= y < ROWS:
      board[y][x] = color_id

def clear_lines():
  global board, score, lines_cleared
  new_rows = []
  cleared = 0
  for r in range(ROWS):
    if 0 in board[r]:
      new_rows.append(board[r])
    else:
      cleared += 1
  for _ in range(cleared):
    new_rows.insert(0, [0]*COLS)
  if cleared:
    board = new_rows
    lines_cleared += cleared
    # simple scoring: 100/300/500/800
    gained = [0,100,300,500,800][cleared]
    return gained
  return 0

def spawn():
  name = random.choice(NAMES)
  rot = 0
  px, py = 3, -2  # start slightly above
  color_id = NAMES.index(name) + 1
  return px, py, rot, name, color_id

def draw_piece(px, py, rot, name, color_id):
  for (sx, sy) in PIECES[name][rot]:
    x = px + sx
    y = py + sy
    if y >= 0:
      draw_cell(x, y, COLORS[color_id])

def rotate_with_kick(px, py, rot, name, dir=1):
  new_rot = (rot + dir) % 4
  kicks = [0, -1, 1, -2, 2]
  for dx in kicks:
    if not collides(px+dx, py, new_rot, name):
      return px+dx, py, new_rot
  return px, py, rot

# ----- input repeat helpers -----
left_cd = right_cd = down_cd = 0
repeat_delay = 5

# ----- game loop -----
px, py, rot, name, color_id = spawn()
gravity_frames = 15
frame = 0
running = True

while running:
  # input
  if ion.keydown(ion.KEY_BACK):
    break

  # left/right with simple repeat
  if left_cd: left_cd -= 1
  if right_cd: right_cd -= 1
  if down_cd: down_cd -= 1

  if ion.keydown(ion.KEY_LEFT) and left_cd == 0:
    if not collides(px-1, py, rot, name):
      px -= 1
    left_cd = repeat_delay
  if ion.keydown(ion.KEY_RIGHT) and right_cd == 0:
    if not collides(px+1, py, rot, name):
      px += 1
    right_cd = repeat_delay
  if ion.keydown(ion.KEY_OK):
    px, py, rot = rotate_with_kick(px, py, rot, name, 1)
    # small debounce
    for _ in range(6):
      time.sleep(0.005)

  soft = ion.keydown(ion.KEY_DOWN)
  if soft and down_cd == 0:
    if not collides(px, py+1, rot, name):
      py += 1
      score += 1  # soft drop reward
    down_cd = 2

  # gravity
  frame += 1
  if frame % (5 if soft else gravity_frames) == 0:
    if not collides(px, py+1, rot, name):
      py += 1
    else:
      # lock
      lock_piece(px, py, rot, name, color_id)
      score += clear_lines()
      # new piece
      px, py, rot, name, color_id = spawn()
      if collides(px, py, rot, name):
        # game over
        draw_board()
        kd.draw_string("GAME OVER", PANEL_X, 160, kd.color(200,0,0), BG)
        kd.draw_string("Score: "+str(score), PANEL_X, 180, TEXT, BG)
        running = False

  # draw
  draw_board()
  if running:
    draw_piece(px, py, rot, name, color_id)

  time.sleep(0.02)

```