# mysql 数据库查询优化

## 排序

max_length_for_sort_data

\--------------------------

确定使用的filesort算法的索引值大小的限值。参见7.2.12节，“MySQL如何优化ORDER BY”。

mysql的filesort算法有两种，

  . 一种是最初的算法，在MySQL 4.1以前只有这种算法，

  . 一种是改进的filesort算法，它出现在MySQL 4.1以后(blob和text类型的字段不能采用这种改进算法)

"最初的算法"流程如下：

1.读取所有的满足条件的数据，只包含sort key和row pointer两种数据

2.在buffer中执行qsort排序

3.排完序后，再根据row pointer去读取相应的行数据

从中可以看出，每次排序都需要读两次表，而根据row pointer去读表往往都是随机离散读的，所有其开销非常大。

改进后的filesort算法是：

1.读取所需要的数据，包含sort key,row pointer和查询所需要访问的字段

2.根据sort key排序

3.按排序后的顺序读取数据，由于sort_buffer_size中包含了所需要的字段，因此不需要再回表了，可以直接返回结果给客户端。

很明显，这种改进的方法对sort_buffer_size的需求也大大增加.

所以为了防止性能下降，mysql增加了一个参数max_length_for_sort_data,当第一步中除了sort key以外的字段内容大于max_length_for_sort_data这个参数时，mysql将采用第一种排序算法。