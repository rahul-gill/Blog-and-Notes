---
layout: default
name: _notes/dbms/More Database Design.md
categories: Databases
date: 2021-10-19
---
${toc}
# Steps in design
- requirement analysis: what our database do
- conceptual design
	- usually ER model
	- object relational mappings(ORMs: rails, django etc.)
- logical design: translate conceptual design to DBMS data model
- schema refinement: consistency, normalization
- physical design: indexes, disk layout
- security design: who accesses what and how


# Informal Design Guidelines
- easy to explain schema, do not mix attributes from mutiple entity types into a single relation.
- minimize redundant informaton and update anomalies
- as far as possible, avoid placing attributes in a base relation whose values may frequently be `NULL`.
- Design relation schemas so that they can be joined with equality conditions on attributes that are appropriately related (primary key, foreign key) pairs in a way that guarantees that no spurious tuples are generated.
## anomalies due to poor design
lets say Employee and Department data is store in a single relation EMP_DEPT
- insertion anomalies: 
	- to insert a new Employee in the relation, we must either include attributes for the department of the employee(like name, locations of department etc.) or Nulls and these attributes must be consistent for corresponding department in other tuples.
	- to add a department with no employee, we'll have to make all attributes related to employee Null and it'll violate entity integrity if {employee_id, department_id} is the primary key
- deletion anomalies:
	- if we delete the last employee of the department, same situation as second insertion anomaly will occur
- modification anomalies:
	- if we want to change any attribute of a department, we'll have to update the value of that attribute in tuples of all employees who work in that department. 
# Functional dependencies
- lets say our database schema has $n$ attributes $A_1,A_2,...,A_n$ and the whole database is descibed by a single universal relation schema $R=\{A_1,A_2,...,A_n\}$ then
A functional dependency $X\to Y$ between two attribute subsets $X$ and $Y$ of $R$ specify a contraint on the relational states of $R$, that if tuples $t_1$ and $t_2$ in a stae have $t_1[X]=t_2[X]$ then they must also have $t_1[Y]=t_2[Y]$
- this means values of $X$ component of a tuple uniquely(or functionally) determine values of $Y$ component. We say that $Y$ is functionally dependent on $X$.
- if $X$ is candidate key of $R$ then $X\to R$ 
- the functional dependency $X\to Y$ is **trivial** if $Y\subseteq X$, else non-trivial
# Closure and Inference Rules
- an functional dependency $X\to Y$	is **inferred** from or implied by a set of dependencies $F$ specified on schema $R$, if $X\to Y$ holds in every legal state of $R$. That is, when $F$ are hold, $X\to Y$ is also hold. We denote this by $F|=X\to Y$
- **closure of a set of functional dependencies** $F$, denoted by $F^+$, is set of all functional dependencies that are in $F$ and those which can be inferred from $F$.
- **closure of attribute set** is set of attributes which can be functionally determined from the attributes in that set w.r.t $F$. We can denote closure of attribute set $X$ by $X^+$
	- computing closure of a set of dependencies is hard so we can compute this instead
	- $X^+=$ set of all attributes $A$ such that $X\to A$ is in $F^+$
-  inference rules can be used to infer all new dependencies from a given set of dependencies
	-  if $Y\subseteq X$ then $X\to Y$
	-  $\{X\to Y\}|=XZ\to YZ$
	-  ${X\to Y, Y\to Z}|=X\to Z$
- a functional depedency set $F$ is set to **cover** another set $E$, if every functional depdency in $E$ can be inferred from $F$(is in $F^+$). We say that $E$ is covered by $F$
- two set of functional depedencies $E$ and $F$ are called **equivalent** if $E^+=F^+$
- **minimal cover** of set of functional dependency $F$ is minimal set of depdencies which is equivalent to $F$
- algorithm to find the closure $F^+$ of attribute set $X$ under $F$
	$X^+ :=  X$
	$oldX^+ := X^+$
	$\text{do}:$
	$\quad \text{for}((Y\to Z) \text{ in } F)$:
	$\quad\quad \text{if}(Y\subseteq X^+): X^+=:X^+\cup Z$
	$\text{while}(X^+  \neq oldX^+)$
