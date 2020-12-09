```q
.panop.sendToInternalTp:{
    .panop.tmp.now:.proc.cp[];
    data:2 cut delete date,time,sym from .panop.tmp.data;
    .lg.o[`panop;"Publishing data to internal tickerplant"];
    .panop.tmp.interval:(60*10000000000)%count data;
    i:0;
    while[i<count data;
            if[.z.p>.panop.tmp.now+.panop.tmp.interval;
                if[not 100000 mod i+:1; .lg.o[`panop;"Published 100,000 rows of data"]];
                .panop.tmp.now+: .panop.tmp.interval;
                {.util.asyncsend[`tppublish;(`.u.upd;.discovery.feedId;.panop.tmp.t;value flip x);.panop.tmp.TpHandle]} data i;
                ];
            ];
 };
```
`mod` - returns the remainder of x%y e.g.
100 mod 10 returns 0
100 mod 11 returns 1

`if[not 100000 mod i+:1; .lg.o[`panop;"Published 100,000 rows of data"]]` - increment i by 1 and check if 100000 divisible by i - if true log out message
