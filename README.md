# guotu_KG_demo
导入脚本
1、导入行政划分

LOAD CSV FROM "file:///dm3.csv" AS line
MERGE (n:DM {code: line[3]})
SET n.type = line[1],n.name = line[2]
RETURN n

导入行政关系

LOAD CSV FROM "file:///dm3.csv" AS line
MATCH (n1:DM{code: line[3]}),(n2:DM{code: line[4]})
WHERE line[4] <> 0
MERGE (n1)-[r:DM_BELOGNGS_TO {type:'dm_belongs_to'}]->(n2)
return r

2、导入地类代码和名称  
LOAD CSV WITH HEADERS FROM "file:///DL.csv" AS line
MERGE (n:DL {code: line.DLBM})
SET n.name = line.DLMC
RETURN n

3、 导入要素代码和分类
LOAD CSV WITH HEADERS FROM "file:///YS.csv" AS line
MERGE (n:YS {code: line.code})
SET n.name = line.name
RETURN n

4、导入地类图斑
LOAD CSV WITH HEADERS FROM "file:///dltb.csv" AS line
MERGE (n:DLTB {BSM: line.BSM})
SET n.X = line.X, n.Y = line.Y,n.MJ = line.MJ, n.QSXZ = line.QSXZ, n.ZLTBBH = line.ZLTBBH
RETURN count(n)

导入土地要素关系
LOAD CSV WITH HEADERS FROM "file:///dltb.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:YS{code: line.YSDM})
MERGE (n1)-[r:HAS_YS {type:'has_ys'}]->(n2)
return r

导入土地地类关系

LOAD CSV WITH HEADERS FROM "file:///dltb.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DL{code: line.DLBM})
MERGE (n1)-[r:HAS_DL {type:'has_dl'}]->(n2)
return r

导入土地权属关系
LOAD CSV WITH HEADERS FROM "file:///dltb.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM})
MERGE (n1)-[r:HAS_QSDW {type:'has_qsdw'}]->(n2)
return r

导入坐落单位关系
LOAD CSV WITH HEADERS FROM "file:///dltb.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DM{code: line.ZLDWDM})
MERGE (n1)-[r:HAS_ZLDW {type:'has_zldw'}]->(n2)
return r


5、导入零星地物,并设置属性
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MERGE (n:LXDW {BSM: line.BSM})
SET n.X = line.X, n.Y = line.Y,n.MJ = line.MJ, n.QSXZ = line.QSXZ, n.ZLTBBH = line.ZLTBBH
RETURN count(n)

导入土地要素关系
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:YS{code: line.YSDM})
MERGE (n1)-[r:HAS_YS {type:'has_ys'}]->(n2)
return r

导入土地地类关系

LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DL{code: line.DLBM})
MERGE (n1)-[r:HAS_DL {type:'has_dl'}]->(n2)
return r

导入土地权属关系
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM})
MERGE (n1)-[r:HAS_QSDW {type:'has_qsdw'}]->(n2)
return r

导入坐落单位关系
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DM{code: line.ZLDWDM})
MERGE (n1)-[r:HAS_ZLDW {type:'has_zldw'}]->(n2)
return r
