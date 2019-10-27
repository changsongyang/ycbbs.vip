���䣺������ô�����Ѷ�sql�Ż�����Ȥ���������²����������µ����ݣ����������sql�Ż���֪ʶ���������

ϲ����ƪ���µ����Ѹ����ްɣ���������ӭ��������ͬ������

## ����

���õ����ݿ���mysql5.6������򵥵Ľ����³���

�γ̱�

```
create table Course(

c_id int PRIMARY KEY,

name varchar(10)

)
```

����100��

ѧ����:

```
create table Student(

id int PRIMARY KEY,

name varchar(10)

)
```

����70000��

ѧ���ɼ���SC

```
CREATE table SC(

    sc_id int PRIMARY KEY,

    s_id int,

    c_id int,

    score int

)
```

����70w��

��ѯĿ�ģ�

�������Ŀ�100�ֵĿ���

��ѯ��䣺

```
select s.* from Student s where s.s_id in (select s_id from SC sc where sc.c_id = 0 and sc.score = 100 )
```

ִ��ʱ�䣺30248.271s

��,Ϊʲô��ô���������鿴�²�ѯ�ƻ���

```
EXPLAIN 

select s.* from Student s where s.s_id in (select s_id from SC sc where sc.c_id = 0 and sc.score = 100 )
```

[![img\_0913\_01\_1.png][img_0913_01_1.png]][img_0913_01_1.png_img_0913_01_1.png]

����û���õ�������typeȫ��ALL����ô�����뵽�ľ��ǽ���һ�������������������ֶε�Ȼ����where�������ֶΡ�

�ȸ�sc���c\_id��score��������

```
CREATE index sc_c_id_index on SC(c_id);
```

```
CREATE index sc_score_index on SC(score);
```

�ٴ�ִ��������ѯ��䣬ʱ��Ϊ: 1.054s

����3w�౶����������˲�ѯʱ�䣬���������ܼ���̶ȵ���߲�ѯЧ�ʣ����������б�Ҫ���ܶ�ʱ�����ǽ�

�����ˣ�������С�ĵ�ʱ��ѹ��û�о������Ż��ĸо�ͦˬ��

����1s��ʱ�仹��̫���ˣ����ܽ����Ż�����ϸ��ִ�мƻ���

[![img\_0913\_01\_2.png][img_0913_01_2.png]][img_0913_01_2.png_img_0913_01_2.png]

�鿴�Ż����sql:

```
SELECT
    `YSB`.`s`.`s_id` AS `s_id`,
    `YSB`.`s`.`name` AS `name`
FROM
    `YSB`.`Student` `s`
WHERE
    < in_optimizer > (
        `YSB`.`s`.`s_id` ,< EXISTS > (
            SELECT
                1
            FROM
                `YSB`.`SC` `sc`
            WHERE
                (
                    (`YSB`.`sc`.`c_id` = 0)
                    AND (`YSB`.`sc`.`score` = 100)
                    AND (
                        < CACHE > (`YSB`.`s`.`s_id`) = `YSB`.`sc`.`s_id`
                    )
                )
        )
    )
```

���䣺��������������ô�鿴�Ż�������

�������£�

�������ִ�� [![img\_0913\_01\_3.png][img_0913_01_3.png]][img_0913_01_3.png_img_0913_01_3.png]

[![img\_0913\_01\_4.png][img_0913_01_4.png]][img_0913_01_4.png_img_0913_01_4.png]

��type=all

������֮ǰ���뷨����sql��ִ�е�˳��Ӧ������ִ���Ӳ�ѯ

```
select s_id from SC sc where sc.c_id = 0 and sc.score = 100
```

��ʱ��0.001s

�õ����½����

[![img\_0913\_01\_5.png][img_0913_01_5.png]][img_0913_01_5.png_img_0913_01_5.png]

Ȼ����ִ��

```
select s.* from Student s where s.s_id in(7,29,5000)
```

��ʱ��0.001s

