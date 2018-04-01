## 常用类
* Object  
Object类是所有类、数组、枚举类的父类。当定义一个没有使用extends关键字为它显式指定父类，则该类默认继承Object类。

* Objects  
Objects工具类是Java7新增的一个类，它提供了一些工具方法来操作对象。例如：  
Objects.toString(obj):输出对象的toString()方法的结果，若对象为null，则输出null。  
Objects.hashCode(obj):输出对象的hashCode，若对象为null，则输出0。 
Objects.requireNonNull(obj):用于检验。要求obj不能为null，如果为null，则抛出NullPointerException。

* String/StringBuffer/StringBuilder  
String类是不可变类，一旦一个String对象被创建以后，包含在这个对象中的字符序列是不可改变的，直到这个对象被销毁。  
StringBuffer对象则代表一个字符串序列可变的字符串。可以通过StringBuffer提供的append(),insert(),reverse()等等方法改变这个字符串对象的字符序列。  
StringBuilder和StringBuffer基本上相同，构造器和方法也基本类似。不同的是，StringBuffer是线程安全的，而StringBuilder则没有实现线程安全的功能，所以StringBuilder性能略高。  
StringBuffer和StringBuilder包含两个长度属性length和capacity。length表示其包含的字符序列的长度；capacity表示StringBuffer/StringBuilder的容量。  
String类方法举例：  
1.String concat(String str):将该String对象与str连接在一起并且返回连接后的新对象。  
2.boolean contentEquals(StringBuffer sb):将该String对象与StringBuffer对象sb进行比较，当它们所包含的字符序列相同时返回true。  
3.static String copyValueOf(char[] data):将字符数组连缀成字符串。
4.static String copyValueOf(char[] data, int offset, int length):将char数组的子数组中的元素连缀成字符串。offset起点，length长度。  
5.void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin):该方法将字符串冲srcBegin开始，到srcEnd结束的字符复制到dst字符数组中，其中dstBegin为目标字符数组的起始复制位置，会覆盖掉重合位置上的值。

* Random/ThreadLocalRandom  
Random类专门用于生成一个伪随机数，它有两个构造器：一个构造器默认使用当前时间作为种子；另一个构造器需要传入一个long型整数作为种子。Random.nextInt(26);获取0-26之间的随机整数。  
ThreadLocalRandom类是Java新增的一个类，它是Random类的增强版。在并发访问的环境下，使用ThreadLocalRandom来代替Random可以减少多线程资源竞争，保证系统具有较好的性能。

* BigDecimal  
Java的float，double两种基本浮点类型都会引起精度丢失。为了能精确表示、计算浮点数，Java提供了BigDecimal类。在创建BigDecimal的时候，不推荐使用new BigDecimal(0.1)来创建新的对象，因为系统无法精确表示0.1这个浮点数，会导致精度问题。可以通过new BigDecimal("0.1")和BigDecimal.valueOf(0.1)代替。

* Date(java.util.Date)  
Date():创建一个当前日期的Date对象。  
Date(long date):根据long型整数来生成一个Date对象。  
boolean after(Date when):测试该日期是否在指定日期when之后。  
boolean before(Date when):测试该日期是否在指定日期when之前。  
int compareTo(Date when):比较两个日期的大小，后面的时间大于前面的时间时返回-1，否则返回1.  
boolean equals(Object obj):当两个时间为同一时刻的时候返回true。  
long getTime():返回该时间对应的long型整数。从1970-01-01 00:00:00开始计算，以毫秒为单位。  
void setTime(long time):设置该Date对象的时间。

* Calendar  
因为Date类在设计上存在一些缺陷，所以Java提供了Calendar类来更好地处理日期和时间。

## Java集合
* Java的集合类主要由Collection和Map两个接口派生而出。

### Set集合
* Set集合不允许包含相同的元素，通过equals判断两个对象是否相等。

#### Iterator
1. boolean hasNext():如果被迭代的集合还有下一个元素就返回true。
2. Object next():返回集合中的下一个元素。
3. void remove():删除当前返回的元素。
* 当使用Iterator对集合元素进行遍历时，Iterator将集合元素的引用传递给了迭代变量。
* 当使用Iterator迭代访问Collection集合元素时，Collection集合里的元素不能被删除，只有通过Iterator的remove()方法才可以进行删除，否则会引发java.util.ConcurrentModificationException异常。

