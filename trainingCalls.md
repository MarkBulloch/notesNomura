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

q)doubleSecondItem 10 20 30 40 50
10 40 30 40 50


q)dropLastTwoItems 1 2 3 4
1 2
q)dropLastTwoItems "abcde"
"abc"


q)transpose(1 2 3 4;10 20 30 40;100 200 300 400)
1 10 100
2 20 200
3 30 300
4 40 400



q)getIndexOf[`john`mike`pat`tim`harry;`pat]
2


q)firstDayOfNextMonth 2020.01.01
2020.02.01
q)firstDayOfNextMonth 2020.01.11
2020.02.01
q)firstDayOfNextMonth 2020.01.31
2020.02.01
q)firstDayOfNextMonth 2020.02.27
2020.03.01




q)whereDivisibleBy[1 3 5 6 10 20;5]
5 10 20


q)roundDown 4.1
4
q)roundDown 4.1 4.3 4.5 4.8 5.0
4 4 4 4 5



Projections


toFloat


  q)toFloat 1 2 3
  1 2 3f
isZero

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
isZeroLong
Expected output:

q)isZeroLong 0
1b
q)isZeroLong 1
0b
q)isZeroLong 0h
0b
q)isZeroLong 0i
0b
halve
Expected output:

q)halve 100
50f
q)halve 2 3 4 5
1 1.5 2 2.5
