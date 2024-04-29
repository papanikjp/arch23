# Αρχιτεκτονική H/Y 

**ΕΡΓΑΣΤΗΡΙΑΚΟ PROJECT 2023/24**

Παπανικολόπουλος Παναγιώτης



## 1o Μέρος

### Ερώτηση 1

Οι προεπιλεγμένες βασικές παράμετροι για την προσομοίωση ορίζονται στην *main()* του αρχείου *starter_se.py* (σειρές 189-206). Η κλήση του gem έγινε μόνο με βασική παράμετρο **--cpu="minor"**, οπότε οι παράμετροι της προσομοίωσης ήταν:

- **Τύπος CPU**: minor

- **Συχνότητα CPU**:  1GHz

  ```python
  parser.add_argument("--cpu-freq", type=str, default="1GHz"
  ```

- **Αριθμός πυρήνων**: 1

  ```python
  parser.add_argument("--num-cores", type=int, default=1, help="Number of CPU cores")
  ```

- **Τύπος Μνήμης**: DDR3_1600_8x8

  ```python
  parser.add_argument("--mem-type", default="DDR3_1600_8x8", choices=ObjectList.mem_list.get_names(), help = "type of memory to use")
  ```

- **Αριθμός καναλιών μνήμης**: 2

  ```python
  parser.add_argument("--mem-channels", type=int, default=2, help = "number of memory channels")
  ```

- **Μέγεθος μνήμης**: 2GB

  ```python
  parser.add_argument("--mem-size", action="store", type=str, default="2GB", help="Specify the physical memory size")
  ```
  
- **Cache**:  devices.L1I, devices.L1D, devices.WalkCache, devices.L2



### Ερώτηση 2

#### Ερώτηση 2.a

| παράμετροι                          | config.ini                                 | config.json                                   |
| ----------------------------------- | ------------------------------------------ | --------------------------------------------- |
| **τύπος CPU**                       | [system.cpu_cluster.cpus] type=MinorCPU    | system.cpu_cluster.cpus[0].type = "MinorCPU"  |
| **συχνότητα CPU**                   | [system.cpu_cluster.clk_domain] clock=1000 | system.cpu_cluster.clk_domain.clock[0] = 1000 |
| **αριθμός πυρήνων**                 | [system.cpu_cluster.cpus] numThreads=1     | system.cpu_cluster.cpus[0].numThreads = 1     |
| **μέγεθος μνήμης**                  | [system] mem_ranges=0:2147483647           | system.mem_ranges[0] =  "0:2147483647"        |
| **παράμετροι L1 data cache**        | [system.cpu_cluster.cpus.dcache]           | system.cpu_cluster.cpus[0].dcache             |
| **παράμετροι L1 instraction cache** | [system.cpu_cluster.cpus.icache]           | system.cpu_cluster.cpus[0].icache             |
| **παράμετροι L2 cache**             | [system.cpu_cluster.l2]                    | system.cpu_cluster.l2                         |



#### Ερώτηση 2.b

| μετρική            | τιμή     | περιγραφή                                                    |
| :----------------- | :------- | ------------------------------------------------------------ |
| **sim_seconds**    | 0.000035 | Ο συνολικός χρόνος της προσομοίωσης σε δευτερόλεπτα *(τα δευτερόλεπτα που χρειάζεται το σύστημα που προσομοιώθηκε για να εκτελέσει το πρόγραμμα)* |
| **sim_insts**      | 5027     | Ο συνολικός αριθμός εντολών που εκτελέστηκαν στην προσομοίωση |
| **host_inst_rate** | 75632    | Ρυθμός (εντολές ανά δευτερόλεπτο) εκτέλεσης εντολών από τον προσομοιωτή (host) |



#### Ερώτηση 2.c

| μετρική       | τιμή |
| :------------ | :--- |
| **sim_insts** | 5027 |
| **sim_ops**   | 5831 |

Συνολικό νούμερο committed εντολών: ***sim_ops=5831***

Υπάρχει διαφορά με την τιμή του ***sim_insts*** γιατί εμπεριέχεται και ο αριθμός των micro-operations



#### Ερώτηση 2.d

Αριθμός προσπελάσεων L2 cache:

system.cpu_cluster.l2.overall_misses::total = 474



### Ερώτηση 3

