# Functions

```q
isWholeNumber:{type[x] in (-5h;-6h;-7h)}
```
Is type of x = short, int or long, if so 1b, else 0b
```q
q)isWholeNumber 4.5
0b
q)isWholeNumber 4
1b
q)isWholeNumber 4h
1b
q)isWholeNumber 4j
1b
q)isWholeNumber"a"
0b
```
---
```q
doubleSecondItem:{@[x;1;*;2]} 
```
`@` - ammend at the 1st index of x and double value.

```q
q)doubleSecondItem 10 20 30 40 50
10 40 30 40 50
```
---
```q
dropLastTwoItems:{-2_x}
```
`-2` - (last 2)    
`_` - drop/cut operator
```q
q)dropLastTwoItems 1 2 3 4
1 2
q)dropLastTwoItems "abcde"
"abc"
```
---
```q
transpose:{flip x}
```
flips the lists from horizontal to vertical
```q
q)transpose(1 2 3 4;10 20 30 40;100 200 300 400)
1 10 100
2 20 200
3 30 300
4 40 400
```
---
```q
getIndexOf:{x?y} OR getIndexOf:{where x=y}
```
`?` - find x in y  
`=` - to find match between x/y
 ```q
q)getIndexOf[`john`mike`pat`tim`harry;`pat]
2
```
---
```q
firstDayOfNextMonth:{"d"$1+"m"$x}
```
cast x (date) to a month e.g. 2020.01  
+1 to the date: 2020.01+1=02  
cast back to a day which will always give the first day of the month
```q
q)firstDayOfNextMonth 2020.01.01
2020.02.01
q)firstDayOfNextMonth 2020.01.11
2020.02.01
q)firstDayOfNextMonth 2020.01.31
2020.02.01
q)firstDayOfNextMonth 2020.02.27
2020.03.01
```
---
```q
whereDivisibleBy:{x where 0=x mod y}
```
`1 3 5 6 10 20 mod 5 = 1 3 0 1 0 0 `  
show x where `0=x`
```q
q)whereDivisibleBy[1 3 5 6 10 20;5]
5 10 20
```
---
```q
roundDown:{floor x}
```
`floor` operator will always round down
```q
q)roundDown 4.1
4
q)roundDown 4.1 4.3 4.5 4.8 5.0
4 4 4 4 5
```
---



# Projections

```q
toFloat:$["f";]
```

```q
q)toFloat 1 2 3
1 2 3f
```
---
```q
isZero:=[0;]
```

```q
q)isZero 0
1b
q)isZero 5
0b
q)isZero 0h
1b
q)isZero 0x00
1b
q)isZero 0b
1b
```
---
```q
isZeroLong:~[0j;]
```

```q
q)isZeroLong 0
1b
q)isZeroLong 1
0b
q)isZeroLong 0h
0b
q)isZeroLong 0i
0b
```
---
```q
halve:%[;2]
```

```q
q)halve 2 3 4 5
1 1.5 2 2.5
```
