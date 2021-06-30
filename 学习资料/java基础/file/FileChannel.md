# FileChannel的map

## map方法

FileChannel提供了map方法来把文件影射为内存映像文件：

```text
MappedByteBuffer map(int mode,long position,long size); 
```

可以把文件的从position开始的size大小的区域映射为内存映像文件，mode指出了 可访问该内存映像文件的方式：READ_ONLY，READ_WRITE，PRIVATE。

1. READ_ONLY,（只读）： 试图修改得到的缓冲区将导致抛出 ReadOnlyBufferException.(MapMode.READ_ONLY)
2. READ_WRITE（读/写）： 对得到的缓冲区的更改最终将传播到文件；该更改对映射到同一文件的其他程序不一定是可见的。 (MapMode.READ_WRITE)
3. PRIVATE（专用）： 对得到的缓冲区的更改不会传播到文件，并且该更改对映射到同一文件的其他程序也不是可见的；相反，会创建缓冲区已修改部分的专用副本。 (MapMode.PRIVATE)

按上节课的例子，我们提供一个Java版的程序，来实现文件内容的拷贝：

```java
    public static void main(String args[]){
        RandomAccessFile f = null;
        try {
            f = new RandomAccessFile("C:/hinusDocs/hello.txt", "rw");
            RandomAccessFile world = new RandomAccessFile("C:/hinusDocs/world.txt", "rw");
            FileChannel fc = f.getChannel();
            MappedByteBuffer buf = fc.map(FileChannel.MapMode.READ_WRITE, 0, 20);

            FileChannel worldChannel = world.getChannel();
            MappedByteBuffer worldBuf = worldChannel.map(FileChannel.MapMode.READ_WRITE, 0, 20);
            worldBuf.put(buf);

            fc.close();
            f.close();
            world.close();
            worldChannel.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

## 使用MappedByteBuffer实现内存共享

实际上，我们可以在两个Java进程中各使用一次map将文件映射到内存，这样两个进程就可以直接通过这个共享内存来实现进程间的数据通信了。例如，先启一个进程，运行：

```java
public class Main {
    public static void main(String args[]){
        RandomAccessFile f = null;
        try {
            f = new RandomAccessFile("C:/hinusDocs/hello.txt", "rw");
            FileChannel fc = f.getChannel();
            MappedByteBuffer buf = fc.map(FileChannel.MapMode.READ_WRITE, 0, 20);

            buf.put("how are you?".getBytes());

            Thread.sleep(10000);

            fc.close();
            f.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

然后，再启一个进程，运行：

```java
public class MapMemoryBuffer {
    public static void main(String[] args) throws Exception {
        RandomAccessFile f = new RandomAccessFile("C:/hinusDocs/hello.txt", "rw");
        FileChannel fc = f.getChannel();
        MappedByteBuffer buf = fc.map(FileChannel.MapMode.READ_WRITE, 0, fc.size());

        while (buf.hasRemaining()) {
            System.out.print((char)buf.get());
        }
        System.out.println();
    }
}
```

就可以看到：



![img](https://pic2.zhimg.com/80/v2-1a2e298047df414475e7784c1f25b6fd_720w.png)

在第二个进程里就可以读到第一个进程往ByteBuffer里写的内容了。这一切看起来就像是发生在内存中，而不是文件中。所以这种方式也叫共享内存。



## 原理

在sun.nio.ch.FileChannelImpl里有map的具体实现：

```text
            try {
                // If no exception was thrown from map0, the address is valid
                addr = map0(imode, mapPosition, mapSize);
            } catch (OutOfMemoryError x) {


    private native long map0(int prot, long position, long length)
```

调用的是一个native方法，这个native方法的实现位于solaris/native/sun/nio/ch/FileChannelImpl.c

是这样的

```java
JNIEXPORT jlong JNICALL
Java_sun_nio_ch_FileChannelImpl_map0(JNIEnv *env, jobject this,
                                     jint prot, jlong off, jlong len)
{
    void *mapAddress = 0;
    jobject fdo = (*env)->GetObjectField(env, this, chan_fd);
    jint fd = fdval(env, fdo);
    int protections = 0;
    int flags = 0;

    if (prot == sun_nio_ch_FileChannelImpl_MAP_RO) {
        protections = PROT_READ;
        flags = MAP_SHARED;
    } else if (prot == sun_nio_ch_FileChannelImpl_MAP_RW) {
        protections = PROT_WRITE | PROT_READ;
        flags = MAP_SHARED;
    } else if (prot == sun_nio_ch_FileChannelImpl_MAP_PV) {
        protections =  PROT_WRITE | PROT_READ;
        flags = MAP_PRIVATE;
    }

    mapAddress = mmap64(
        0,                    /* Let OS decide location */
        len,                  /* Number of bytes to map */
        protections,          /* File permissions */
        flags,                /* Changes are shared */
        fd,                   /* File descriptor of mapped file */
        off);                 /* Offset into file */

    if (mapAddress == MAP_FAILED) {
        if (errno == ENOMEM) {
            JNU_ThrowOutOfMemoryError(env, "Map failed");
            return IOS_THROWN;
        }
        return handle(env, -1, "Map failed");
    }

    return ((jlong) (unsigned long) mapAddress);
}
```

哈哈，原来还是使用的mmap这个系统调用。所以，Java中的很多操作其实就是linux系统调用封了一层皮而已。

## 要注意的地方

我们在讲解DirectBuffer的时候，就跳过了一个重要的地方，那就是它是怎么回收的。这个机制十分复杂，牵扯到GC的具体实现，同样的问题，在MappedByteBuffer中也存在。

如果你使用了FileChannel.map方法去映射一个文件，然后马上关闭这个channel，然后再试图删除文件，就会发现不能成功。这是因为MappedByteBuffer还没有被回收，文件句柄还没有释放。而具体什么时候才会释放，以及能不能提前释放。这个问题等我们讲完了GC之后再来看。现在只需要知道基本的用法就好了。