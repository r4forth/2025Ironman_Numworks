# Python 應用: 數學程式

## 牛頓法（Newton–Raphson）求方程根

解方程 f(x)=0。我們示範：

$f(x)=x^3−2x−5$

這個方程的實根約在 x≈2.09。

```
# Newton-Raphson root finder on NumWorks
# Solve f(x) = 0  for  f(x)=x^3 - 2x - 5

def f(x):
    return x**3 - 2*x - 5

def df(x):
    # derivative: 3x^2 - 2
    return 3*x*x - 2

def newton(x0, tol=1e-7, max_iter=50):
    x = x0
    for k in range(1, max_iter+1):
        d = df(x)
        if d == 0:
            print("Zero derivative at iter", k, "x =", x)
            return None
        x_new = x - f(x)/d
        err = abs(x_new - x)
        print("iter", k, "x =", x_new, "f(x) =", f(x_new), "err =", err)
        if err < tol:
            return x_new
        x = x_new
    print("Not converged within", max_iter, "iterations")
    return None

# Try with a reasonable initial guess
root = newton(2.0)
print("Root ≈", root)
```


## 程式重點講解

f(x): 你要解的目標函數。改它就能解別的方程。

df(x): 
𝑓
(
𝑥
)
f(x) 的導數。牛頓法需要導數來畫切線。

newton(x0, tol, max_iter)：

x0：初始猜測（很重要；離真正根太遠可能不收斂）。

每一步更新：
  
𝑥
𝑘
+
1
=
𝑥
𝑘
−
𝑓
(
𝑥
𝑘
)
𝑓
′
(
𝑥
𝑘
)
x
k+1
	​

=x
k
	​

−
f
′
(x
k
	​

)
f(x
k
	​

)
	​


tol：收斂門檻（相鄰兩次解的差小於它就停）。

max_iter：最多疊代次數，避免無限跑。

印出每次 iter 的 
𝑥
x、
𝑓
(
𝑥
)
f(x) 與誤差 err，便於教學觀察。


## 進位換算

```
#!/usr/bin/python3


def switch_base(base):
    switcher = {
        "2": "01",
        "8": "01234567",
        "10": "0123456789",
        "16": "0123456789ABCDEF"
    }
    return switcher.get(base, "Invalid base")


def convert_to_decimal(number_str, is_negative, in_base_str):
    array_len = len(number_str)
    number_array = []
    int_base = int(in_base_str)
    for e in number_str.upper():
        base_elements_array = switch_base(in_base_str)
        if e not in base_elements_array:
            raise Exception()
        else:
            number_array.append(base_elements_array.index(e))

    if not is_negative:
        number_array.reverse()
    exp = 0
    result = 0
    signal = 1
    if is_negative:
        signal = -1
        exp = 1
    for e in number_array:
        if exp >=0:
            exp = exp * signal
        exp_calc = e*(int_base**exp)
        print(e,'*',int_base,'E',exp,'=',exp_calc)
        result = exp_calc + result
        if exp < 0:
            exp = exp - 1
        else:
            exp = exp + 1
    return result


def convert_from_decimal(number_str, is_fraction, in_base_str):
    result_str_array = []
    int_num = 0
    frac = 0
    try:
        int_num = int(number_str)
    except:
        frac = float(number_str)
    base = int(in_base_str)
    if not is_fraction:
        while int_num != 0:
            pre_num = int(int_num/base)
            r = int(int_num%base)
            result_str_array.append(r)
            print(int_num,'/',base,':',r)
            int_num = pre_num
        result_str_array.reverse()
    else:
        result_str_array.append('.')
        for e in range(8):
            frac = frac - int(frac)
            pre_num = (frac * base)
            i = int(pre_num)
            result_str_array.append(i)
            print("%.2f" % frac, '*', base, ':', "%.2f" % pre_num, ':', i)
            frac = pre_num
    return result_str_array


def prepare_to_decimal():
    try:
        in_number_str = input("Please enter a number:\n")
        in_base_str = input("Please enter the number base:\n")
        result = 0
        split = in_number_str.split('.')
        if len(split) > 2:
            raise Exception()
        else:
            result = convert_to_decimal(split[0], False, in_base_str)
            if len(split) == 2:
                result = result + convert_to_decimal(split[1], True, in_base_str)
        print('Result:', result)
    except Exception as inst:
        print('The input is not a base '+in_base_str+' value.')


def prepare_from_decimal():
    try:
        in_number_str = input("Please enter a number:\n")
        in_base_str = input("Please enter the number base:\n")
        result = []
        split = in_number_str.split('.')
        if len(split) > 2:
            raise Exception()
        else:
            result = convert_from_decimal(split[0], False, in_base_str)
            if len(split) == 2:
                result = result + convert_from_decimal(in_number_str, True, in_base_str)
        str_result = ''
        for e in result:
            str_result = str_result + str(e)
        print('Result: ',str_result)
    except Exception as inst:
        print('The input is not a decimal value.')

def prepare_hex_to_binary():
    try:
        result = 0
        in_number_str = input("Hex number:\n")
        in_base_str = '16'
        split = in_number_str.split('.')
        if len(split) > 2:
            raise Exception()
        else:
            result = convert_to_decimal(split[0], False, in_base_str)
            if len(split) == 2:
                result = result + convert_to_decimal(split[1], True, in_base_str)

        in_number_str = str(result)
        in_base_str = '2'
        split = in_number_str.split('.')
        if len(split) > 2:
            raise Exception()
        else:
            result = convert_from_decimal(split[0], False, in_base_str)
            if len(split) == 2:
                result = result + convert_from_decimal(in_number_str, True, in_base_str)
        str_result = ''
        for e in result:
            str_result = str_result + str(e)
        print('Result:',str_result)
    except Exception as inst:
        print('The input is invalid.', type(inst))


def r():
    option = input("Options:\n[1] To Decimal\n[2] From Decimal\n[3] From Hex to Binary\n")
    if "1" in option:
        prepare_to_decimal()
    elif "2" in option:
        prepare_from_decimal()
    elif "3" in option:
        prepare_hex_to_binary()
    else:
        print("Invalid option.")
r()

```