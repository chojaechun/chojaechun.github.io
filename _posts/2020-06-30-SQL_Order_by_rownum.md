---
layout: post
title:  "SQL- Sub Query : Order by 와 rownum을 같이 사용하는 경우 조회 오류 처리"
date:   2020-06-17 18:01:00 +0900
categories:  [Development]
comments: true
---

<html>
 <body>
<br>             
<br>    처음 알았다.....
<br>    
<br>    Order by 와 rownum을 같이 사용할 경우 조회가 정상적으로 되지 않는 것을
<br>    
<br>    
<br>    ex )
<br>    
  <br>    <b>Table    : TABLE_A</b>
  <br>    <b>Column   :     A,  B, CREATE_DATE</b>
  <br>    <b>Row Data : 1) TE, ST, 2020-06-29 10:00:00</b>
  <br>&emsp;&emsp;&emsp;&ensp; <b>2) ST, TE  2020-06-29 10:00:10</b>
<br>    
<br>    
<br>    SELECT TABLE_B.A, TABLE_B.B
<br>      FROM (  SELECT A, B
<br>                FROM TABLE_A
<br>              ORDER BY CREATE_DATE DESC
<br>           ) TABLE_B
<br>     WHERE ROWNUM = 1
<br>     
<br>     결과 : 1) TE, ST, 2020-06-29 10:00:00
<br>     
<br>     
<br>     당연 2번 ( 당연 TABLE_A 의 최근에 생성된 ROW 순으로 조회) 에 대한 결과가 나올 것이라 생각했지만, 
<br>     
<br>     SQL 의 순서 조회 조건은 ROWNUM을 먼저 실행하고 ORDER BY 를 진행하게 되어있다고 한다..
<br>     
<br>     
<br>     원하는 결과값을 도출하기 위해선 ROW_NUMBER() OVER ( ORDER BY "원하는 Sort 조회 Column" DESC ) - 이것을 사용하면 된다!
<br>    
<br>    
<br>    
<br>    ex 1 )
<br>    
<br>    SELECT TABLE_B.A, TABLE_B.B
<br>      FROM (  SELECT A, B, ROW_NUMBER() OVER ( ORDER BY CREATE_DATE DESC )
<br>                FROM TABLE_A
<br>              ORDER BY CREATE_DATE DESC
<br>           ) TABLE_B
<br>     WHERE ROWNUM = 1
<br>    
<br>    결과 : 2) ST, TE  2020-06-29 10:00:10
<br>    
<br>    
<br>    ex 2 )
<br>    
<br>    SELECT TABLE_B.A, TABLE_B.B, TABLE_B.SORT
<br>      FROM (  SELECT A, B, ROW_NUMBER() OVER ( ORDER BY CREATE_DATE DESC ) AS SORT
<br>                FROM TABLE_A
<br>              ORDER BY CREATE_DATE DESC
<br>           ) TABLE_B
<br>     WHERE TABLE_B.SORT = 1
<br>    
<br>    결과 : 2) ST, TE  2020-06-29 10:00:10, 1
<br>    
<br>    
<br>    
<br>    ex 1 의 내용을 보면 ex 2 와 다르게 컬럼명을 지정하지 않았음에도 정상적으로 조회가 된다.
<br>    이는 ROW_NUMBER 함수는 지정된 컬럼 ( CREATE_DATE ) 을 참조해 순서에 맞게 고유의 번호를 할당하게 된다.
<br>    ROWNUM 은 이 고유 번호를 참고하여 순서 조건에 맞는 제일 최상단의 데이터를 가져오게 된다.
<br>    
  </body>
</html>