- algorithm to find key $K$ of $R$ given functional dependency set $F$
	- returns any of one candidate key depending on attribute removal order
	$K:=R$
	$for(A \text{ in } K):$
	$\quad if(R= (K-A)^+): K:=K-\{A\}$
- algorithm to find minimal cover $F$ of dependency set $E$	
	- the if line checks if removing $B$ from $X$ won't affect $F$	
	$F:=E$
	$\text{replace } X\to \{A_1,A_2,...,A_n\} \text{ in } F \text{ with } X\to A_1,X\to A_2,...,X\to A_n$
	$for((X\to A) \text{ in } F):$
	$\quad for(B \text{ in } X):$
	$\quad\quad \text{if}(\ (F-X\to A)\cup(X-\{B\}\to A) \text{  in } F)$:
	$\quad\quad\quad \text{replace }X\to A \text{ with } X-\{B\}\to A \text{ in } F$
	$for((X\to A) \text{ in } F):$
	$\quad if((F-X\to A) \text{ is equivalent to } F)$:
	$\quad\quad F=(F-X\to A)$

# Normal forms
- the normalization process takes a relational schema through a series of tests to certify whether it satisfies a certain normal form
-  process of normalization through decomposition must also confirm the existence of additional properties
	- nonadditive join or lossless join property
	- dependency preservation property
- relations may be left in a lower normalization status, for performance reasons and database design as practiced in industry today pays particular attention to normalization only up to 3NF, BCNF, or at most 4NF because the constraint may be hard to detect or understand.
<br>

- an attribute of a relation schema is called **prime attribute**, if it is member of some candidate key of schema.
## 1NF
- States that domain of an attribute must only include atomic values and value of an attribute must be a single value from that domain
- so it disallow multivalued attributes, composite attributes and their combination
- now considered part of formal relational model
- we can decompose composite composite attributes and extract multi-valued attributes in seperate relation to make the schema 1NF.
## 2NF
- $X\to Y$ is **fully functional dependency** if any attribute from $X$ is removed, the dependency won't hold. In partial functional dependency some attribute can be removed and the dependency would still hold.
- a relational schema is 2NF, if every non-prime attribute is fully functionally dependent on every key of the relation, or no no-prime attribute is partially functionally dependent on some key.
- or no proper subset of a key can determine a non-prime attribute, if schema is 2NF.
- the test involves testing functional dependencies with key attributes on left hand side. If the left side is single attribute primary key, the test is done 
- for example this relation is not 2NF: 
(<u>EmployeeSSN</u>, <u>ProjectNo</u>, Hours, EmployeeName, ProjectName, ProjectLocation) 
	it can be made 2NF by dividing it into 3 relations 
	(<u>EmployeeSSN</u>, <u>ProjectNo</u>, Hours)
	(<u>EmployeeSSN</u>, EmployeeName)
	(<u>ProjectNo</u>, ProjectName, ProjectLocation)
- another example: (<u>property_id</u>, country_name, lot_no, area, price, tax_rate) and lets say {country_name,lot_no} is a candidate key, price depends only on the area, tax_rate depends on the country_name.
	then this schema is 2NF, because tax_rate is partially dependent on key {country_name,lot_no}, to make it 2NF, we divide it into two relations:
	(<u>property_id</u>, country_name, lot_no, area, price)
	(<u>country_name</u>, tax_rate)
## 3NF
- **transitive dependency**: $X\to Y$ is transitive functional dependency, if there exists a set of attributes $Z$ which is neither a candidate key nor a subset of any key of the relation and both $X\to Z$ and $Z\to Y$ hold.
	or if $X$ is not a super key and $B$ is a non-prime attribute.
- A relational schema is 3NF, if its 2NF and no non-prime attribute of it is transitively dependent on any key
- or no non-prime attribute can determine another non-prime attribute
- for example EmployeeSSN $\to$ DepartmentNo and DepartmentNo $\to$ DeparmentManagerSSN and DepartmentNo is not a subset of any key of the relation 
	(Ename, <u>EmployeeSSN</u>, Bdate, Address, DepartmentNo, DepartmentName, DeparmentManagerSSN)
	we can make this schema 3NF by dividing it into two relations
	(Ename, <u>EmployeeSSN</u>, Bdate, Address, DepartmentNo)
	(<u>DepartmentNo</u>, DepartmentName, DeparmentManagerSSN)
