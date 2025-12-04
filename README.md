## The Pulse of Information Networks: Identifying Top-$r$ Turning Points of Evolving Communities （$r$-ICTPS）

### 1 Introduction

This paper provides the source code, datasets, and experimental scripts for **$r$-ICTPS**, a novel framework for detecting turning points in the evolution of intimate communities within weighted temporal graphs (WTGs).

We comprehensively evaluate the effectiveness and efficiency of the proposed algorithms on multiple real-world temporal graph datasets. Table 1 presents two implemented variants of our method, designed to compare performance under different indexing and pruning strategies.

| **ALGORITHM** | **DESCRIPTION**                                              |
| :-----------: | :----------------------------------------------------------- |
|     Basic     | The basic algorithm that detects turning points by exhaustively enumerating all candidate time intervals and computing intimate communities using a BFS-based intimacy scoring method. |
|     IBTPS     | An enhanced version incorporating structural and intimacy-based pruning strategies to further reduce the search overhead and improve runtime efficiency. |
| BasicMeasure  | Evaluates the six effectiveness metrics (DN, DM, AC, QCI, ICI, IAC) based on communities obtained by the Basic algorithm. |
|  BasicMemory  | Measures the memory footprint of the Basic algorithm, including both the intermediate data structures used during exhaustive enumeration and the overall peak memory consumption during execution. |
|  IBTPSMemory  | Reports the memory usage of IBTPS, capturing the additional index memory required by pruning structures as well as the total memory consumed throughout the optimized turning-point detection process. |

### 2 Requirements

All experiments were conducted on a Linux server equipped with a 3.7 GHz CPU and 128 GB of memory. All programs are developed in Java 1.8 and do not require any additional external JAR packages.

### 3 Datasets

Our experiments utilize four real-world weighted temporal graph datasets widely used in existing research. Each dataset represents a temporal network where edges are associated with timestamps and weights, capturing the dynamic evolution of the graph over time. The datasets are stored as plain text files, typically containing a single graph file per dataset. Each graph file is organized into time-ordered segments, where each segment begins with a timestamp line (e.g., a year), followed by multiple lines describing weighted edges active during that time period. This format facilitates straightforward parsing and temporal snapshot extraction for analysis.

Here, DBLP is taken as an example. 

- **dblp.txt**: The graph file is organized by temporal snapshots separated by timestamp lines (e.g., years). Each timestamp line is followed by multiple lines representing weighted edges active at that time. Each edge line is formatted as:
`source_node_id <space> target_node_id <space> edge_weight`.
  
  | **timestamp** | **1938** |        |        |
  | ------------- | :------: | :----: | :----: |
  |               |  464254  | 464253 |   1    |
  |               |  464253  | 464254 |   1    |
  | **timestamp** | **1940** |        |        |
  |               |  464940  | 464939 |   1    |
  |               |  ......  | ...... | ...... |

### 4 Usage

4.1 Efficiency

We explain how to measure the running time of our proposed algorithms. All methods share the same input parameters: a *graph file path*, an *upper bound N*, a *decay parameter α* , a *core number k*, an *intimacy threshold θ*, a *turning points number r*, a *time interval size Δ*, and the *query node q*.

Input format:

```
<dataSet> <N> <ALPHA> <coreK> <intimacy> <topR> <span> <NAME>
```

Command line usage:

```
java -cp IntimateCommunity.jar formal.method^
graph file path
upper bound
decay parameter
core number
intimacy threshold
turing point number
time interval size
query node
```

We provide a *test file* for testing with the following experimental parameters, and  All required files are stored in the **data** folder.

Example input:

```
java -cp IntimateCommunity.jar formal.IBTPS ./data/test.txt 9 0.1 2 0.01 2 1 10
```

Example output:

```
Total time: 3ms
turing points 0 : 3
2 [1, 2, 3, 9, 10]
3 [4, 5, 7, 8, 10]
turing points 1 : 5
4 [4, 5, 7, 8, 10]
5 [1, 3, 9, 10]
```

4.2 Effectiveness

We discuss how to obtain six key evaluation metrics: **DN**, **DM**, **AC**, **QCI**, **ICI**, and **IAC**. The parameters used in effectiveness experiments remain consistent with those in **Section 4.1 Efficiency** to ensure fair and comparable results across all methods.

Input format example (supporting multiple query nodes separated by commas):

```
<dataSet> <N> <ALPHA> <coreK> <intimacy> <topR> <span> <NAME or NAME_LIST>
```

Command line usage:

```
java -cp IntimateCommunity.jar formal.BasicMeasure^
graph file path
upper bound
decay parameter
core number
intimacy threshold
turing points number
time interval size
query node list
```

Example input:

```
java -cp IntimateCommunity.jar formal.BasicMeasure ./data/test.txt 9 0.1 2 0.01 2 1 10
```

Example output:

```
Running for NAME = 10
Total time: 7ms
turing points 0 : 3
2 [1, 2, 3, 9, 10]
3 [4, 5, 7, 8, 10]
turing points 1 : 5
4 [4, 5, 7, 8, 10]
5 [1, 3, 9, 10]
========================
DN = 0.7944444444444444
DM = 1.8333333333333333
AC = 2.0
QCI = 0.2052777777777778
ICI = 0.1885555555555556
IAC = 0.7359427609427609
```

4.3 Memory

We also evaluate the memory consumption of our method by reporting two essential measurements: **Index Memory** and **Total Memory**. The parameter configuration used for memory analysis is the same as described in **Section 4.1 Efficiency**, ensuring consistency across all evaluations.

Input format:

```
<dataSet> <N> <ALPHA> <coreK> <intimacy> <topR> <span> <NAME>
```

Command line usage:

```
java -cp IntimateCommunity.jar formal.IBTPSMemory^
graph file path
upper bound
decay parameter
core number
intimacy threshold
turing points number
time interval size
query node
```

Example input:

```
java -cp IntimateCommunity.jar formal.IBTPSMemory ./data/test.txt 9 0.1 2 0.01 2 1 10
```

Output description:

In addition to standard result files, our memory evaluation produces two supplementary logs recording detailed memory usage during execution. The **heap_usage.log** file tracks peak JVM heap consumption throughout the process, enabling precise quantification of runtime memory footprint. The **indexs_memory.log** file records memory usage of all indexing structures created by the algorithm, isolating and analyzing overhead introduced by index-based optimizations.

**heap_usage.log** example:

```
1764832953594 21.69
1764832953615 16.14
1764832953625 16.46
1764832953636 16.46
1764832953651 16.14
1764832953662 16.30
......
1764832959153 16.14
1764832959168 16.13
1764832959179 16.25
MAX_HEAP_MB 21.69
```

**indexs_memory.log** example:

```
1764833297082 INIT 0.0000
1764833297336 ITER 0.4688
1764833297586 ITER 0.4063
1764833297837 ITER 0.3438
1764833298092 ITER 0.2656
1764833298342 INIT 0.5781
......
1764833301916 ITER 0.4688
1764833302175 ITER 0.4063
1764833302433 ITER 0.3438
MAX_INDEX_KB 0.6406
```

4.4 Case Study

We discuss how to obtain all relevant information for the case study, including turning points and their corresponding community sets. These constitute the core content of the case study presented in this paper.

Case methods employ identical parameters: a *graph file path*, an *upper bound N*, a *decay parameter α*, a *core number k*, an *intimacy threshold θ*, a *turning points number r*, a *time interval size Δ*, and the *query node q*. Example input format:

```
java -cp IntimateCommunity.jar formal.method^
graph file path
upper bound
decay parameter
core number
intimacy threshold
turing point number
time interval size
query node
```

We use the reduced *DBLP* dataset for testing with the following parameters. All required files are stored in the **data** folder.

Input

```
<dataSet> <N> <ALPHA> <coreK> <intimacy> <topR> <span> <NAME>
```

Example command:

```
java -cp IntimateCommunity.jar formal.Basic ./data/dblp.txt 9 0.1 4 0.1 3 2 25541
```

Example output:

```
Total time: 10267ms
turing points 0 : 2023
2021 [109315, 1609306, 569419, 25541, 109316, 25542, 288770, 25543, 25544, 103432, 1803, 683398, 1210719, 27535, 2256, 399766, 1092993, 1535620, 781214, 1214852, 110486, 70745, 132184, 425116, 180505, 425115, 1202736, 26533, 500064, 936, 128808, 890215, 654692, 1092733, 1210722, 267573, 1210723, 1210720, 1535013, 1210721, 1532577, 11385, 131129, 1811936, 1202734, 1202735, 455160]
2023 [169734, 109316, 115975, 1072407, 459791, 1803, 193801, 346635, 301342, 29, 43808, 455460, 1202736, 1532725, 132129, 91174, 385325, 39725, 39728, 114481, 1535013, 147767, 1318690, 892980, 147768, 1709600, 2108, 175423, 892979, 1822500, 315718, 148039, 1866, 212303, 304712, 1210719, 79, 304971, 1532481, 17240, 132184, 1573191, 727145, 1895, 212843, 110440, 890215, 890214, 1092731, 108909, 1092732, 1092733, 453234, 1093221, 1660268, 1093222, 114038, 11385, 890739, 647817, 11393, 110723, 113283, 110722, 11397, 1093021, 399766, 145, 1092993, 747416, 147861, 1655680, 425116, 425115, 271514, 801171, 416, 418, 936, 5553, 1093026, 131000, 615369, 25541, 1071829, 112071, 905163, 25543, 25544, 268747, 11217, 193235, 2584820, 111323, 1017835, 11237, 109284, 131562, 36075, 1093117, 2452948, 14074, 39674, 1811936]
turing points 1 : 2018
2016 [111555, 25541, 1803, 23627, 259983, 1210719, 110031, 1367419, 120048, 1660268, 365566, 896723]
2018 [10561, 110723, 113283, 25541, 704015, 17415, 25543, 147013, 25544, 112523, 972868, 1803, 112522, 1210719, 110031, 109079, 114841, 111387, 132120, 425116, 704016, 896723, 111134, 2660040, 418, 132198, 2659212, 1092726, 110120, 1535742, 1092733, 1367419, 5553, 1535014, 790270, 224631, 1660268, 2502800, 11385]
turing points 2 : 2021
2019 [110723, 113283, 422916, 109316, 697868, 17415, 108934, 1803, 112522, 1202588, 399766, 118672, 109079, 1214852, 614046, 1154440, 1916932, 111387, 425116, 425115, 111134, 418, 2659212, 91174, 68012, 5553, 1535014, 892345, 2502800, 1532577, 615369, 10561, 429253, 25541, 25543, 147013, 25544, 972868, 212303, 1210719, 79, 132184, 122844, 896723, 132198, 1092726, 117990, 131429, 1535742, 890215, 1092733, 25582, 1367419, 790270, 1181153, 224631, 1660268, 11385, 455160]
2021 [109315, 1609306, 569419, 25541, 109316, 25542, 288770, 25543, 25544, 103432, 1803, 683398, 1210719, 27535, 2256, 399766, 1092993, 1535620, 781214, 1214852, 110486, 70745, 132184, 425116, 180505, 425115, 1202736, 26533, 500064, 936, 128808, 890215, 654692, 1092733, 1210722, 267573, 1210723, 1210720, 1535013, 1210721, 1532577, 11385, 131129, 1811936, 1202734, 1202735, 455160]
```