**AtomicSimpleCPU**: Αυτό το μοντέλο είναι το απλούστερο στο gem5 και είναι αναπαράσταση ενός single-threaded in-order επεξεργαστή. Εκτελεί εντολές χωρίς pipelining. Είναι αποτελεσματικός για γρήγορες προσομοιώσεις και αρχικές αξιολογήσεις απόδοσης.

**TimingSimpleCPU**: Βασίζεται στον AtomicSimpleCPU και χρησιμοποιεί pipelining και λαμβάνει υπόψη θέματα χρονισμού με αποτέλεσμα να προσομοιώσει πιο ρεαλιστικά έναν πραγματικό επεξεργαστή. 

**MinorCPU**: Είναι λεπτομερής in-order επεξεργαστής περιλαμβάνει χαρακτηριστικά όπως ακριβή διαχείριση των interrupts. Στοχεύει στη ισορροπία μεταξύ ακρίβειας προσομοίωσης και υπολογιστικής απόδοσης. 

**O3CPU**: Το μοντέλο O3CPU αναπαριστά έναν επεξεργαστή out-of-order με 3 στάδια pipelining



#### Ερώτηση 3.a

Γράφτηκε το απλό πρόγραμμα demo.c και έγινε compile με την εντολή

```bash
arm-linux-gnueabihf-gcc -O0 -ggdb3 -std=c99 -static -o demo.out demo.c
```

Οι χρόνοι της προσομοίωσης είναι:

| τύπος CPU       | sim_seconds | host_seconds |
| --------------- | ----------- | ------------ |
| ΜinorCPU        | 0.005235    | 34.66        |
| TimingSimpleCPU | 0.014439    | 11.70        |



#### Ερώτηση 3.c

Επιπλέον έγινε προσομοίωση 3GHz MinorCPU με την εντολή:

```bash
./build/ARM/gem5.opt -d lab1/q3_3Ghz configs/example/se.py --cpu-type=MinorCPU --cpu-clock="3GHz" --caches -c lab1/demo.out
```



Από τα στατιστικά (stats.txt) που εμφανίζονται παρακάτω, φαίνεται ότι το πρόγραμμα εκτελέστηκε γρηγορότερα λόγω της μεγαλύτερης συχνότητας λειτουργίας της CPU.  

| μετρική                     | τιμή     |
| --------------------------- | -------- |
| system.cpu_clk_domain.clock | 333      |
| sim_seconds                 | 0.003496 |





## 2o Μέρος

### 1ο βήμα

#### Ερώτηση 1

| παράμετρος                               | ενότητα             | πεδίο           |    τιμή |
| ---------------------------------------- | ------------------- | --------------- | ------: |
| **Μέγεθος L1 instruction cache** (bytes) | [system.cpu.icache] | size            |   32768 |
| **Μέγεθος L1 data cache** (bytes)        | [system.cpu.dcache] | size            |   65536 |
| **Μέγεθος L2 cache** (bytes)             | [system.l2]         | size            | 2097152 |
| **Αssociativity L1 instruction cache**   | [system.cpu.icache] | assoc           |       2 |
| **Αssociativity L1 data cache**          | [system.cpu.dcache] | assoc           |       2 |
| **Αssociativity L2 cache**               | [system.l2]         | assoc           |       8 |
| **Μέγεθος cache line** (bytes)           | [system]            | cache_line_size |      64 |



#### Ερώτηση 2

| #    | Benchmarks | sim_seconds | cpi       | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | --------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0,083982    | 1,679650  | 0,014798    | 0,000077    | 0,282163 |
| 2    | specmcf    | 0,064955    | 1,299095  | 0,002108    | 0,023612    | 0,055046 |
| 3    | spechmmer  | 0,059396    | 1,187917  | 0,001637    | 0,000221    | 0,077760 |
| 4    | specsjeng  | 0,513528    | 10,270554 | 0,121831    | 0,000020    | 0,999972 |
| 5    | speclibm   | 0,174671    | 3,493415  | 0,060972    | 0,000094    | 0,999944 |

<img src=".\images\A2 g1.png" width="400" height="300"><img src=".\images\A2 g2.png" width="400" height="300"><img src=".\images\A2 g3.png" width="400" height="300"><img src=".\images\A2 g4.png" width="400" height="300"><img src=".\images\A2 g5.png" width="400" height="300">

**Σχόλιο** : Το πιο αργό benchmark είναι το specsjeng. Επιπλέον, τα πιο αργά benchmarks έχουν μεγάλο L2 cache miss rate.



