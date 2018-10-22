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
即是素数（质数），也是奇数。除了33，还有131, 1313, 5381等。PHP内置的Hash函数用的是5381

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
### Hash冲突
测试上面的代码
```
<?php
 
$ht=new HashTable(); 
$ht->insert('key1','value1');
$ht->insert('key12','value12');
echo $ht->find('key1');
echo '<br/>';
echo $ht->find('key12');
//value12 
//value12
```
原因是：不同的关键字通过Hash函数计算出来的Hash值不同。
打印key1和key12都是8
```
function hashfunc($key){
            $strlen = strlen($key);
            $hashval=0;
            
            for($i=0;$i<$strlen;$i++){
                $hashval+=ord($key[$i]);
            }
            return $hashval % 10;
        }

        echo hashfunc('key1');//8
        echo hashfunc('key12');//8
```
也就是说，value1被value12被覆盖了。
解决冲突的常用方法有：开放定址法和拉链法

###拉链法解决冲突
将所有相同的Hash值得关键字节点链接在同一个链表中

拉链法巴相同的Hash值的关键字节点以一个链表链接起来，那么在查找元素是就必须遍历这条链表，
比较链表中每个元素的关键字与查找的关键字是否相等，如果相等就是我们要找的关键字。
因为节点需要保存关键字（key）和数据（value），同时还要记录相同的Hash值的节点，所以创建一个HashNode类来存储这些信息
HashNode结构
```
<?php
class HashNode{
	    public $key;
	    public $value;
	    public $nextNode;
	    public function __construct($key,$value,$nextNode==null){
	    	$this->key=$key;
	    	$this->value=$value;
	    	$this->nextNode=$nextNode;
    	} 
		}	

```
HashNode 有3个属性 $key，$value，$nextNode ，$key是节点的关键字，$value是节点的值，而$nextNode 是指向具有相同Hash值得节点的指针。
```
//修改插入模式
public function insert($key,$value){
			$index=$this->hashfunc($key);
			/*创建一个新的节点*/
			if(isset($this->buckets[$index])){
				$newNode = new HashNode( $key,$value,$this->buckets[$index]);
			}else{
				$newNode=new HashNode( $key,$value,null);
			}

			$this->buckets[$index]=$newNode;
			/*保存新节点*/
		}

```
1.使用Hash函数计算关键字的Hash值，通过Hash值定位到Hash表的指定位置。
2.如果此位置已经被占用，把新节点的$nextNode指向此节点，否则把新的节点设置为null
3.把新节点保存到Hash表的当前位置

查找

```
public function find($key){
			$index = $this->hashfunc($key);
			$current= $this->buckets[$index];

			while (isset($current)) { /*遍历当前链表*/
				if($current->key==$key){/*比较当前姐idande关键字*/
					return $current->value;/*成功*/
				}
				$current=$current->nextNode;/*比较下一个接点*/
			}
			return  null; /*查找失败*/
		}
```
1使用Hash函数计算关键字的Hash值，通过Hash值定位到Hash表的制定位置
2遍历当前链表，比较链表中每个节点的关键字与查找关键字是否相等。如果相等，查找成功。
3如果整个链表都没找到，查找失败
```
<?php
 class HashNode{
	    public $key;
	    public $value;
	    public $nextNode;
	    public function __construct($key,$value,$nextNode=null){
	    	$this->key=$key;
	    	$this->value=$value;
	    	$this->nextNode=$nextNode;
    		} 
		}	

class NewHashTable{

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

        public function insert($key,$value){
			$index=$this->hashfunc($key);
			/*创建一个新的节点*/
			if(isset($this->buckets[$index])){
				$newNode = new HashNode( $key,$value,$this->buckets[$index]);
			}else{
				$newNode=new HashNode( $key,$value,null);
			}

			$this->buckets[$index]=$newNode;
			/*保存新节点*/
		}

		public function find($key){
			$index = $this->hashfunc($key);
			$current= $this->buckets[$index];

			//var_dump($current );
			//object(HashNode)
			 
			while (isset($current)) { /*遍历当前链表*/
				if($current->key==$key){/*比较当前姐idande关键字*/
					return $current->value;/*成功*/
				}
				$current=$current->nextNode;/*比较下一个接点*/
			}
			return  null; /*查找失败*/
		}

}

$nht=new NewHashTable();
$nht->insert('key1','value1');
$nht->insert('key12','value12');
echo $nht->find('key1');
echo '<br/>';
echo $nht->find('key12'); 
```















