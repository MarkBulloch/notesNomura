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
Split string (z) where comma occurs (x), and cast to y ("SJF*") - pass in like this when casting a list
```q
fromDelimitedString[",";"SJF*";"JPM,100,4.5,test string"]
`JPM
100
4.5
"test string"
```
---
```q
toFullName:{`$" " sv string[x, y]} 
```
cast x and y to a string, then form one string with a space in the middle via " " sv, cast back to one symbol 
```q
toFullName[`John;`Murphy]
`John Murphy
```
---
```q
roundToNearest:{"j"$x}  
```
casting to a long will always round to the nearest
```q
q)roundToNearest 4.1 4.3 4.5 4.8 5.0
4 4 5 5 5
```
---

# Execution Control

```q
ensureStr:{$[10h=type x;x;string x]}           
```
if else statement - check the type of x is a string, if true then return x, if false then string x
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
---
```q
isAllCaps:{x~upper x}
```
check x matches the equivalent of upper (capital) x and return boolean
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
---
```q
startsWith:{y~3#x}               
```
check whether y (abc/bc) matches the first 3 letters of x (abcdef) - will return boolean
```q
startsWith["abcdef";"abc"]
1b
startsWith["abcdef";"bc"]
0b
```
---

# Iterators
```q
myMax:{(|/) x}
```
use `over` iterator along with the max `(|)` to find the maximum of the list
```q
myMax 3 6 2 9
9

myMax -6 -9 -11 4
4
```
---
```q
myMin:{(&/) x}
```
use `over` iterator along with the min `(&)` to find the minimum of the list
```q
myMin 3 6 2 9
2

myMin 17 -10 3
-10
```
---
```q
myCount:{1+ max rank x}
```
`rank` returns the indexes of where values would occur in a sorted list, getting the maximum index+1 will return the count of the list

```q
myCount 5 7 9
3

myCount "hi there"
8
```
---
```q
myDistinct:{key group x}
```
group x returns a dictionaries of distinct keys and their indexes, key this dictionary to just return the distinct values
```q
myDistinct 4 2 2 3 3 1
4 2 3 1
myDistinct "ctcttft"
"ctf"
```
---

# Dictionaries
```q
createDict:{x!y}
```
self explanatory - key!value
```q
createDict["kdb";1 2 3]
k| 1
d| 2
b| 3
```
---
```q
dictToTable:{$[all 1=count each value x;enlist x;flip x]}
```
if else statement - if the count of x=1 then enlist the dictionary to form a table, else flip the dictionary (count>1) to form a table
```q
dictToTable `k`d`b!0.1 0.2 0.3                                // works when values are atoms
k   d   b  
-----------
0.1 0.2 0.3
dictToTable `k`d`b!(0.1 1; 0.2 2; 0.3 3)                      //works when values are lists
k   d   b  
-----------
0.1 0.2 0.3
1   2   3
dictToTable `k`d`b!("ab";`this`is;1 2 )                      //works when values are other types
k   d   b  
-----------
a  this 1
b  is   2
```
---
```q
countHandles:{count each x}
```
count each dictionary value and return another dictionary
```q
countHandles`k`d`b!("HELLO_WORLD";`k`d`b;enlist 7)
k| 11
d| 3
b| 1
```
---
```q
removeHandles:{
    a:0;
    while[a<count key x;
        x[key[x][a]]:x[key[x][a]] except y;
        a+:1];
    x}
```
complex soloution which Ryan developed, try find simpler way to achieve this
```q
removeHandles["tqb"!(110 112i;enlist 110i;110 101i);110i]    // using integers in this example, should work the same for longs
t| ,112i
q| `int$()
b| ,101i

removeHandles["tqb"!(110 112i;enlist 110i;110 101i);110 112i]     // should work for list of handles also
t| `int$()
q| `int$()
b| ,101i
```
---
# Tables
Define the following two tables with the corresponding columns and types:

trade

time (timestamp)
sym (symbol)
size (long)
price (long)
side (symbol)
exchange (symbol)

quote
time (timestamp)
sym (symbol)
bid (long)
ask (long)
bidSize (long)
askSize (long)
exchange (symbol)
Define a variable BATCH and assign it the value 1b (true)

```q
trade:flip `time`sym`size`price`side`exchange!"psjjss"$\:()
quote:flip `time`sym`bid`ask`bidSize`askSize`exchange!"psjjjjs"\:()
BATCH:1b
```
---
```q
dictToTable:{$[all 1=count each value x;enlist x;flip x]}
upd:{[t;d] $[BATCH~1b;t insert d;dictToTable cols[t]!d]}
```
Uses code from dictionaries functions quiz
```q
BATCH:0b                                                                // start by testing in non-batch mode
upd[`trade;(2#.z.P;`ASD`IBM;300 400;4000 6000;`O`P;`N`D)]                    // test with two records
time                          sym size price side exchange                    // copy of table returned
----------------------------------------------------------
2016.03.19D12:17:50.871658000 ASD 300  4000  O    N
2016.03.19D12:17:50.871658000 IBM 400  6000  P    D
trade                                                                // underlying table should be unchanged
time sym size price side exchange
---------------------------------
```
---
```q
returnKeyedTable:{[t;k] count [k]!k xcols t}
```

```q
Example of Code:

returnKeyedTable[`trade;`sym]                                        // returns a keys version of the table
sym| time                          size price side exchange
---| ------------------------------------------------------
GE | 2016.04.22D15:02:31.169380000 150  1025  B    N
BP | 2016.04.22D15:02:31.169380000 210  1050  S    L

trade                                                                // but doesn't alter the underlying table (trade)
time                          sym size price side exchange
----------------------------------------------------------
2016.04.22D15:02:31.169380000 GE  150  1025  B    N
2016.04.22D15:02:31.169380000 BP  210  1050  S    L
```
---
```q
applyKeyToTable[y xkey x]
```
```q
applyKeyToTable[`trade;`sym]                    // alters the underlying table and just returns the table name
`trade

trade
sym| time                          size price side exchange
---| ------------------------------------------------------
GE | 2016.04.22D15:02:31.169380000 150  1025  B    N
BP | 2016.04.22D15:02:31.169380000 210  1050  S    L
```
# Joins
- simple join `,`
- join each (paiwise) `a,'b`

- left join reference table (LHS) must be keyed
- LJ will included null row values, inner join will not include nulls
- a Keyed time series table (LHS) will key the resulting join time table

- plus join - RHS table must be keyed and must not contain any non-keyed non-numeric cols

- inner join - null values/rows are ommited from the resultant join table

- union join - appends date to the end of the able, both tables must be unkeyed, if one is keyed an error is thrown
-includes nulls in the resultant table, must make sure the cols match up if they dont itll all be null and appened to end of table
 
- asof - mainly used to get the prevailing quote at the time of a trade, need to define the cols you want to join on
- wj - can run functions on the join e.g. min, max

Reference guide: https://code.kx.com/q/basics/joins/
