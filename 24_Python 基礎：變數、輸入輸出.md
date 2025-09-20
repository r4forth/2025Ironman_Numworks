# Python 基礎: 變數、輸入輸出

## 便利商店買飲料找錢

小明去便利商店買一瓶飲料，飲料價格 25 元，他拿出一張 50 元的鈔票。請用 NumWorks 的 Python 計算機程式，輸入價格與付款金額，輸出找零是多少。

```
# Change Calculator

# Input price and payment
price = int(input("Enter price: "))
paid = int(input("Enter payment: "))

# Calculate change
change = paid - price

# Output result
print("Price:", price)
print("Payment:", paid)
print("Change:", change)

```


## BMI Calculator

輸入一個人的身高（公尺）和體重（公斤），計算出 BMI 
值，並印出結果。


```
# BMI Calculator

# Input height and weight
height = float(input("Enter height (m): "))
weight = float(input("Enter weight (kg): "))

# Calculate BMI
bmi = weight / (height * height)

# Output result
print("Height:", height, "m")
print("Weight:", weight, "kg")
print("BMI:", bmi)
```