#### Ερώτηση 3

##### **Default** (χωρίς --cpu-clock)

| #    | Benchmarks | sim_seconds | cpi       | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | --------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0,083982    | 1,679650  | 0,014798    | 0,000077    | 0,282163 |
| 2    | specmcf    | 0,064955    | 1,299095  | 0,002108    | 0,023612    | 0,055046 |
| 3    | spechmmer  | 0,059396    | 1,187917  | 0,001637    | 0,000221    | 0,077760 |
| 4    | specsjeng  | 0,513528    | 10,270554 | 0,121831    | 0,000020    | 0,999972 |
| 5    | speclibm   | 0,174671    | 3,493415  | 0,060972    | 0,000094    | 0,999944 |

##### **CPU clock 1GHz**

| #    | Benchmarks | sim_seconds | cpi      | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | -------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0,161025    | 1,610247 | 0,014675    | 0,000077    | 0,282157 |
| 2    | specmcf    | 0,127942    | 1,279422 | 0,002108    | 0,023627    | 0,055046 |
| 3    | spechmmer  | 0,118530    | 1,185304 | 0,001629    | 0,000221    | 0,077747 |
| 4    | specsjeng  | 0,704056    | 7,040561 | 0,121831    | 0,000020    | 0,999972 |
| 5    | speclibm   | 0,262327    | 2,623265 | 0,060971    | 0,000094    | 0,999944 |

##### **CPU clock 3GHz**

| #    | Benchmarks | sim_seconds | cpi       | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | --------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0,058385    | 1,753291  | 0,014932    | 0,000077    | 0,282166 |
| 2    | specmcf    | 0,043867    | 1,317329  | 0,002108    | 0,023609    | 0,055046 |
| 3    | spechmmer  | 0,039646    | 1,190581  | 0,001637    | 0,000221    | 0,077761 |
| 4    | specsjeng  | 0,449821    | 13,508136 | 0,121831    | 0,000020    | 0,999972 |
| 5    | speclibm   | 0,146433    | 4,397377  | 0,060972    | 0,000094    | 0,999944 |

Σύμφωνα με το sim_seconds για την αρχική προσομοίωση και την δεύτερη (1GHz), φαίνεται ότι στην αρχική (default) η συχνότητα της CPU είναι μεγαλύτερη από 1GHz.

Σύμφωνα με το sim_seconds για τα πρώτα 3 bencharks παρατηρείται καλό scaling (οι χρόνοι εκτέλεσης είναι αντιστρόφως ανάλογοι της συχνότητας της CPU), ενώ για τα 2 τελευταία bencharks δεν βελτιώνεται ανάλογα ο χρόνος εκτέλεσης γιατί το L2 cache miss rate είναι 1, οπότε γίνονται συνεχώς προσπελάσεις στην RAM.

|                                 | system.clk_domain.clock | system.cpu_clk_domain.clock |
| ------------------------------- | ----------------------- | --------------------------- |
| **default** (χωρίς --cpu-clock) | 1000                    | 500                         |
| **CPU clock 1GHz**              | 1000                    | 1000                        |
| **CPU clock 3GHz**              | 1000                    | 333                         |

Φαίνεται ότι το system.clk_domain.clock δείχνει την συχνότητα που έχει το ρολόι για όλο το σύστημα, ενώ το system.cpu_clk_domain.clock είναι το ρολόι που χρησιμοποιεί ο επεξεργαστής και έτσι καθορίζεται η συχνότητα λειτουργείας του.



#### Ερώτηση 4

**mem-type=DDR3_1600_8x8  και cpu-clock=1GHz**

| #    | Benchmarks | sim_seconds | cpi      | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | -------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0,161025    | 1,610247 | 0,014675    | 0,000077    | 0,282157 |



##### mem-type=DDR3_2133_8x8  και cpu-clock=1GHz

| #    | Benchmarks | sim_seconds | cpi      | dcache miss | icache miss | l2 miss  |
| ---- | ---------- | ----------- | -------- | ----------- | ----------- | -------- |
| 1    | specbzip   | 0.160699    | 1,606994 | 0,014457    | 0,000077    | 0,282157 |

Βελτιώθηκε ελάχιστα το CPI και ο χρόνος εκτέλεσης, λογικά λόγω της γρηγορότερης προσπέλασης της μνήμης