���������൱���˰���Mysql��Ȼ������ִ�����Ĳ�ѯ�����ǽ�sql�Ż�����exists�Ӿ䣬��������EPENDENT SUBQUERY��

mysql����ִ������ѯ����ִ�����Ĳ�ѯ��������Ҫѭ��70007\*8�Ρ�

��ô�������Ӳ�ѯ�أ�

```
SELECT s.* from 

Student s

INNER JOIN SC sc

on sc.s_id = s.s_id

where sc.c_id=0 and sc.score=100
```

����Ϊ�����·������Ӳ�ѯ�����������ʱɾ������sc\_c\_id\_index��sc\_score\_index

ִ��ʱ���ǣ�0.057s

Ч��������ߣ�����ִ�мƻ���

[![img\_0913\_01\_6.png][img_0913_01_6.png]][img_0913_01_6.png_img_0913_01_6.png]

�����������������֣��Ҳ����ǲ���Ҫ��sc���s\_id����������

CREATE index sc\_s\_id\_index on SC(s\_id);

show index from SC

[![img\_0913\_01\_7.png][img_0913_01_7.png]][img_0913_01_7.png_img_0913_01_7.png]

��ִ�����Ӳ�ѯ

ʱ��: 1.076s����Ȼʱ�仹�䳤�ˣ�ʲôԭ�򣿲鿴ִ�мƻ���

[![img\_0913\_01\_8.png][img_0913_01_8.png]][img_0913_01_8.png_img_0913_01_8.png]

�Ż���Ĳ�ѯ���Ϊ��

```
SELECT
    `YSB`.`s`.`s_id` AS `s_id`,
    `YSB`.`s`.`name` AS `name`
FROM
    `YSB`.`Student` `s`
JOIN `YSB`.`SC` `sc`
WHERE
    (
        (
            `YSB`.`sc`.`s_id` = `YSB`.`s`.`s_id`
        )
        AND (`YSB`.`sc`.`score` = 100)
        AND (`YSB`.`sc`.`c_id` = 0)
    )
```

ò�������������Ӳ�ѯ���ٽ��е�where��������

�ص�ǰ���ִ�мƻ���

[![img\_0913\_01\_9.png][img_0913_01_9.png]][img_0913_01_9.png_img_0913_01_9.png]

������������where�������ˣ���������ִ�мƻ������ǹ̶��ģ���ô�����ȿ��±�׼��sqlִ��˳��

[![img\_0913\_01\_10.png][img_0913_01_10.png]][img_0913_01_10.png_img_0913_01_10.png]

�������������join�ٽ���where���ˣ������������������������join��������70w�����ݷ���join���٣������ִ��where

���������Ƿ���������Ϊ���ų�mysql�Ĳ�ѯ�Ż������Լ�дһ���Ż����sql

```
SELECT
    s.*
FROM
    (
        SELECT
            *
        FROM
            SC sc
        WHERE
            sc.c_id = 0
        AND sc.score = 100
    ) t
INNER JOIN Student s ON t.s_id = s.s_id
```

����ִ��sc��Ĺ��ˣ��ٽ��б����ӣ�ִ��ʱ��Ϊ��0.054s

��֮ǰû�н�s\_id������ʱ����

�鿴ִ�мƻ���

[![img\_0913\_01\_11.png][img_0913_01_11.png]][img_0913_01_11.png_img_0913_01_11.png]

����ȡsc����������Ч�ʾ͸߶��ˣ����ڵ���������ȡsc��ʱ�������ɨ�����ô���ڿ�����ȷ��Ҫ�����������

```
CREATE index sc_c_id_index on SC(c_id);
```

```
CREATE index sc_score_index on SC(score);
```

��ִ�в�ѯ��

```
SELECT
    s.*
FROM
    (
        SELECT
            *
        FROM
            SC sc
        WHERE
            sc.c_id = 0
        AND sc.score = 100
    ) t
INNER JOIN Student s ON t.s_id = s.s_id
```

ִ��ʱ��Ϊ��0.001s�����ʱ���൱���ף�����50��

ִ�мƻ���

[![img\_0913\_01\_12.png][img_0913_01_12.png]][img_0913_01_12.png_img_0913_01_12.png]

���ǻῴ��������ȡsc�����������õ���������

��ô����ִ����sql

```
SELECT s.* from 

Student s

INNER JOIN SC sc

on sc.s_id = s.s_id

where sc.c_id=0 and sc.score=100
```

ִ��ʱ��0.001s

ִ�мƻ���

[![img\_0913\_01\_13.png][img_0913_01_13.png]][img_0913_01_13.png_img_0913_01_13.png]

������mysql�����˲�ѯ����Ż�����ִ����where���ˣ���ִ�����Ӳ������Ҷ��õ���������

2015-04-30�ղ��䣺��������µ���һЩ�������ݣ������Է��֣�ǰ�����Ż����sqlִ��Ч���ֱ����

��������ΪSC�������������300W,ѧ��������Ϊ��ɢ��

�Ȼع��£�

show index from SC

[![img\_0913\_01\_14.png][img_0913_01_14.png]][img_0913_01_14.png_img_0913_01_14.png]

ִ��sql

```
SELECT s.* from 

Student s

INNER JOIN SC sc

on sc.s_id = s.s_id

where sc.c_id=81 and sc.score=84
```

```
 
```

ִ��ʱ�䣺0.061s�����ʱ����΢���˵�

ִ�мƻ���

[![img\_0913\_01\_15.png][img_0913_01_15.png]][img_0913_01_15.png_img_0913_01_15.png]

�����õ���intersect��������������������ͬʱ�����Ľ�����󲢼����ٿ��ֶ�score��c\_id�����ֶȣ�

����һ���ֶο������ֶȶ����Ǻܴ󣬴�SC�������c\_id=81�����Ľ����70001,score=84�Ľ����39425

��c\_id=81 and score=84 �Ľ����897�����������ֶ��������������ֶ��ǱȽϸߵģ���˽�������������ѯЧ��

������ߣ�������һ���Ƕȿ����ñ��������300w���Ժ����࣬�������洢���ԣ����ǲ�С����Ŀ��������������

���ӣ������Ͳ���ȫ�����ص��ڴ棬����Ҫ�Ӵ���ȥ��ȡ�����������ĸ���Խ�࣬�����̵Ŀ�����Խ����˸��ݾ���

ҵ������������е����������Ǳ�Ҫ�ģ���ô���������԰ɡ�

```
alter table SC drop index sc_c_id_index;
alter table SC drop index sc_score_index;
create index sc_c_id_score_index on SC(c_id,score);
```

ִ��������ѯ��䣬����ʱ��Ϊ��0.007s������ٶȻ��ǿ��Խ��յ�

ִ�мƻ���

[![img\_0913\_01\_16.png][img_0913_01_16.png]][img_0913_01_16.png_img_0913_01_16.png]

�������Ż���ʱ��һ����

�ܽ᣺

1��mysqlǶ���Ӳ�ѯЧ��ȷʵ�Ƚϵ�

2�����Խ����Ż������Ӳ�ѯ

3�����ӱ�ʱ����������where�����Ա���й��ˣ�Ȼ����������

����Ȼmysql�������������Ż���

4���������ʵ���������Ҫʱ����������������

5��ѧ�����sqlִ�мƻ���mysql���sql�����Ż������Է���ִ�мƻ�����Ҫ

## �����Ż�

���潲���Ӳ�ѯ���Ż����Լ���ν��������������ڶ���ֶ�����ʱ���ֱ���ֶν����˵�������

���淢����ʵ������������Ч�ʻ���ߣ����������������ϴ󣬵��������ֶȲ��ߵ�����¡�

## ��������

��ѯ������£�

```
select * from user_test_copy where sex = 2 and type = 2 and age = 10
```

������

```
CREATE index user_test_index_sex on user_test_copy(sex);
CREATE index user_test_index_type on user_test_copy(type);
CREATE index user_test_index_age on user_test_copy(age);
```

