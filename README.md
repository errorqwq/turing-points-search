## The Pulse of Information Networks: Identifying Top-$r$ Turning Points of Evolving Communities （$r$-ICTPS）

### 1 Introduction

This paper provides the source code, datasets, and experimental scripts for **$r$-ICTPS**, a novel framework for detecting turning points in the evolution of intimate communities within weighted temporal graphs (WTGs).

We comprehensively evaluate the effectiveness and efficiency of the proposed algorithms on multiple real-world temporal graph datasets. Table 1 presents two implemented variants of our method, designed to compare performance under different indexing and pruning strategies.

| **ALGORITHM** | **DESCRIPTION**                                              |
| :-----------: | :----------------------------------------------------------- |
|     Basic     | The basic algorithm that detects turning points by exhaustively enumerating all candidate time intervals and computing intimate communities using a BFS-based intimacy scoring method. |
|    IB-TPS     | An enhanced version incorporating structural and intimacy-based pruning strategies to further reduce the search overhead and improve runtime efficiency. |

### 2 Requirements

The experiments have been run on a Linux server with a 3.7 GHZ, 128 GB memory. All programs are developed using Java 1.8 and don't need any incorporate additional JAR packages.

### 3 Datasets

Our experiments utilize four real-world weighted temporal graph datasets widely used in existing research. Each dataset represents a temporal network where edges are associated with timestamps and weights, capturing the dynamic evolution of the graph over time. The datasets are stored as plain text files, typically containing a single graph file per dataset. Each graph file is organized into time-ordered segments, where each segment begins with a timestamp line (e.g., a year), followed by multiple lines describing weighted edges active during that time period. This format facilitates straightforward parsing and temporal snapshot extraction for analysis.

Here, DBLP is taken as an example. 

- **dblp.txt**: The graph file is organized by temporal snapshots separated by timestamp lines (e.g., years). Each timestamp line is followed by multiple lines representing weighted edges active at that time. Each edge line is formatted as :source_node_id <tab> target_node_id <tab> edge_weight.

  | timestamp | **1938** |        |        |
  | --------- | :------: | :----: | :----: |
  |           |  464254  | 464253 |   1    |
  |           |  464253  | 464254 |   1    |
  |           |   1940   |        |        |
  |           | **1940** |        |        |
  |           |  464940  | 464939 |   1    |
  |           |  ......  | ...... | ...... |

### 4 Usage

4.1 Efficiency

We explain how to measure the running time of our proposed algorithms. All methods share the same input parameters: a *graph file path*, an *upper bound N*, a *decay parameter α* , a *core number k*, an *intimacy threshold θ*, a *turing points number r*, a *time interval size Δ*, and the *query node q*.

```
<dataSet> <N> <ALPHA> <coreK> <intimacy> <topR> <span> <NAME>
```

```
java -cp IntimateCommunity.jar formal.method^
graph file path
upper bound
decay parameter
core number
intimacy threshold
turing points number
time interval size
query node
```

We provide a *test file* for testing with the following experimental parameters, and  All required files are stored in the **data** folder.

Input

```
java -cp IntimateCommunity.jar formal.IBTPS ./data/dblp.txt 9 0.1 4 0.1 3 2 25541
```

Output

```
queryRelation: artist
AaType: 117
queryID: 6832424
l: 3
queryK: 10
r: 3
read graph ~~~
read graph over ~~~
b: 10
# threads: 10
Top-1
semantic: [artist, writer]
community size: 110
community nodes: [2970414, 3292726, 4703810,...]
Top-2
semantic: [artist, composer, writer]
community size: 114
community nodes: [2970414, 3292726, 4703810,...]
Top-3
semantic: [artist, composer, associatedBand, writer]
community size: 566
community nodes: [4481092, 2876457, 4640835,...]
Opt-Record+P time:2361.7469ms
```

4.2 Effectiveness

We discuss how to obtain the three most important metrics: NMP, ALSD, and SC. The parameters used by the Effectiveness method are the same as those used in **4.1 Efficiency**. Here is an example of parameter input.

```
java -jar core.jar Effectiveness^
relationFilePath
similarityFilePath 
graphFilePath 
vertexTypeFilePath 
queryRelation 
AaType
queryID 
l
queryK
r
threads
```

We still use the reduced *DBpedia* for testing with the following experimental parameters, and All required files are stored in the **data** folder.

Input

