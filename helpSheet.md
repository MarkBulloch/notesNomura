# Setup New AWS Stack

## Steps:
1. Download launch development-setup (non-prod) from here:
https://gitlab.nomura.com/GM-AILabs-CTB/ailabs-developers-setup

2. Go to UAT/dev server:
https://sso-core-prd.nomura.com/am/saml2/jsp/idpSSOInit.jsp?spEntityID=urn:amazon:webservices&metaAlias=/Internal/05-Nomura-Internal-IDP-PRD

3. Select eu-west non prod
4. Go to cloud formation under maintence and gov
5. Create stack with new resources
6. Upload downloaded .yaml template file
7. Give name, AZ-1b, env - gmdev, type-t3large, user bullocma
8. Go to summary and create stack
9. When create complete - go to EC2 instances and stack shoud be available
10. Get ipv4 address and insert to putty

To take out of spot - remove spot param from template
May need to change config.json file on dev-env to pull from different S3 bucket.

---




# Turn log into table

## Get the table cols:
```q
q)get `$"BlendedModel.0.tickerplant-1.ctp.redlxp00322.20191019D104011295688000"
`.u.regCols `BlendedModel `time`isin`cusip`alias`bondType`sector`b2CStatus`be..
`upd        `BlendedModel (`s#2019.10.19D07:11:34.443800000 2019.10.19D07:11:..
q)
q)x:get `$"BlendedModel.0.tickerplant-1.ctp.redlxp00322.20191019D104011295688000"
q)first x
`.u.regCols
`BlendedModel
`time`isin`cusip`alias`bondType`sector`b2CStatus`benchmark`book`radialId`toky..
q)
q)reg:first x
q)reg
`.u.regCols
`BlendedModel
`time`isin`cusip`alias`bondType`sector`b2CStatus`benchmark`book`radialId`toky..
```

```q
q)flip reg[2]
time isin cusip alias bondType sector b2CStatus benchmark book radialId tokyo..
-----------------------------------------------------------------------------..
q)t:flip reg[2]
q)t
time isin cusip alias bondType sector b2CStatus benchmark book radialId tokyo..
-----------------------------------------------------------------------------..
```

## Get the data:
```q
q)last x
`upd
`BlendedModel
(`s#2019.10.19D07:11:34.443800000 2019.10.19D07:11:34.444600000 2019.10.19D07..
q)up:last xS
q)d: flip up[2]
q)d
2019.10.19D07:11:34.443800000 `GB0009997999 "G92435MN6" "821"     `FIX `Gilts..
2019.10.19D07:11:34.444600000 `GB00BDRHNP05 "00BDRHNP0" "1q27"    `FIX `Gilts..
2019.10.19D07:11:34.444600000 `GB00BFWFPP71 "00BFWFPP7" "1T49"    `FIX `Gilts..
2019.10.19D07:11:34.444600000 `GB00BJLR0J16 "00BJLR0J1" "1f54"    `FIX `Gilts..
2019.10.19D07:11:34.444600000 `GB00B84Z9V04 "00B84Z9V0" "3q44"    `FIX `Gilts..
2019.10.19D07:11:34.452868000 `GB00B128DH60 "G92450ZK7" "IL27"    `INF `Brock..
2019.10.19D07:11:34.453366000 `GB00B3Y1JG82 "G4527HPY1" "IL29"    `INF `Brock..
2019.10.19D07:11:34.455378000 `GB0008932666 "G92435HN2" "IL30old" `INF `Brock..
2019.10.19D07:11:34.455799000 `XS0307538016 "G6459BCM1" ""        `INF `Brock..
2019.10.19D07:11:34.456250000 `GB00B3D4VD98 "G924502U1" "IL32"    `INF `Brock..
2019.10.19D07:11:34.456597000 `GB00BYY5F144 "00BYY5F14" "IL26"    `INF `Brock..
2019.10.19D07:11:34.457282000 `GB00B85SFQ54 "G9T44MFJ6" "IL24"    `INF `Brock..
2019.10.19D07:11:34.457736000 `GB00B46CGH68 "G4527HQA2" "IL34"    `INF `Brock..
2019.10.19D07:11:34.458593000 `GB0008983024 "G92435BG3" "IL24old" `INF `Brock..
2019.10.19D07:11:34.459161000 `GB00B1Z5HQ14 "G92450M39" "IL22"    `INF `Brock..
2019.10.19D07:11:34.459161000 `GB0031790826 "G92450NS3" "IL35old" `INF `Brock..
2019.10.19D07:11:34.459161000 `GB00BYZW3J87 "00BYZW3J8" "IL36"    `INF `Brock..
2019.10.19D07:11:34.459888000 `GB00BBDR7T29 "G9T44MGV8" "IL19"    `INF `Brock..
2019.10.19D07:11:34.460419000 `GB00B1L6W962 "G92450G93" "IL37"    `INF `Brock..
2019.10.19D07:11:34.461073000 `GB0009081828 "G92435BP3" "IL20old" `INF `Brock..
2019.10.19D07:11:34.461435000 `XS0299655448 "G6459BCK5" ""        `INF `Brock..
2019.10.19D07:11:34.461915000 `GB00B3LZBF68 "G924508Y7" "IL40"    `INF `Brock..
..

```


## Upsert the data to table
```q
q)t upsert d
time                          isin         cusip       alias     bondType sec..
-----------------------------------------------------------------------------..
2019.10.19D07:11:34.443800000 GB0009997999 "G92435MN6" "821"     FIX      Gil..
2019.10.19D07:11:34.444600000 GB00BDRHNP05 "00BDRHNP0" "1q27"    FIX      Gil..
2019.10.19D07:11:34.444600000 GB00BFWFPP71 "00BFWFPP7" "1T49"    FIX      Gil..
```
---

# Update ASG (e.g. genhousekeeper in POC to not shutdown)

1. Go to AWS console (POC) and then EC2
2. From EC2 select Auto Scaling Groups from LHS
3. Select name of ASG - KDB-DATALAKE-PROD-Oct-17-v2-GenHousekeepingAutoscalingGroup-1LEAM4KWX8QPK
4. Under group details update desired capacity from 0 -> 1
5. Change minimum capacity from 0 -> 1
---
	
	
# Add filter to instance to stop alert emails 
1. Go to AWS console
2. Select cloud formation
3. Filter for a stack name starting with cloud and select prod
4. Select update
5. Use current template
6. Under specify stack details, update the FilterIgnorePattern to include your filter e.g. Call to terminate housekeeping instance failed
7. Save and stack will update
---
# Adding Tasks to TASKS table for release
Add function to `Post Release Tasks - To be run on Saturday` section  

```q
{[sd;ed] dts:sd+til 1+ed-st; n:count dts; args:flip(dts;n#`BlendedModel;n#`.hk.buildFromS3.bm.skew); .task.addMultipleTasks . (args[;0];args[;1];n#`genhousekeeping;n#`.hk.buildFromS3.run;args)}[2019.10.19;2020.09.18]
```
Important to note - keep all in one function, avoid setting global variables

`.task.addMultipleTasks` takes 5 params:
* date - defined in args[0] as dts
* table - defined in args[1] as `BlendedModel`
* process - genhousekeeping
* function - defined as `.hk.buildFromS3.run` (function to actually run tasks)
* arguments - defined as args(needs to be flipped) - arguments to the function .i.e in this case dates, table, func
---

# Setting up new UAT Stack
## Steps:
1. Launch using dev-DL-CF-template (launch in 1a) - s3 locator from .util.getLatestUploadedWekaSnapshot[], ami - latest or newly created one
2. Check all instances started in EC2 console, eg dataplant, eventbus, desired num of queryfarms (specified in template but 8 for prod env and 6 otherwise)
3. ssh to the dataplant server and check the cloud-init log. /var/log/cloud-init-output.log is the path to the log, or an alias for just viewing it in tail -f mode is cloudinitlog - should see somthing similar to:
Dec 18 15:46:23 cloud-init[3290]: util.py[DEBUG]: Cloud-init v. 19.3-3.amzn2 finished at Fri, 18 Dec 2020 15:46:23 +0000. Datasource DataSourceEc2.  Up 385.41 seconds
4.cd $LOG - ensure the pdb starts (comes up, does all it's checks/data changes and runs a commit, logs out the "started on..." message )
5. Connect to ingress and run post release checks - .gw.postRelease.run[] - some will fail due to no live data and most likely no close data
6. Flip ENI/ELB - update to use the latest stack (post message into chat) 
7. Verify query side is using new stack and delete old stack if necessary

# Comparing on-prem/aws tplogStats
## Steps:

1. Open handle to each host - red/wok and aws
2. res:(wok"select logDate, table, wokCnt:tabCount from stats where logDate in 2021.01.13 2021.01.17 2021.01.21 2021.01.25 2021.01.29") lj 2!aws"select logDate, table, tabCountAWS1a:tabCount from stats where logDate in 2021.01.13 2021.01.17 2021.01.21 2021.01.25 2021.01.29, source=`$\1a\""
3. select from res where not wokCnt=tabCountAWS1a, wokCnt>0, not table in systemtables, dlevents etc