�ֱ��sex,type,age�ֶ�����������������Ϊ300w,��ѯʱ�䣺0.415s

ִ�мƻ���

[![img\_0913\_01\_17.png][img_0913_01_17.png]][img_0913_01_17.png_img_0913_01_17.png]

����type=index\_merge

����mysql�Զ�������������Ż����Խ��������intersect��������

## ��������

���ǿ�������3�����Ͻ�����������������copyһ���Ա�������

```
create index user_test_index_sex_type_age on user_test(sex,type,age);
```

��ѯ��䣺

```
select * from user_test where sex = 2 and type = 2 and age = 10
```

ִ��ʱ�䣺0.032s������10�౶���Ҷ������������ֶ�Խ�ߣ���ߵ��ٶ�ҲԽ��

ִ�мƻ���

[![img\_0913\_01\_18.png][img_0913_01_18.png]][img_0913_01_18.png_img_0913_01_18.png]

**����ǰ׺**

����������������ǰ׺�����ԣ�

ִ��һ����䣺

```
select * from user_test where sex = 2
select * from user_test where sex = 2 and type = 2
select * from user_test where sex = 2 and age = 10
```

����ʹ�õ��������������ĵ�һ���ֶ�sexҪ������where������

## ��������

���ǲ�ѯ���ж������������������ڻ�ȡ�������ʱ������ȥ���̻�ȡ�����е����ݣ�ֱ�ӷ����������ݼ���

�磺

```
select sex,type,age from user_test where sex = 2 and type = 2 and age = 10
```

ִ��ʱ�䣺0.003s

Ҫ��ȡ�����ֶο�Ķ�

## ����

```
select * from user_test where sex = 2 and type = 2 ORDER BY user_name
```

ʱ�䣺0.139s

�������ֶ��Ͻ�����������������Ч��

```
create index user_name_index on user_test(user_name)
```

�����һЩsql���ŵ��ܽᣬ�Ժ���ʱ���������о�

1�� �����;����������ֵ���ͣ��ҳ��Ⱦ����̣ܶ�������������������ֶεȵ�

2�� ������������

3�� ������Ҫ����������������

�������й���֮���кܶ����ݣ���ô������Ч�ʽ���Ƚϵͣ����е����ֶȽϵͣ�

��ô����ڶ�����Ͻ�����������ô����е����ֶȾʹ���ˣ�������������Ч����ߡ�

4�� ����ҵ�񳡾�������������

ֻ��ѯҵ����Ҫ���ֶΣ������Щ�ֶα��������ǣ����������߲�ѯЧ��

5�� ������ӵ��ֶ�����Ҫ��������

�������Լ������߱����ӵ�Ч��

6�� where�����ֶ�����Ҫ��������

7�� �����ֶ�����Ҫ��������

8�� �����ֶ�����Ҫ��������

9�� Where�����ϲ�Ҫʹ�����㺯������������ʧЧ

**�ο�����**

