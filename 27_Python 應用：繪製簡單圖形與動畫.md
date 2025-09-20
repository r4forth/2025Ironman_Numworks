# Python 應用: 繪製簡單圖形與動畫

## 烏龜畫圖

### 畫一個粉紅色的正方形

```
import turtle as t

# 設定畫筆顏色 (R, G, B)
t.color(255, 105, 180)   # 粉紅色 (HotPink)

# 畫一個正方形
for i in range(4):
    t.forward(80)   # 前進 80 像素
    t.right(90)     # 右轉 90 度

```


### 畫一個橘色的五邊形

```
# Filled orange pentagon with kandinsky (NumWorks)
import kandinsky as kd
import math

W, H = 320, 222
cx, cy = 160, 110     # center
r = 70                # radius
orange = kd.color(255, 165, 0)

# build regular pentagon vertices (clockwise), start pointing up
verts = []
for k in range(5):
    ang = math.radians(-90 + k * 72)  # -90 deg to point upward
    x = cx + r * math.cos(ang)
    y = cy - r * math.sin(ang)        # screen y goes downward
    verts.append((x, y))

# scanline fill
ymin = int(min(v[1] for v in verts))
ymax = int(max(v[1] for v in verts))

for y in range(ymin, ymax + 1):
    xs = []
    for i in range(5):
        x1, y1 = verts[i]
        x2, y2 = verts[(i + 1) % 5]
        if y1 == y2:
            continue
        if (y >= min(y1, y2)) and (y < max(y1, y2)):  # half-open to avoid double count
            x = x1 + (y - y1) * (x2 - x1) / (y2 - y1)
            xs.append(x)
    xs.sort()
    for j in range(0, len(xs), 2):
        x0 = int(math.ceil(xs[j]))
        x1 = int(math.floor(xs[j + 1]))
        if x1 >= x0:
            kd.fill_rect(x0, y, x1 - x0 + 1, 1, orange)

```

## 簡易動畫

小球在螢幕四周彈來彈去

```
import kandinsky as kd, time

# 螢幕大小
W, H = 320, 222
# 球的半徑
R = 8
# 球的位置與速度
x, y = 100, 100
vx, vy = 2, 3

# 顏色
ball = kd.color(255, 100, 100)  # 紅色球
bg   = kd.color(255, 255, 255)  # 白色背景

while True:
    # 清除畫面
    kd.fill_rect(0, 0, W, H, bg)
    # 畫球
    kd.fill_rect(x - R, y - R, 2*R, 2*R, ball)

    # 更新位置
    x += vx
    y += vy

    # 碰到左右邊界反彈
    if x - R < 0 or x + R > W:
        vx = -vx
    # 碰到上下邊界反彈
    if y - R < 0 or y + R > H:
        vy = -vy

    time.sleep(0.02)  # 控制速度

```