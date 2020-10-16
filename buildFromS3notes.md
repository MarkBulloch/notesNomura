.hk.buildFromS3.run:{[dt;tab;function]
    $[system"e";
        .hk.buildFromS3.runInner[dt;tab;function];
        .Q.trp[.hk.buildFromS3.runInner[dt;tab]; function; .hk.buildFromS3.handleError]];
 };
 
 $[system"e";] - if else statement, system"e" enables error trapping for client requests:
0	When a client request has an error, by default the server clears the stack. Appropriate for production use as it enables the server to continue processing other client requests.
1	The server suspends on an error, and does not process other requests until the stack is cleared. Appropriate for development: enables debugging on the server.

.Q.trp - is an extended form of trap which collects the back trace - will trap the error and run the .handleError logic if requiredS
 

 .hk.buildFromS3.replayTplog:{[tplog]
    / create new log
    newTplog:.hk.buildFromS3.setUpNewLog tplog;
    / replayLogs
    .lg.o[`buildFromS3; "Replaying ", string tplog];
    -11!tplog;
    hclose .hk.tmp.tplogHandle;
    newTplog
 };

 newTplog assingnment - calls the .setUpNewLog function passing in the tplog as an argument
 .lg.o - log out message to console e.g. above will print Replaying logfileName
 11! - replay tplog 
 hclose - close the handle to the tplog
 newTplog - without semi colon is standard to display the file to the console
 
 .hk.buildFromS3.setUpNewLog:{[x]
    name: ` vs last ` vs x;
    name: ` sv name[0 1],(`$string[name 2],"-buildFromS3"),.proc.procname,.z.h,`$.util.timestampToString .z.p;
    .lg.o[`buildFromS3; "Setting up ", string name];
    tplog: .Q.dd[first ` vs x] name;
    tplog set ();
    .hk.tmp.tplogHandle: hopen tplog;
    tplog
 };
 
 x - /mnt/dev_env/data/tplog/2019.05.06/genhousekeeping-1/APATrade.1184589.tickerplant-9.ctp.woklxd00318.20190506D012858688779000
 
 last ` vs x - `APATrade.1184589.tickerplant-9.ctp.woklxd00318.20190506D012858688779000 (one symbol)
 name:` vs last ` vs x - `APATrade`1184589`tickerplant-9`ctp`woklxd00318`20190506D012858688779000 (multiple symbols)
 name[0 1] - `APATrade`1184589 (first two symbols)
 
 (`$string[name 2],"-buildFromS3") -`tickerplant-9-buildFromS3
 .proc.procname - `genhousekeeping-1
 .z.h - `i-03c02d12467ecdbd8.eu-west-1.aws.nomura.com
 `$.util.timestampToString .z.p - `20201015D143634092250000
 
 name: ` sv name[0 1],(`$string[name 2],"-buildFromS3"),.proc.procname,.z.h,`$.util.timestampToString .z.p; - 
 `APATrade.1184589.tickerplant-9-buildFromS3.genhousekeeping-1.i-03c02d12467ecdbd8.eu-west-1.aws.nomura.com.20201015D143758080556000
 
 .Q.dd - Shorthand for ` sv x,`$string y. Useful for creating filepaths, suffixed stock symbols, etc.
 [first ` vs x] - `:/mnt/dev_env/data/tplog/2019.05.06/genhousekeeping-1
 
 creates one joined symbol - .Q.dd[first ` vs x] name; - `:/mnt/dev_env/data/tplog/2019.05.06/genhousekeeping-1/APATrade.1184589.tickerplant-9-buildFromS3.genhousekeeping-1.i-03c02d12467ecdbd8.eu-west-1.aws.nomura.com.20201015D143758080556000
 
 hopen tplog - open handle to tplog


 .hk.buildFromS3.regCols:{[t;x]
    / create empty table
    .lg.o[`buildFromS3;"Creating Empty Table"];
    .hk.tmp.buffer:flip x;
    .hk.tmp.registered:0b;
 };
 
 x in this function is a dictionary of the column names - flip to form an empty table
.hk.tmp.buffer - table is saved to a global namespace so it can be accessed, can also be done using set
.hk.tmp.registered - also defined in global namespace to be acccessed in the upd function

 
.hk.buildFromS3.upd:{[t;x]
    / update with func
    data:.hk.tmp.func[t;.hk.tmp.buffer upsert flip x];
    / add .u.regcols to log if first upd
    if[not .hk.tmp.registered;
        .hk.tmp.tplogHandle enlist (`.u.regCols;t; cols[data]!());
        .hk.tmp.registered:1b;
        ];
    / write data to new log
    if[98h = type data; data: value flip data];
    if[0 = count first data; :(::)];
    .hk.tmp.tplogHandle enlist (`upd;t;data);
 };

 data variable assingnment - updating the empty table with the data and new function
 
 
.hk.buildFromS3.handleError:{[e;s]
    .lg.w[`stream; "Replay threw: '",e,"\n",.track.formatstacktrace s];
    .hk.buildFromS3.clear[];
    'e
 };

 
.hk.buildFromS3.runInner:{[dt;tab;function]
  / check if func is a symbol
    if[-11h = type function; :.lg.o[`buildFromS3;"Using Function: ",string function] ];
  /  pull down logs from s3
    .hk.tmp.oldLogs: .house.getTplogs[dt;tab];
   /  replays log updating the data with the function
    .hk.tmp.func:function;
    @[`.;`upd;:;.hk.buildFromS3.upd];
    @[`.u;`regCols;:;.hk.buildFromS3.regCols];
    .hk.tmp.newTplogs:.hk.buildFromS3.replayTplog each .hk.tmp.oldLogs;
    if[not count .hk.tmp.newTplogs; :.lg.o[`buildFromS3;"No new logs"] ]; /most likely needs moved
    .hk.buildFromHdb.upload[dt;tab;.hk.tmp.newTplogs];
    .hk.buildFromHdb.sendReplayTasks[(),dt;tab];

    .hk.buildFromS3.clear[];
};
 
 upload and send replay tasks are taken from the .hk.buildFromHDB namespace
 
.hk.buildFromS3.clear:{[logs]
    if[.proc.region like "aws*";
            .util.remove each .hk.tmp.oldLogs,.hk.tmp.newTplogs];
    if[`tplogHandle in key `.hk.tmp;
            @[hclose;.hk.tmp.tplogHandle;(::)] ];
    delete tmp from `.hk;
 };

 .proc.region - `aws-eu-west-1b like "*aws*" (1b)

 q))key `.hk.tmp
``func`oldLogs`tplogHandle`buffer`registered`newTplogs - `tplogHandle present therefore 1b and close the tplogHandle 
 delete tmp from .`hk - removes the tmp namespace from the housekeeping namespaces to be used again 

	  
.hk.buildFromS3.bm.skew:{[t;x]
    / load in csv if not there
    if[not `symsMap in key `.hk.buildFromS3.bm;
        .hk.buildFromS3.bm.symsMap:`time`skewedMark`isin xcol ("  PFS";enlist csv)0: `$":",getenv[`ANALYTICSHOME],"/ref/skew_adjustments.csv";
        update `g#isin from `time xasc `.hk.buildFromS3.bm.symsMap;
        ];
    / delete skewed mark if it exists in x
    if[`skewedMark in cols x;
        x:delete skewedMark from x;
        ];
    /add midPrice or midYield to asof join, if null then set to =value of either midPrice or midYield
    update skewedMark:{x^x+y}[;skewedMark] ?[quoteType=`PRC; midPrice; midYield] from aj[`isin`time;x;.hk.buildFromS3.bm.symsMap]
 };

 
("  PFS") - first two spaces blank as we dont want to include the first two colunms of the csv, P (timestamp), F (float), S (symbol)
("  PFS";enlist csv)0:`$":",getenv[`ANALYTICSHOME],"/ref/skew_adjustments.csv" - csv stored in separate directory - read0 (display csv to console)
xcol - rename table columns of csv to `time`skewedMark`isin
update `g#isin from `time xasc `.hk.buildFromS3.bm.symsMap; - apply the grouped attribute to the csv which is already being sorted by time using xasc

if[`skewedMark in cols x;
x:delete skewedMark from x;
   ]; - check if the skewedMark colunm already exists in x (cols gets all colunm names), if exists in cols then delete the column from the data
   
   
update skewedMark:{x^x+y} - add x+y i.e. skewedMark to either midPrice or midYield, also used the fill operator (^) to replace nulls with the value of either midPrice/Yield
   
?[quoteType=`PRC; midPrice; midYield] - if quoteType=`PRC then add skewedMark to midPrice, if not (in this case =`YLD) then add skewedMark to midYield

from aj[`isin`time;x;.hk.buildFromS3.bm.symsMap] - asof join data from x to the csv
 
	  