[http://www.cnblogs.com/linfangshuhellowored/p/4430293.html][http_www.cnblogs.com_linfangshuhellowored_p_4430293.html]

��sql��ѯ

[http://tech.meituan.com/mysql-index.html][http_tech.meituan.com_mysql-index.html]

�ѿ����˻�

[http://www.cnblogs.com/Toolo/p/3634563.html][http_www.cnblogs.com_Toolo_p_3634563.html]

sql�Ż�

[http://www.cnblogs.com/mliang/p/3637937.html][http_www.cnblogs.com_mliang_p_3637937.html]

[http://www.cnblogs.com/xwdreamer/archive/2012/07/19/2599494.html][http_www.cnblogs.com_xwdreamer_archive_2012_07_19_2599494.html]

ִ�мƻ��ο���

[http://www.cnblogs.com/ggjucheng/archive/2012/11/11/2765237.html][http_www.cnblogs.com_ggjucheng_archive_2012_11_11_2765237.html]


[img_0913_01_1.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_1.png
[img_0913_01_1.png_img_0913_01_1.png]: https://images0.cnblogs.com/blog/722277/201504/281308508332906.png
[img_0913_01_2.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_2.png
[img_0913_01_2.png_img_0913_01_2.png]: https://images0.cnblogs.com/blog/722277/201504/281308568337873.png
[img_0913_01_3.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_3.png
[img_0913_01_3.png_img_0913_01_3.png]: https://images0.cnblogs.com/blog/722277/201504/281342048962810.png
[img_0913_01_4.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_4.png
[img_0913_01_4.png_img_0913_01_4.png]: https://images0.cnblogs.com/blog/722277/201504/281342059581151.png
[img_0913_01_5.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_5.png
[img_0913_01_5.png_img_0913_01_5.png]: https://images0.cnblogs.com/blog/722277/201504/281308597865371.png
[img_0913_01_6.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_6.png
[img_0913_01_6.png_img_0913_01_6.png]: https://images0.cnblogs.com/blog/722277/201504/281309019116755.png
[img_0913_01_7.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_7.png
[img_0913_01_7.png_img_0913_01_7.png]: https://images0.cnblogs.com/blog/722277/201504/281309039744626.png
[img_0913_01_8.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_8.png
[img_0913_01_8.png_img_0913_01_8.png]: https://images0.cnblogs.com/blog/722277/201504/281309076938021.png
[img_0913_01_9.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_9.png
[img_0913_01_9.png_img_0913_01_9.png]: https://images0.cnblogs.com/blog/722277/201504/281309121615490.png
[img_0913_01_10.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_10.png
[img_0913_01_10.png_img_0913_01_10.png]: https://images0.cnblogs.com/blog/722277/201504/281309162242017.png
[img_0913_01_11.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_11.png
[img_0913_01_11.png_img_0913_01_11.png]: https://images0.cnblogs.com/blog/722277/201504/281309201619112.png
[img_0913_01_12.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_12.png
[img_0913_01_12.png_img_0913_01_12.png]: https://images0.cnblogs.com/blog/722277/201504/281309232553568.png
[img_0913_01_13.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_13.png
[img_0913_01_13.png_img_0913_01_13.png]: https://images0.cnblogs.com/blog/722277/201504/281309263808780.png
[img_0913_01_14.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_14.png
[img_0913_01_14.png_img_0913_01_14.png]: https://images0.cnblogs.com/blog/722277/201504/301012440526578.png
[img_0913_01_15.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_15.png
[img_0913_01_15.png_img_0913_01_15.png]: https://images0.cnblogs.com/blog/722277/201504/301012457409561.png
[img_0913_01_16.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_16.png
[img_0913_01_16.png_img_0913_01_16.png]: https://images0.cnblogs.com/blog/722277/201504/301012473803261.png
[img_0913_01_17.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_17.png
[img_0913_01_17.png_img_0913_01_17.png]: https://images0.cnblogs.com/blog/722277/201504/291444259742769.png
[img_0913_01_18.png]: https://gitee.com/souyunkutech/souyunku-home/raw/master/images/souyunku-web/2019/09/0913/01/img_0913_01_18.png
[img_0913_01_18.png_img_0913_01_18.png]: https://images0.cnblogs.com/blog/722277/201504/291444304909521.png
[http_www.cnblogs.com_linfangshuhellowored_p_4430293.html]: http://www.cnblogs.com/linfangshuhellowored/p/4430293.html
[http_tech.meituan.com_mysql-index.html]: http://tech.meituan.com/mysql-index.html
[http_www.cnblogs.com_Toolo_p_3634563.html]: http://www.cnblogs.com/Toolo/p/3634563.html
[http_www.cnblogs.com_mliang_p_3637937.html]: http://www.cnblogs.com/mliang/p/3637937.html
[http_www.cnblogs.com_xwdreamer_archive_2012_07_19_2599494.html]: http://www.cnblogs.com/xwdreamer/archive/2012/07/19/2599494.html
[http_www.cnblogs.com_ggjucheng_archive_2012_11_11_2765237.html]: http://www.cnblogs.com/ggjucheng/archive/2012/11/11/2765237.html



