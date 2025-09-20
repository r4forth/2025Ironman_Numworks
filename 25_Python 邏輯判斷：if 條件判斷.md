# Python 邏輯判斷: if 條件判斷

## 判斷成績是否及格

```
# Pass or Fail Checker

# Input score
score = int(input("Enter your score: "))

# If-Else decision
if score >= 60:
    print("Pass")
else:
    print("Fail")

```

## 判斷開啟哪個水閥

Water Valve Controller
輸入一個數字代表水壓：

* 如果水壓 小於 30 → 開啟 Valve A
* 如果水壓 介於 30 和 70 → 開啟 Valve B
* 如果水壓 大於等於 70 → 開啟 Valve C

```
# Water Valve Controller

# Input water pressure
pressure = int(input("Enter water pressure: "))

# Decision with if-elif-else
if pressure < 30:
    print("Open Valve A")
elif pressure < 70:
    print("Open Valve B")
else:
    print("Open Valve C")


```