**Setup New AWS Stack**

Steps:
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







**Turn log into table**

Get the table cols:
```
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

```
q)flip reg[2]
time isin cusip alias bondType sector b2CStatus benchmark book radialId tokyo..
-----------------------------------------------------------------------------..
q)t:flip reg[2]
q)t
time isin cusip alias bondType sector b2CStatus benchmark book radialId tokyo..
-----------------------------------------------------------------------------..
```

Get the data:
```
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


Upsert the data to table
```
q)t upsert d
time                          isin         cusip       alias     bondType sec..
-----------------------------------------------------------------------------..
2019.10.19D07:11:34.443800000 GB0009997999 "G92435MN6" "821"     FIX      Gil..
2019.10.19D07:11:34.444600000 GB00BDRHNP05 "00BDRHNP0" "1q27"    FIX      Gil..
2019.10.19D07:11:34.444600000 GB00BFWFPP71 "00BFWFPP7" "1T49"    FIX      Gil..
```
