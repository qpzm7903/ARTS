Java IO
#java-io



File类，一个抽象类，表示文件或者文件夹。

流，按照输入输出可以划分为输入流和输出流。

按照读取的类型可以划分为字节流和字符流。

|        | 输入流      | 输出流       |
| ------ | ----------- | ------------ |
| 字符流 | Reader      | Writer       |
| 字节流 | InputStream | OutputStream |
|        |             |              |



InputStream

定义：所有字节输入流的抽象父类。

类图：

![image-20200714215740539](image-20200714215740539.png)



输入的来源可以有：

- 字节数组
- String对象
- 文件
- 管道
- 其他种类流的序列
- 其他数据源



OutputStream

定义：所有字节输出流的抽象父类。

类图：

![image-20200714215930805](image-20200714215930805.png)



OutputStream和InputStream基本是一一对应的。所以接下来试试

```java
public class DataStreamDemo {

    public static void main(String[] args) throws IOException {
        try (DataOutputStream dataOutputStream = new DataOutputStream(new FileOutputStream("data.txt"));) {

            dataOutputStream.writeBoolean(true);
            dataOutputStream.writeInt(1);
            dataOutputStream.writeChar('a');
            dataOutputStream.writeChars("testString");
            dataOutputStream.writeByte(2);
            dataOutputStream.writeBytes("123123");
            dataOutputStream.writeDouble(123D);
            dataOutputStream.writeFloat(123F);
            dataOutputStream.writeLong(123L);
        }
        try (DataInputStream dataInputStream = new DataInputStream(new FileInputStream("data.txt"));) {
            System.out.println(dataInputStream.readBoolean());
            System.out.println(dataInputStream.readInt());
            System.out.println(dataInputStream.readChar());
        }

    }
}
```



# NIO

Java NIO 由  channel、buffer、slector组成

channel就是数据通道
buffer和channel之间进行数据读写



## channel
步骤：
- 获取channel
- 将channel的数据读取到buffer
- 读取buffer里的数据

例如
```java
public static void main(String[] args) {  
    System.out.println("Working Directory = " + System.getProperty("user.dir"));  
    try (RandomAccessFile file = new RandomAccessFile("data/test.txt", "rw")) {  
        FileChannel channel = file.getChannel();  
        ByteBuffer buffer = ByteBuffer.allocate(24);  
        int read = channel.read(buffer);  
        while (read != -1) {  
            // change to read mode  
            buffer.flip();  
            while (buffer.hasRemaining()) {  
                System.out.print((char) buffer.get());  
            }  
            // change to write mode  
            buffer.clear();  
            read = channel.read(buffer);  
        }  
  
    } catch (IOException e) {  
        throw new RuntimeException(e);  
    }  
}
```

## buffer
buffer是单通道，也就是同一时刻，只能里面读取数据，或者向里面写数据。

buffer有几个属性
- capacity 容量
- position
	- read
		- 表示读取到的位置
		- position <= limit < capacity - 1
	- write
		- 表示写入的位置
		- position <= capacity - 1
- limit
	- read
		- 表示读取的限制，等于上次写入的位置
	- write
		- 表示写入的限制，等于capacity
- mark