```
java -jar core.jar Effectiveness^
  "./data/dbpedia/relation2id.txt" ^
  "./data/dbpedia/similaritytotalfile.txt" ^
  "./data/dbpedia/DbpediaGraphWithPredicate.txt" ^
  "./data/dbpedia/DbpediaVertex.txt" ^
  artist 117 6832424 3 10 3 10
```

Output

```
queryRelation: artist
AaType: 117
queryID: 6832424
l: 3
queryK: 10
r: 3
read graph ~~~
read graph over ~~~
Top-1
community size: 398
community nodes: [4481092, 2876457, 529420,...]
Top-2
community size: 1515
community nodes: [4481092, 1402901, 5716052, ...]
Top-3
community size: 1567
community nodes: [4481092, 4640835, 5541982, 2773025,...]
ALSD: 0.2777777777777778
NMP: 24
SC: 0.9425733585898489
```

4.3 Case

We discuss how to obtain all relevant information for the case study, including the node set and the $\mathcal{P}^*$edge set. The node set contains three types of information: node ID, the name of the entity corresponding to the node ID, and $r$. The $\mathcal{P}^*$-edge set contains four types of information: the starting node, the ending node, the $\mathcal{P}^*$-edge, and $r$. These pieces of information constitute the case study in the paper.

Case methods employ identical parameters: a *relation file path*, a *similarity file path*, a *graph file path*, a *node type file path*, a *query relation*, a *type id* of anchor node, a length bound $\hat{l}$, a query *k*, a query $r$, a parameter $b$, the number of parallel threads # thread, a *result of node set file path* and a *result* $\mathcal{P}^*$-*edge set file path*. Here is an example of parameter input.

```
java -jar core.jar Case^
relationFilePath
similarityFilePath 
graphFilePath 
vertexTypeFilePath 
queryRelation 
AaType
queryID 
l
queryK
r
threads
writeNodeFilePath
writeEdgeFilePath
```

We still use the reduced *DBpedia* for testing with the following experimental parameters. It should be noted that the node set and the $\mathcal{P}^*$-edge set are stored separately in different designated files. All required files are stored in the **data** folder.

Input

```
java -jar core.jar Case^
  "./data/dbpedia/relation2id.txt" ^
  "./data/dbpedia/similaritytotalfile.txt" ^
  "./data/dbpedia/DbpediaGraphWithPredicate.txt" ^
  "./data/dbpedia/DbpediaVertex.txt" ^
  director 164 6122536 3 7 3 10 ^
  "./data/dbpedia2023Gu/caseNodes.txt" ^
  "./data/dbpedia2023Gu/caseEdges.txt"

```

Output

```
queryRelation: director
AaType: 164
queryID: 6122536
l: 3
queryK: 7
r: 3
read graph ~~~
read graph over ~~~
Node file over!
Edge file over!
[director, editing]
result:655
Node file over!
Edge file over!
[executiveProducer, director, starring, editing]
result:34032
Node file over!
Edge file over!
[executiveProducer, director, starring, editing, cinematography]
result:36481


```

Here is the output of node set for the above query example:

```
id,label,community
6949994,John_Farrow,1
6891625,Delmer_Daves,1
7299182,Ralph_Dawson,1
...


```

Here is the output of P\*-edge set for the above query example:

```
id,target,label,community
6949994,4474141,[6949994-director-4958557-editing-4474141][6949994-director-5264031-editing-4474141][6949994-director-5307556-editing-4474141][6949994-director-5459690-editing-4474141][6949994-director-5169782-editing-4474141][6949994-director-2911410-editing-4474141][6949994-director-62041-editing-4474141],1
6949994,2172058,[6949994-director-3723988-editing-2172058][6949994-director-2829666-editing-2172058][6949994-director-2373049-editing-2172058][6949994-director-6011433-editing-2172058][6949994-director-974207-editing-2172058][6949994-director-3311782-editing-2172058][6949994-director-3635791-editing-2172058][6949994-director-4040401-editing-2172058][6949994-director-4570348-editing-2172058][6949994-director-511964-editing-2172058][6949994-director-226646-editing-2172058][6949994-director-5235401-editing-2172058][6949994-director-2012569-editing-2172058][6949994-director-6539905-editing-2172058][6949994-director-4713753-editing-2172058][6949994-director-3678799-editing-2172058][6949994-director-5554785-editing-2172058][6949994-director-5347649-editing-2172058],1
6949994,1410889,[6949994-director-3191771-editing-1410889],1
...


```

