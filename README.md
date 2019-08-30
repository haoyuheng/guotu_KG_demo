# guotu_KG_demo
### 导入脚本

#### 1、导入相关分类

导入行政划分

```cypher
LOAD CSV FROM "file:///dm3.csv" AS line
MERGE (n:DM {code: line[3]})
SET n.type = line[1],n.name = line[2]
RETURN n
```

导入行政关系

```cypher
LOAD CSV FROM "file:///dm3.csv" AS line
MATCH (n1:DM{code: line[3]}),(n2:DM{code: line[4]})
WHERE line[4] <> 0
MERGE (n1)-[r:DM_BELOGNGS_TO {type:'dm_belongs_to'}]->(n2)
return r
```

导入地类代码和名称  

```cypher
LOAD CSV WITH HEADERS FROM "file:///DL.csv" AS line
MERGE (n:DL {code: line.DLBM})
SET n.name = line.DLMC
RETURN n
```

导入要素代码和分类

```cypher
LOAD CSV WITH HEADERS FROM "file:///YS.csv" AS line
MERGE (n:YS {code: line.code})
SET n.name = line.name
RETURN n
```

导入扣除系数，0.5/1，用于线状地物分类

```cypher
CREATE (n:KCBL { KCBL: '0.5', title: '0.5' }),(m:KCBL { KCBL: '1', title: '1' })
```

导入权属性质分类，10/20/30

```cypher
CREATE (p1:QSXZ { QSXZ: '10', title: '国有土地所有权' }),(p2:QSXZ { QSXZ: '20', title: '国有土地使用权' }),(p3:QSXZ { QSXZ: '30', title: '集体土地所有权' }),(p4:QSXZ { QSXZ: '31', title: '村民小组' }),(p5:QSXZ { QSXZ: '32', title: '村集体经济组织' }),(p6:QSXZ { QSXZ: '33', title: '乡集体经济组织' }),(p7:QSXZ { QSXZ: '34', title: '其他农民集体经济组织' }),(p8:QSXZ { QSXZ: '40', title: '集体土地使用权' })
```

导入扣除类型，扣除类型指按田坎系数（TK）、按比例扣除的散列式其他非耕地系数（FG）或耕地系数（GD）

```cypher
CREATE (n:KCLX { KCLX: 'TK', title: '按田坎系数' }),(m:KCLX { KCLX: 'FG', title: '按非耕地系数' }),(p:KCLX { KCLX: 'GD', title: '按耕地系数' })
```

导入耕地类型，当地类为梯田耕地时，耕地类型填写“T”；TT（梯田）、PD（坡耕地）（二调本来就有） 

```cypher
CREATE (n:GDLX { GDLX: 'T', title: '梯田耕地' })
```

#### 2、导入地类图斑

导入土地及其属性

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MERGE (n:DLTB {BSM: line.BSM})
SET n.QSXZ = line.QSXZ, n.KCLX = line.KCLX,n.GDPDJ = line.GDPDJ, n.KCDLBM = line.KCDLBM, n.TKXS = line.TKXS,n.TBMJ = line.TBMJ, n.XZDWMJ = line.XZDWMJ,n.LXDWMJ = line.LXDWMJ, n.TKMJ = line.TKMJ, n.TBDLMJ = line.TBDLMJ
RETURN count(n)
```

导入土地要素关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:YS{code: line.YSDM})
MERGE (n1)-[r:HAS_YS {type:'has_ys'}]->(n2)
return r
```

导入土地地类关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DL{code: line.DLBM})
MERGE (n1)-[r:HAS_DL {type:'has_dl'}]->(n2)
return r
```

导入扣除类型关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:KCLX{KCLX: line.KCLX})
MERGE (n1)-[r:HAS_KCLX {type:'has_kclx'}]->(n2)
return r
```

导入耕地类型关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:GDLX{GDLX: line.GDLX})
MERGE (n1)-[r:HAS_GDLX {type:'has_gdlx'}]->(n2)
return r
```

导入土地权属关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM})
MERGE (n1)-[r:HAS_QSDW {type:'has_qsdw'}]->(n2)
return r
```

