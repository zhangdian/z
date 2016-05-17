# 2013-06-25-java-deep-copy

##JAVA中的浅拷贝和深拷贝(shallow copy and deep copy)

###一个示例 

首先要区分"copy a value"和"copy an object"之间的区别：

	// copy a value
	int val   = 1;
	int cpVal = val;

	// copy a value(the object reference for the array above)
	int[] val 	= new int[]{42};
	int[] cpVal = val;

	// copy a object
	StringBuffer object   = new StringBuffer("I am a object");
	StringBuffer cpObject = new StringBuffer(object);

简单的说，赋值操作都是"copy a value"。如果想要"copy an object"，需要显式的或者在内部进行类似"new"的操作。

<!-- more -->

###对象(object)的浅拷贝和深拷贝

浅拷贝意味着仅仅拷贝对象的一层，而深拷贝意味着拷贝对象多于一层的数据。那么问题就是，怎么去定义一层？

考虑下面的例子：

	public class Example {
    	public int foo;
    	public int[] bar;
    	public Example() { };
    	public Example(int foo, int[] bar) { this.foo = foo; this.bar = bar; };
	}
	
	Example eg1 = new Example(1, new int[]{1, 2});

下面两个例子分别执行了对eg1的__浅拷贝和深拷贝__：

	Example eg2 = new Example(eg1.foo, eg1.bar);
	Example eg2 = new Example(eg1.foo, Arrays.copy(eg1.bar));

###clone方法

每一个类和array都包含这个方法，它的功能是创建目的对象的一个拷贝，然而要注意以下几点：

* deep无法确定到底是多少层？两层？三层？还是所有链接的对象
* 实际上，文档并没有说明clone是否创建一个新的对象

下面是javadoc对于clone的解释：

	"Creates and returns a copy of this object. 
	The precise meaning of "copy" 
	may depend on the class of the object. 
	
	The general intent is that, for any object x, 
	the expression x.clone() != x will be true, 
	and that 
	the expression x.clone().getClass() == x.getClass() 
	will be true, 
	but these are not absolute requirements. 
	
	While it is typically the case that 
	x.clone().equals(x) will be true, 
	this is not an absolute requirement."

###Java List深拷贝实现

使用List的addAll方法就可以deep copy源列表中的所有元素(前提是list内部元素执行浅拷贝即可)：

	public static void main(String[] args) {
        
        List<String> list = new ArrayList<String>();
        list.add("1");
        list.add("2");
        
		// 创建dest list，将源list的所有值都拷贝过去
        List<String> dest = new ArrayList<String>();
        dest.addAll(list);
        
		// 在dest list中添加一个元素，修改一个元素
        dest.add("3");
        dest.set(0, "2");
        
		// 打印两个数组
        for (String item : list) {
            System.out.println(item);
        }
        System.out.println("--------------------------------");
        for (String item : dest) {
            System.out.println(item);
        }
        System.out.println("--------------------------------");
    }

输出：

	1
	2
	--------------------------------
	2
	2
	3
	--------------------------------

###参考
[Java :deep copy, shallow copy, clone](http://stackoverflow.com/questions/6182565/java-deep-copy-shallow-copy-clone)