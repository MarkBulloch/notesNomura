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
projecting the second param, will always cast to type float
```q
q)toFloat 1 2 3
1 2 3f
```
---
```q
isZero:=[0;]
```
projection created - is x=0, 1b if true, 0b if not
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
projection created to check if x is 0 and type long
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
projection created as divide takes two params so will always be divided by 2
```q
q)halve 2 3 4 5
1 1.5 2 2.5
```

# Cast
```q
fromDelimitedString:{y$x vs z}    
```

```q
fromDelimitedString[",";"SJF*";"JPM,100,4.5,test string"]
`JPM
100
4.5
"test string"
```
```q
toFullName:{`$" " sv string[x, y]} 
```

```q
toFullName[`John;`Murphy]
`John Murphy
```
```q
roundToNearest:{"j"$x}  
```
```q
q)roundToNearest 4.1 4.3 4.5 4.8 5.0
4 4 5 5 5
```


# Execution Control

```q
ensureStr:{$[10h=type x;x;string x]}           
```
```q
q)ensureStr"a"
,"a"                    // should return a string if passed a char
q)ensureStr"abc"
"abc"
q)ensureStr`abc
"abc"
q)ensureStr 2
,"2"
q)ensureStr 10
"10"
```
```q
isAllCaps:{x~upper x}
```

```q
isAllCaps"a"
0b
isAllCaps"A"
1b
isAllCaps"aaB"
0b
isAllCaps"BBB"
1b
isAllCaps"ABC_123"
1b
isAllCaps "ABC|~"
1b
```
```q
ensureStr:{$[10h=type x;x;string x]}
```
```q
q)ensureStr"a"
,"a"                    // should return a string if passed a char
q)ensureStr"abc"
"abc"
q)ensureStr`abc
"abc"
q)ensureStr 2
,"2"
q)ensureStr 10
"10"
```
```q
startsWith:{y~3#x}               
```
```q
startsWith["abcdef";"abc"]
1b
startsWith["abcdef";"bc"]
0b
```