导入土地权属性质关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:QSXZ {QSXZ: line.QSXZ})
MERGE (n1)-[r:HAS_QSXZ {type:'has_qsxz'}]->(n2)
return r
```

~~导入坐落单位关系(数据中和权属关系一致，未导入)~~

```cypher
LOAD CSV WITH HEADERS FROM "file:///dltb_5000.csv" AS line
MATCH (n1:DLTB{ BSM: line.BSM }),(n2:DM{code: line.ZLDWDM})
MERGE (n1)-[r:HAS_ZLDW {type:'has_zldw'}]->(n2)
return r
```

#### 3、导入零星地物

导入土地及其属性

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MERGE (n:LXDW {BSM: line.BSM})
SET n.X = line.X, n.Y = line.Y,n.MJ = line.MJ, n.QSXZ = line.QSXZ, n.ZLTBBH = line.ZLTBBH
RETURN count(n)
```

导入土地要素关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:YS{code: line.YSDM})
MERGE (n1)-[r:HAS_YS {type:'has_ys'}]->(n2)
return r
```

导入土地地类关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DL{code: line.DLBM})
MERGE (n1)-[r:HAS_DL {type:'has_dl'}]->(n2)
return r
```

导入土地权属关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM})
MERGE (n1)-[r:HAS_QSDW {type:'has_qsdw'}]->(n2)
return r
```

导入土地权属性质关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:QSXZ {QSXZ: line.QSXZ})
MERGE (n1)-[r:HAS_QSXZ {type:'has_qsxz'}]->(n2)
return r
```

~~导入坐落单位关系(数据中和权属关系一致，未导入)~~

```cypher
LOAD CSV WITH HEADERS FROM "file:///lxdw.csv" AS line
MATCH (n1:LXDW{ BSM: line.BSM }),(n2:DM{code: line.ZLDWDM})
MERGE (n1)-[r:HAS_ZLDW {type:'has_zldw'}]->(n2)
return r
```

#### 4、导入线状地物

导入土地及其属性

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MERGE (n:XZDW {BSM: line.BSM})
SET n.XZDWYBH = line.XZDWYBH, n.XZDWBH = line.XZDWBH,n.CD = line.CD, n.KD = line.KD, n.KCTBBH1 = line.KCTBBH1,n.KCTBBH2 = line.KCTBBH2, n.QSXZ = line.QSXZ, n.KCBL = line.KCBL, n.BGJLH = line.BGJLH, n.BGRQ = line.BGRQ, n.SHAPE_Leng = line.SHAPE_Leng
RETURN count(n)
```

导入土地要素关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:YS{code: line.YSDM})
MERGE (n1)-[r:HAS_YS {type:'has_ys'}]->(n2)
return r
```

导入土地地类关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:DL{code: line.DLBM})
MERGE (n1)-[r:HAS_DL {type:'has_dl'}]->(n2)
return r
```

导入扣除系数分类关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:KCBL{KCBL: line.KCBL})
MERGE (n1)-[r:HAS_KCBL {type:'has_kcbl'}]->(n2)
return r
```

**导入土地权属关系**

左权属关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM1})
MERGE (n1)-[r:HAS_LEFT_QSDW {type:'has_left_qsdw'}]->(n2)
return r
```

右权属关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:DM {code: line.QSDWDM2})
MERGE (n1)-[r:HAS_RIGHT_QSDW {type:'has_right_qsdw'}]->(n2)
return r
```

导入土地权属性质关系

```cypher
LOAD CSV WITH HEADERS FROM "file:///xzdw_5000.csv" AS line
MATCH (n1:XZDW{ BSM: line.BSM }),(n2:QSXZ {QSXZ: line.QSXZ})
MERGE (n1)-[r:HAS_QSXZ {type:'has_qsxz'}]->(n2)
return r
```