#### foreach
* foreach循环中迭代变量也不是集合中元素的本身，系统只是依次把集合元素的值赋给了迭代变量，因此在foreach循环中修改迭代变量的值也没有任何实际意义(集合元素不是基本类型时另外考虑)。
* 当使用foreach循环迭代访问集合元素时，该元素也不能被删除，否则将会引起ConcurrentModificationException异常。

#### HashSet
* HashSet底层是散列表结构。HashSet按Hash算法来存储集合中的元素，因此具有很好的存取和查找性能。HashSet具有以下特点：  
1.元素不能相同，不能保证元素的排列顺序，元素是无序的。  
2.HashSet不是同步的，也就不是线程安全的。  
3.集合元素值可以是null，但是只能有一个。

* 当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该HashCode值决定该对象在HashSet中的存储位置。如果有两个元素通过equals方法比较返回true，但它们的hashCode()方法返回值不相等，HashSet将会把它们存储在不同的位置，依然可以添加成功。

* HashSet也有“桶”(bucket)的概念，当两个对象hashCode相等，但是包含的值不同时，HashSet就会以链式结构来保存多个对象，这样会导致性能下降。

#### LinkedHashSet
* 不允许重复(equals判断是否相同)。以插入集合的顺序来保存集合元素，使用链表维护元素的次序。因为需要维护元素的次序，所以性能上略低于HashSet，但是在迭代访问Set里面的元素时将会有很好的性能，因为它以链表来维护内部顺序。

#### TreeSet
* TreeSet可以确保集合元素处于排序状态。默认按照从小到大的顺序进行排序。实用红黑树算法来维护集合元素的次序。

### List集合
* List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List集合允许有重复的元素。List集合默认按照元素的添加顺序设置元素的索引。

* List还额外提供了一个listIterator()方法，该方法返回一个ListIterator对象。ListIterator接口在Iterator基础上额外提供了如下方法：  
1.boolean hasPrevious():是否还有上一个元素。  
2.Object previous():返回上一个元素。  
3.void add():在指定位置添加一个元素。

#### ArrayList/Vector
* ArrayList和Vector类都是基于数组实现的List类，基本上两者是想同的。在没有指定长度的情况下，两者默认的长度是10。显著的区别就是Vector是线程安全的，所以Vector的性能要比ArrayList低。

### Queue集合
* Queue用于模拟先进先出的队列。通常，队列不允许随机访问队列中的元素。

#### PriorityQueue实现类
* PriorityQueue保存队列元素的顺序并不是按加入队列的顺序，而是按队列元素的大小进行重新排序。

#### Deque接口与ArrayDeque实现类
* Deque接口是Queue接口的子接口，它代表一个双端队列，允许从两端来操作队列的元素。Deque接口提供了一个典型的实现类ArrayDeque，它是一个基于数组实现的双端队列。Deque的默认长度为16.

#### LinkedList实现类
* LinkedList类是List接口的实现类，可以根据索引来随机访问集合中的元素。除此之外，LinkedList还实现了Deque接口，因此它可以被当成双端队列来使用。

### Map集合
#### HashMap/Hashtable
* HashMap和HashTable都是Map接口典型的实现类，都是通过键值对来储存数据。但是HashMap和HashTable也存在着两个典型的区别。  
1.Hashtable是一个线程安全的Map实现，但HashMap是线程不安全的，所以HashMap比HashTable的性能高一点。  
2.Hashtable不允许使用null作为key或者value。如果将null放进Hashtable中，将会引发NullPointerException异常；但HashMap可以使用null作为key或value。

#### LinkedHashMap实现类
* LinkedHashMap使用双向链表来维护key-value对的次序，该链表负责维护Map的迭代顺序，迭代顺序与key-value对的插入顺序保持一致。LinkedHashMap需要维护元素的插入顺序，因此性能上略低于HashMap，但是在迭代访问Map里的全部元素时将有较好的性能。

#### SortedMap接口和TreeMap实现类
* TreeMap就是一个红黑树数据结构，每个key-value对即作为红黑树的一个节点。TreeMap储存key-value对时，需要根据key对节点进行排序。TreeMap可以保证所有的key-value对处于有序状态。

## Java输入/输出
### File类
* File类能新建、删除、重命名文件和目录，File不能访问文件内容本身。如果需要访问文件内容本身，则需要使用输入/输出流。

### IO流的分类
1. 输入流(InputStream/Reader)和输出流(OutputStream/Writer)
2. 字节流(操作的数据单元是8位的字节，InputStream/OutputStream)和字符流(操作的数据单元是16位的字符，Reader/Writer)
3. 节点流和处理流

### 