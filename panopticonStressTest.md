```q
    now:.z.p;
    interval:(60*10000000000)%count data;
    i:0;
    while[i<count data;
            if[.z.p>now+interval;
                if[not 100000 mod i+:1; show "Published 100,000 rows of data"];
                now+:interval;
                sendDataFunc data i;
                ];
            ];

```
`mod` - returns the remainder of x%y e.g.
100 mod 10 returns 0
100 mod 11 returns 1

``if[not 100000 mod i+:1; show "Published 100,000 rows of data"];`` - increment i by 1 and check if 100000 divisible by i - if true log out message
