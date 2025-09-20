# Python 迴圈使用: For_While 迴圈

## while 迴圈

模擬一個水箱從 0 公升開始注水，每次加 10 公升，直到水量達到或超過 100 公升，並在過程中顯示目前水量。

```
# Water Tank Filling Simulation

water = 0   # initial water level

while water < 100:
    water = water + 10
    print("Current water level:", water, "liters")

print("Tank is full!")
```

執行結果:

![](img/26_01.png)

## For 迴圈

請用 For 迴圈，計算 1 + 2 + 4 + .... + 2048 的值。

```
# Sum of powers of 2 (1 + 2 + 4 + ... + 2048)

total = 0

for i in range(12):   # 0 to 11
    total = total + 2**i

print("Sum =", total)
```

另外一個版本

```
# Sum of doubling numbers (1 + 2 + 4 + ... + 2048)
total = 0
num = 1   # start from 1

for i in range(12):   # repeat 12 times
    total = total + num
    num = num * 2     # double the number each step

print("Sum =", total)
```

## 九九乘法表

```
# Compact 9x9 table
for i in range(1, 10):
    for j in range(1, 10):
        print(i*j, end="\t")
    print()
```

輸出結果:

