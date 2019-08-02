---
title: "Myblog"
date: 2019-08-02T17:44:41+08:00
draft: true
---
Mysql存储过程
    
      SQL语句需要先编译然后执行，而存储过程（Stored Procedure）是一组为了完成特定功能的SQL语句集，经编译后存储在数据库中，用户通过指定存储过程的名字并给定参数（如果该存储过程带有参数）来调用执行它。存储过程是可编程的函数，在数据库中创建并保存，可以由SQL语句和控制结构组成。当想要在不同的应用程序或平台上执行相同的函数，或者封装特定功能时，存储过程是非常有用的。数据库中的存储过程可以看做是对编程中面向对象方法的模拟，它允许控制数据的访问方式。

存储过程的优点：

      (1).增强SQL语言的功能和灵活性：存储过程可以用控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算。

      (2).标准组件式编程：存储过程被创建后，可以在程序中被多次调用，而不必重新编写该存储过程的SQL语句。而且数据库专业人员可以随时对存储过程进行修改，对应用程序源代码毫无影响。

      (3).较快的执行速度：如果某一操作包含大量的Transaction-SQL代码或分别被多次执行，那么存储过程要比批处理的执行速度快很多。因为存储过程是预编译的。在首次运行一个存储过程时查询，优化器对其进行分析优化，并且给出最终被存储在系统表中的执行计划。而批处理的Transaction-SQL语句在每次运行时都要进行编译和优化，速度相对要慢一些。

      (4).减少网络流量：针对同一个数据库对象的操作（如查询、修改），如果这一操作所涉及的Transaction-SQL语句被组织进存储过程，那么当在客户计算机上调用该存储过程时，网络中传送的只是该调用语句，从而大大减少网络流量并降低了网络负载。

      (5).作为一种安全机制来充分利用：通过对执行某一存储过程的权限进行限制，能够实现对相应的数据的访问权限的限制，避免了非授权用户对数据的访问，保证了数据的安全。


存储过程创建：
1）存储过程结构

  CREATE PROCEDURE myproc(OUT s int)
    BEGIN
      SELECT COUNT(*) INTO s FROM students;
      //存储过程体
    END

    存储过程主体以BEGIN开始，以END结尾，存储过程可以指定输入输出参数，（OUT S INT）out用以指定存储过程的输入输出类型（out类型类似与java中的方法返回值），S为变量名，int指定变量类型,out属性可以不指定；
    存储过程体可以是自定义的语言，也可以是SQL语言。

2）变量的声明
     存储过程中对于变量的声明主要由两种方式：
     第一种方式：
	declare 变量名 数据类型 default 初始值（类似强类型语言变量定义）；
     第二种方式：
	set 变量名=变量值（类似弱语言类型变量定义）；
    变量可以结合SQL语言进行赋值：
	select * from into 变量名 from table where 查询条件；(将*号的值传给变量，但是需要注意*的查询结果是否为多个)；

	注意：建议在变量名的复制的时候采用@开头，同时变量赋值最好不要用与数据库的同名字段（在使用游标存放数据库表中的字段内容时这样做会导致数据无法进入游标）；

3）变量的作用域：

      存储过程的变量作用域分为全局变量（指的是整个存储过程）与局部变量，通过BEGIN---END的结构实现变量作用域的划分。

	例：
	CREATE PROCEDURE myprocdure()
	  BEGIN
	        declare a int default 0;
	        BEGIN
		declare a int default 5;
		select a;
	        END
	        select a;
	  END

	在新建查询中调用该存储过程及其结果：
		call myrodure;
	结果为2个result:  5,0
	
4）流程控制语句：
        条件语句：
	CREATE PROCEDURE myprocdure()
	    BEGIN
	         declare s int default 0;
	         set s=5;
	         if s>0 then
	 	insert into table1 (studentName,studentId) values('张三',1001);
	        end if;
	        if s>3 then 
		s=s+1;
	       else
		s=s-1;
	       end if;
	    END
       循环语句：
	create procedure proc5()
	     BEGIN
	          declare a int default 1;
	          while a<=10
		insert into table1(amount,price)values(a,50);
		set a=a+1;
	          END WHILE
	     END



案例：

主表结构：
DROP TABLE IF EXISTS `order_1001`;
CREATE TABLE `order_1001`  (
  `xh` int(11) NOT NULL AUTO_INCREMENT COMMENT '序号',
  `ddbh` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '订单编号',
  `khmc` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '客户名称',
  `khbh` int(10) NULL DEFAULT NULL COMMENT '客户编号',
  `htrq` datetime(0) NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP(0) COMMENT '订单合同日期',
  `jfrq` datetime(0) NULL DEFAULT NULL COMMENT '订单预计完成日期',
  `zjsl` int(10) NULL DEFAULT NULL COMMENT '主机数量',
  `ddxq` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '订单详情',
  `whr` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '维护人',
  `whsj` date NULL DEFAULT NULL COMMENT '维护时间',
  `bz` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '备注',
  `zjwcsl` int(10) NULL DEFAULT NULL COMMENT '主机完成量',
  `ddwcqk` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '订单完成情况',
  PRIMARY KEY (`xh`) USING BTREE,
  INDEX `ddbh`(`ddbh`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 2019040502 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

副表结构:
DROP TABLE IF EXISTS `order_1002`;
CREATE TABLE `order_1002`  (
  `xh` int(10) NULL DEFAULT NULL COMMENT '序号',
  `zjh` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '主机号',
  `zjxh` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '主机规格',
  `zjgg` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '主机规格',
  `ddh` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '关联订单号',
  `gw` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '工位',
  `scx` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '生产线',
  `whr` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '维护人',
  `whsj` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '维护时间',
  `bz` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '备注',
  INDEX `billNo`(`zjh`) USING BTREE,
  INDEX `ddh`(`ddh`) USING BTREE,
  CONSTRAINT `order_1002_ibfk_1` FOREIGN KEY (`ddh`) REFERENCES `order_1001` (`ddbh`) ON DELETE RESTRICT ON UPDATE RESTRICT
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

需求描述：根据主表的订单号以及主机数量在副表中批量插入数据，确保每个附表中每个订单所属的主机数据条数与主表的主机数量匹配。

存储过程如下：

CREATE DEFINER=`root`@`localhost` PROCEDURE `showzj`()
BEGIN
    DECLARE a_ddh varchar(255);
    DECLARE b_zjsl INT(10);
    DECLARE done INT DEFAULT 0;
    declare i int default 0;

    DECLARE cur CURSOR FOR
    SELECT ddbh,zjsl
    FROM order_1001;
    
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done=1;
    
    OPEN cur;
		
    emp_loop:LOOP
		
        FETCH cur INTO a_ddh,b_zjsl;
				
        	IF done=1 THEN    
          	  LEAVE emp_loop;
      	       END IF;
				
			while(i<b_zjsl) DO
					insert into order_1002 (zjh,ddh) values(i,a_ddh);
					set i=i+1;
			end while;
				
			set i=0;
       			
    END LOOP emp_loop;
		
    CLOSE cur;

END

