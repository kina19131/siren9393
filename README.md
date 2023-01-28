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
a. Cross Site Link Failure 
b. vLAG ISL (aka Single Site Failure) 
c. Double failure in vLANG ISLs (aka Double Site Failure) 

For any of the *Automated* testing, they can be found in 
```
PATH
```

Physical: <br>
a. Private Ethernet Failures <br>
b. Storage Failures on site A, B <br>
c. Site Failures <br>
d. Public network (en0) Failure <br>
e. Single switch failure <br>
f. Two switch faiulres - one switch from Site A & one from Site B <br>
g. Both switches from single site failure <br>
h. 3 switch failures <br>

(a) 
(b) 
(c)
(d) 
(e)
![7BD85163-88DD-4A89-9756-1235C0407F17](https://user-images.githubusercontent.com/65144979/215292088-1d0ef368-28ac-429b-bf53-3af190d068a2.jpeg)
*Please note that this is an image to drive the concept; it is one of the examples.* 
As you can see from the image, the connection has not been completely cut off. Thereby, they should still behave normally without failing. 

(f)
![image](https://user-images.githubusercontent.com/65144979/215292397-b8281e04-7c4d-461c-a8c1-0670f50cba0a.jpeg)
*Please note that this is an image to drive the concept; it is one of the examples.* 
Though there is a disconnection, the sub connections are still active. Therefore, 8a and 8c are not dead. It should operate as normal. 

(g) 
![image](https://user-images.githubusercontent.com/65144979/215292309-18a50fdc-908d-4d37-87f5-3386a1876d2c.jpeg)
*Please note that this is an image to drive the concept; it is one of the examples.*
In this particular case, 8a and 8c will both stop. One site will be completely down. 

(h) This testing 

