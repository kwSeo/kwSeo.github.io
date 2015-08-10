---
layout: post
title: "MariaDB에서 CSV파일 로드할 때 주의해야 할 점"
---

#Summary
- CSV파일을 로드하기 위해 LOAD DATA INFILE를 사용한다면, 처음 MariaDB Client 실행시 '--local-infile'옵션울 주어야한다.
- CSV파일은 쉼표(',')로 구분하는데 종종 컬럼의 데이터가 쉼표를 포함하고 있는 경우가 있다.  

#MariaDB에서 CSV파일 읽어들이기

	$mysql -u root -p --local-infile
	
	LOAD DATA LOCAL INFILE "file_path"
	INTO TABLE your_db.your_table
	FIELDS TERMINATED BY ','
	ENCLOSED BY '\"'
	LINES TERMINATED BY '\n'

MariaDB에서 CSV파일을 로드하고자 할 때에는 위와 같이 LOAD하면 됩니다. 'file_path'에 해당하는 경로의 CSV파일로부터 ','로 구분하고 줄은 '\n'로 구분합니다. 또한 각 컬럼은 '"'로 둘러쌓여있다는 의미로, 지정한 CSV파일로부터 'your_db.your_table'테이블로 읽어들입니다. 이때 주의해야 할 점은 처음 MariaDB Client를 실행할 때  '**--local-infile**'이라는 옵션이 필요하다는 것입니다. 이 옵션을 주지 않으면 LOAD DATA INFILE 문법을 사용할 수 없습니다.

#CSV는 쉽표(',')로 구분한다 <- 중요!!!
CSV파일은 각 컬럼을 **쉽표(',')로 구분**합니다. 그 덕에 CSV파일은 매우 간단해 보얼 것입니다. 하지만 때때로 이 특징 때문에 문제가 발생할 때가 있습니다. LOAD DATA INFILE을 통해 값을 읽어들일때 컬럼 안의 값이 쉼표를 포함하게 있다면 그 쉼표를 기준으로 컬럼을 나누어 버린다는 것입니다. 즉 처음 사람이 이 데이터를 입력할때 우리가 알고 있는 일반적인 의미의 쉼표로 사용한 것이지만 컴퓨터는 이를 구분자로 인식한다는 것입니다. 이런 문제는 'FIELDS TERMINATED BY'를 통해 구분자를 바꾼다고 해서 해결되지 않습니다. 왜냐하면 CSV는 쉼표로 구분하니까요... 그러니 읽어들이고자 하는 CSV파일 안에 구분자 이외의 의도로 사용된 쉼표가 없는지 확인해야합니다. 

저는 처음 CSV파일을 로드할때 'ENCLOSED BY'로 컬럼의 데이터가 무엇으로 묶여있는지 지정해 주어서 데이터 내의 쉼표는 알아서 처리해 줄 것이라고 생각혔었는데 그게 아니더라구요. 제가 사용했던 버전은 MariaDB 10.0.20이었습니다. 


