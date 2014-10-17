# Hash 算法与数据库实现

标签（空格分隔）： 未分类
Hash 算法与数据库实现
---

Hash表 （HashTable）又称三列表，通过把关键字Key映射到数组中的一个位置来访问记录，以加快查找速度。
这个映射函数称为Hash函数，存放记录的数组称为Hash表。

### Hash 函数
    Hash函数的作用是把任意长度的输入，通过hash算法变换成固定长度的输出，概述出就是Hash值。
    这种转换是一种压缩映射，就是hash值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出
    ，而不可能从Hash值来唯一地确定输入的值。

*    Hash算法满足条件：
每个关键字都可以均匀地分布到Hash表任意一个位置，并与其他已被散列到Hash表的关键字不发生冲突。

### Hash算法
    关键字k可能是整数或者字符串，可以按照关键字的类型设计不同的Hash算法。
    
整数关键字的Hash算法有
>*   直接取余法
>*   乘积取证法
>*   Times33

##直接取余法
    直接用关键字k除以Hash表的大小m取余数
```
    h（k）= k mod m
```
如果hash表的大小为 m = 12 ，所给关键字为k=100，则h（k）=4.
直接求余，速度快。

##乘积取整法

乘积取整法首先使用关键 K 乘以一个常数A（0 < A < 1）,
并取出kA的小数部分。然后用Hash表达小m乘以这个值，再取出整数部分即可。
```
    h（k）=floor（m*（kA mod 1））
```
其中，kA mod 1 表示 KA的小数部分，floor是取整操作。
当关键字是字符串的时候，就不能使用上面的Hash算法。因为字符串是有字符组成，所以可以把字符串所有字符的ASCII码加起来得到一个整数，然后再按照上面的Hash算法去计算即可：
```
# 简单算法描述 不可用于实际开发
    function hash_fun($key,$m){
        $strlen = strlen($key);
        $hashval = 0;
        for($i =0 ;$i<$strlen;$i++){
            $hasval +=ord($key($i));
        }
        return $haval % $m;
    }
```
##经典Hash算法Times33
算法思路就是不断乘以33，效果和随机性都非常好
```
    unsigned int DJBHash(char *str){
        unsigned int hash= 5381;
        while(*str){
        hash += (hash << 5)+(*str++)
        }
        return (hash & 0x7FFFFFFFF);
    }
```
## Hash表 
Hash是非常重要的数据结构。时间复杂度是O（1）
实现Hash的步骤
创建一个固定大小的数组用于存放数据。
设计Hash函数。
通过Hash函数把关键字映射到某个位置，并在此位置上进行数据存取。

##使用PHP实现Hash表
首先创建一个HashTable类，有两个属性 $buckets 和 $size .$buckets 用于存储数据的数组，$size用于记录$buckets数组大小。
然后在构造函数中为$buckets数组分配内存。
```
<?php
    class HashTables{
        private $buckets;
        private $size= 10;
        
        public function __construct(){
            $this->bukets=new SplFixedArray($this->size);
        }
    }

```
构造函数中，为$buckets数组分配了一个大小为10的数组。
SplFixedArray（）是php的SPL扩展，效率更快。（5.3以前版本要开启SPL模块，5.3默认开启）
接着为Hash表制定一个Hash函数，现在使用的字符串相加取余的方法：
```
    private function hashfunc($key){
        $strlen = strlen($key);
        $hashval=0;
        for($i=0;$i<$strlen;$i++){
            $hashval+=ord($key[$i]);
        }
        return $hashval % $this->size
    }
```
有了Hash函数，就可以实现插入和查找方法。插入数据时，先通过Hash函数计算关键字所在Hash表的位置，然后把数据保存到此为位置即可。
```
    public function insert($key,$val){
        $index=$this->hashfunc($key);
        $this->buckets[$index]=$val;
    }
```
查找数据方法与插入方法相似，先通过Hash函数计算关键字所在Hash表的位置，然后返回此位置的数据即可。
```
    public function find($key){
        $index=$this->hashfunc($key);
        return $this->buckets[$index];
    }
```
完整的代码及测试
```
<?php
    class HashTable{
        private $buckets;
        private $size=10;
        
        public function __construct(){
            $this->buckets = new SplFixedArray($this->size);
        }
        
        private function hashfunc($key){
            $strlen = strlen($key);
            $hashval=0;
            
            for($i=0;$i<$strlen;$i++){
                $hashval+=ord($key[$i]);
            }
            return $hashval % $this->size;
        }
    
    
        public function insert($key,$val){
            $index = $this->Hashfunc($key);
            $this->buckets[$index]=$val;
        }
        
        public function find($key){
            $index=$this->hashfunc($key);
            return $this->buckets[$index];
        }

}
//测试文档
$ht=new HashTable();
$ht->insert('key1','value1');
$ht->insert('key2','value2');
echo $ht->find('key1');
echo '<br/>';
echo $ht->find('key2');
```
















