### Setup and Run DTW workload on GDPC cluster 
**Run all of these as instance user**

```
db2start
 
> For AIX On pscalep74008c: cd /hao/workload/dtw/aixsetup
> For Linux: On coralm228: cd /home/haoqi/dtw/setup

db2 -tvf createdatabase.ddl
db2 -tvf createtablespace.ddl
db2 -tvf crtb.ddl
db2 -tvf cridx.ddl

./db2set.ksh

db2set DB2RSHCMD=/usr/bin/ssh
db2 -tvf dbcfg.ddl
db2 -tvf dbm_setcfg.ddl
db2 update dbm cfg using SVCENAME 65000

> For linux: db2 update dbm cfg using SVCENAME 24598

db2stop force && db2start
db2 backup db dtw to /dev/null

./load.ksh
./load_stock.ksh

db2 -tvf drop.ddl
db2 -tvf runstat.ddl
db2 -tvf setintegrity.ddl
```

**Run DTW clients:**
```
ssh into the client machine: svtdbm@largenode44.torolab.ibm.com
pass: nine9xml

cd /home/svtdbm/dtw

> Makes changes to run_dtw and run it

./run_dtw
```

### Remove GDPC Instance 
```
Run db2idrop (db2idrop might say it failed because it cannot remove gpfs but in the log file it will say that instance was dropped successfully)
Remove gpfs
Run db2_deinstall -a (on all nodes including tiebreaker)
```

# THIS IS INSTRUCTION FOR TESTING 

Stress, Member soft kill, CF soft kill, TB Reboot, Member and CF Reboot are trivial. Please follow any guidances provided for regular purescale

Automated: 
- Cross Site Link Failure 
- vLAG ISL (aka Single Site Failure) 
- Double failure in vLANG ISLs (aka Double Site Failure) 

Physical: 
- Private Ethernet Failures
- Storage Failures on site A, B
- Site Failures 
- Public network (en0) Failure 
- Single switch failure
- Two switch faiulres - one switch from Site A & one from Site B 
- Both switches from single site failure 
- 3 switch failures 
