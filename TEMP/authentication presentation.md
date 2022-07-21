# To do
1. PHP /tmp/sess_
2. What's Redis
3. Cookie based session
4. HMAC
5. Definition of Cookie
6.  Add brute force & prevention to zafiyetli and duzeltilmis

# PHP /tmp/sess_
```php
<?php  
session_start();  

$_SESSION["session"]="verysecuresessionvalue"; // writes session value(plain text) to /tmp/sess_<RANDOM_VALUE>
```

# What's Redis and How it works
Redis stands for **Re**mote **Di**ctionary **S**erver. 
* It stores data on **memory**
* It can be used a **cache storage** between application and actual database.

![[Pasted image 20220628105524.png]]

### How to keep data safe in redis
* **Replicas** can be used to keep data safe as they're used in cassandra

![[Pasted image 20220628111025.png]]

* **Snapshotting (RDP)** is another alternative, it stores data to disk, so it's greate for backup. However since snapshots are taken after a time period passed. You may not have all data after a change made until snapshot is taken.

![[Pasted image 20220628111300.png]]

* **Append Only File (AOF)**, it resolves the issue mentioned in Snapshotting. Every change made to database is written to AOF file, and whenever redis is restarted, it reads from that AOF file and rebuild state.

![[Pasted image 20220628111824.png]]

We can use both to keep data safe. While **AOF** writes data to disk, **RDB** can be used to write data into a backup area.

![[Pasted image 20220628131217.png]]

![[Pasted image 20220628131623.png]]

### Redis on Flash
In standart, Redis uses only RAM to store data, however we can use another way which is called **Redis on Flash**.

In this approach, redis uses both RAM and SSD. For datas that is being accessed **frequently**, it uses RAM, for other datas it uses SSD. 

![[Pasted image 20220628132451.png]]

# Cookie Based Session
There are several ways to access a user's session data. If you want to store that data in clients side, cookie based session can be used. In this approach server sends client's session data via **Set-Cookie** header with applying symmetric encryption.

# HMAC
Stands for **Hash Based Message Authentication Code**.

Hash Function: **SHA256(Arbitrary Amount of Bits)** -> **Fixed Amount of Bits**

HMAC does same thing as hash function however it accepts two additional parameters.
1. Hash Type: Determines the hash function that is going to be used in HMAC.
2. Secret: A secret set of bits that is shared among users who have a trust relationship.
 
HMAC: **HMAC(Arbitrary Amount of Bits, HashType, Secret)** -> **Fixed Amount of Bits**

### Purposes of using HMAC
* By using HMAC we can ensure about data integrity. We can acknowledge that data is not being changed by a hacker that performed MITM attack.
* Also we can use HMAC in authentication, since secret is only known by trusted users, it's safe to use.

![[Pasted image 20220628152731.png]]

# Cookie
Cookies are datas that's being passed to Client Side in order to turn stateless HTTP into stateful. Through cookies, web browsers remember you when you reenter a website that have been sent you cookie before.