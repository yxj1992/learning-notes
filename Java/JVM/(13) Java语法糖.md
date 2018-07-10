### Java语法糖  
语法糖不会提供实质性功能改进，但或能提高效率、提升语法严谨性、减少编码出错机会  

#### 泛型与类型擦除  
泛型是JDK1.5新增特性，本质是参数化类型（Parametersized Type）的应用，即数据类型被指定为参数  
这种参数类型可用在类、接口和方法的创建中，分别称泛型类、泛型接口和泛型方法  

泛型技术在C#和Java中的根本性分歧  
**C#泛型：**  
无论在程序源码、编译后的IL（Intermediate Language，中间语言，这时泛型是一个占位符）、运行期的CLR都切实存在  
List<int>与List<String>是两个不同的类型，它们在系统运行期生成，有自己的虚方法表和类型数据  
这种实现称类型膨胀  
基于这种方法实现的泛型称真实泛型  

**Java泛型：**  
只在程序源码中存在，在编译后的字节码文件中替换为原生类型（Raw Type，也称裸类型），且在相应位置插入强制转型代码  
对于运行期Java语言ArrayList<int>与ArrayList<String>是同一个类  
Java泛型技术实际上是Java的一颗语法糖  
Java泛型实现方法称类型擦除  
注，所谓的擦除仅是对方法Code属性中的字节码进行擦除，实际上元数据中保留了泛型信息，这是能通过反射手段取得参数化类型的根本依据  
基于这种方法实现的泛型称伪泛型  

**当泛型遇见重载**  

- 入参不同出参相同  
 
```
public static void method(List<String> list){}  
public static void method(List<Integer> list){}  
```  

以上代码不能被编译  
因编译后参数List<String>和List<Integer>被擦除，变成相同的原生类型List<E>，导致两个方法的特征签名和描述符完全相同，不可共存于一个Class文件  

- 入参不同出参不同  

```
public static String method(List<String> list){}
public static Int method(List<Integer> list){}
```  

以上代码可以被编译和执行  
因两个方法的返回值不同，即，虽特征签名相同但描述符不同，可共存于一个Class文件  
上述方法特征签名指Java语言中的特征签名，内容包括方法名称、参数顺序、参数类型  
此外还有字节码中的特征签名，内容包括方法名称、参数顺序、参数类型、方法返回值、受查异常表  

#### 自动装箱、拆箱与遍历循环  
```
/**
 * 自动装箱、拆箱与遍历循环
 */
public static void main(String... args) {
    List<Integer> list = Arrays.asList(1, 2, 3, 4);
    // 若JDK1.7可写成 List<Integer> list =[1, 2, 3, 4];
    int sum = 0;
    for (int i : list) {
        sum += i;
    }
    System.out.println(sum);
}

```  

```
/**
 * 自动装箱、拆箱与遍历循环编译后
 */
public static void main(String[] args) {
    List list = Arrays.asList(new Integer[]{
            Integer.valueOf(1), 
            Integer.valueOf(2), 
            Integer.valueOf(3), 
            Integer.valueOf(4)
    });
    int sum = 0;
    for (Iterator localIterator = list.iterator(); localIterator.hasNext(); ) {
        int i = ((Integer) localIterator.next()).intValue();
        sum += i;
    }
    System.out.println(sum);
}

```  

包装类“==”运算不遇到算术运算时不会自动拆箱，equals()方法不处理数据转型关系  

#### 条件编译  
Java语言条件编译的实现：  
根据布尔常量值真假，编译器把分支中不成立的代码块消除掉  
这一工作在编译器解除语法糖阶段（com.sun.tools.javac.comp.Lower类）完成  
```
/**
 * 条件编译
 */
public static void main(String[] args) {
    if (true) {
        System.out.println(" block 1");
    } else {
        System.out.println(" block 2");
    }
}

```  

```
/**
 * 条件编译反编译
 */
public static void main(String[] args) {
    System.out.println(" block 1");
}

```  