- another example: (<u>property_id</u>, country_name, lot_no, area, price) is not 3NF because area $\to$ price and (area is not a super key) and (price is non-prime)
	we divide it into two relations to make it 3NF
	(<u>property_id</u>, country_name, lot_no, area)
	(<u>area</u>, price)
- so for a 3NF schema, every non-prime attribute is
	- fully functionally dependent on every key
	- non-transitively dependent on every key

## Boyce Codd Normal Form(BCNF)
- a relational schema is BCNF, if for every non-trivial functional dependency $X\to Y$ in it, $X$ is a superkey.
- its simpler
- in, practice most 3NF schemas are also BCNF
- a general example of 3NF, but not BCNF schema
![5302d29f1efe95e805e6c4b94ca68e5d.png](/assets/images/5302d29f1efe95e805e6c4b94ca68e5d.png)



# Properties of Relational decomposition
- dividing a relation into multiple relations is decomposition
- problems that can occur with decompositions:
	- lossiness(it may be impossible to recostruct the original relation)
	- some dependency checking may require joins(dependecies are not obvious from relations themselves)
	- some queries become more expensive(in-escapable)
- so just making relational schema of higher normal form alone doesn't make the design good.
## depedendency preservation 
if the dependecies specified on original relations are either directly appear in decomposition or could be inferred from the dependencies that appear in the decomposition(problem#2)
- given a set of dependencies $F$ on $R$, the **projection** of $F$ on $R_i$, $F_{R_i}$ where $R_i\subseteq R$ is set of dependencies $X\to Y$ in $F^+$ s.t. attributes in $X\cup Y$ are contained in $R_i$.
	- so its set of those dependencies $X\to Y$ in $F^+$ where both $X$ and $Y$ are subsets of $R_i$
- formal definition: a decomposition $D=\{R_1,R_2,...,R_m\}$ of $R$ is dependency preserving w.r.t $F$, if union of projections of $F$ on each $R_i$ in $D$ is equivalent to $F$, that is
	$\left(\bigcup\limits_{i=1}^{m} F_{R_i}\right)^+=F^+$
- an example: when LOT(<u>property_id</u>, country_name, lot_no, area), where 
	FD1: area$\to$country_name and FD2:{country_name, lot_no)$\to$(property_id, area). 
	When we decompose it in BCNF this way:
	LOTX(<u>property_id</u>,area,lot_no)
	LOTY(<u>area</u>, country_name)
	Here FD2 is not preserved
- another example: $ABC$ with $A\to B$, $B\to C$ and$C\to A$ decomposed in $AB$ and $BC$
	- $C\to A$ is preserved because
		by dependency 1 and 3: $C\to B$ and by 2 and 3 $B\to A$
		so by both of these $C\to A$
## Non-additive(lossless) join property
- decomposition $D=\{R_1,R_2,...,R_m\}$ of $R$ is non-additive(or lossless) w.r.t set of dependencies $F$, if for every relation state $r$ of $R$ that satisfy $F$, the natural join of all $\pi_{R_i}(r)$s is equal to $r$
- for decomposition of $R$ into two relations $R_1$ and $R_2$ and relation state $r$ satisfying $F$  
	- its always true that $r\subseteq \pi_{R_1}(r) \bowtie \pi_{R_2}(r)$
	- when equality holds, the relation is lossless
- lossy example: 

|A|B|C|
|-|-|-|
|1|2|3|
|4|5|6|
|7|2|8|
with $A\to B$ and $B\to C$, decomposed using second dependency:
<table>
<tr><td>

|A|B|
|-|-|
|1|2|
|4|5|
|7|2|

</td><td>

|B|C|
|-|-|
|2|3|
|5|6|
|2|8|

</td></tr> </table>
natural  joining these we get two spurious tuples(last 2)

|A|B|C|
|-|-|-|
|1|2|3|
|4|5|6|
|7|2|8|
|1|2|8|
|7|2|3|

- theorem: decomposition of $R$ into $X$ and $Y$ is lossless w.r.t $F$ if and only if $F^+$ contains  
	$X\cap Y\to X$ or $X\cap Y\to Y$
	- for example in last example decomposing ABC in AB and BC was lossy because their intersection B is not a key of either resulting relations