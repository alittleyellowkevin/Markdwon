# Java

## Java修饰符

### 权限修饰符

1. private ：被声明为private的方法，变量和构造方法，只能被所属类访问，并且类和接口不能声明为private，私有变量只能通过getter方法被外界访问
2. public：能被任何类访问，不在同一个包内则导入此包
3. protected：被声明为protect的方法，变量和构造方法，能被同一个包内的其他类访问，以及不同包的子类访问，但是类与接口都不能声明protected，并且接口的成员也不能声明为protected

### 非权限修饰符

1. final：final变量无法修改，final方法无法重写，final类无法继承
2. static：静态变量和静态方法应该用  _className.method_  访问
3. abstruct：抽象类和抽象方法必须被继承
4. synchronize：声明的方法同一时间只能被一个线程访问

