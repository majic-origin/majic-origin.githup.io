### 一、文本文件复制（字符缓冲流实现）
字符缓冲流（BufferedReader / BufferedWriter）是文本文件复制最常用的方式，它自带缓冲区，效率高，还支持按行读写，非常方便。

```import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class TextFileCopy {
    public static void main(String[] args) {
        // 源文件路径和目标文件路径
        String sourcePath = "source.txt";
        String targetPath = "target.txt";

        BufferedReader br = null;
        BufferedWriter bw = null;

        try {
            // 1. 创建字符缓冲输入流，读取文本文件
            br = new BufferedReader(new FileReader(sourcePath));
            // 2. 创建字符缓冲输出流，写入目标文件
            bw = new BufferedWriter(new FileWriter(targetPath));

            String line;
            // 3. 按行读取，直到文件末尾（返回null）
            while ((line = br.readLine()) != null) {
                bw.write(line);       // 写入一行数据
                bw.newLine();         // 写入换行符，保证和原文件格式一致
            }

            System.out.println("文本文件复制完成！");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4. 关闭流，释放资源（先开后关）
            try {
                if (bw != null) bw.close();
                if (br != null) br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}```



原理说明
字符流（Reader/Writer）专门处理文本文件，能自动处理编码，避免乱码。
BufferedReader.readLine()：一次读取一行文本，非常适合复制文本文件。
BufferedWriter.newLine()：写入系统默认换行符，保证跨平台文件格式一致。
缓冲流自带 8KB 缓冲区，减少磁盘 IO 次数，比普通字符流效率高很多。
### 二、任意文件复制（字节缓冲流实现）
字节缓冲流（BufferedInputStream / BufferedOutputStream）是 “万能复制” 方式，支持文本、图片、视频、压缩包等所有类型的文件，原理是按字节读写数据。
完整代码

```import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class AnyFileCopy {
    public static void main(String[] args) {
        // 源文件路径和目标文件路径（支持所有格式）
        String sourcePath = "source.jpg";   // 可以改成.mp4/.zip/.txt等任意文件
        String targetPath = "target.jpg";

        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;

        try {
            // 1. 创建字节缓冲输入流，读取文件
            bis = new BufferedInputStream(new FileInputStream(sourcePath));
            // 2. 创建字节缓冲输出流，写入目标文件
            bos = new BufferedOutputStream(new FileOutputStream(targetPath));

            // 3. 定义缓冲区数组，每次读写1024字节（1KB）
            byte[] buffer = new byte[1024];
            int len;
            // 4. 循环读取数据，直到文件末尾（返回-1）
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len); // 写入实际读取到的字节数
            }

            System.out.println("任意文件复制完成！");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 5. 关闭流，释放资源
            try {
                if (bos != null) bos.close();
                if (bis != null) bis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}```

原理说明
字节流（InputStream/OutputStream）直接操作文件的原始字节，不关心文件格式，所以能复制任意文件。
BufferedInputStream/BufferedOutputStream 自带 8KB 缓冲区，比普通字节流读写效率提升很多。
read(buffer) 会把数据读到缓冲区数组里，返回值是实际读取的字节数，write(buffer,0,len) 只写读取到的有效字节，避免多余数据写入。

### 三、两种方式对比
<html>
<body>
<!--StartFragment--><html><head>
            
            <style>
              table { border-collapse: collapse; width: 100%; }
              th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
            </style>
          </head>
          <body>
复制方式 | 适用场景 | 优点 | 缺点
-- | -- | -- | --
字符缓冲流 | 文本文件（.txt/.java/.md 等） | 读写方便、支持按行操作、无乱码 | 只能处理文本文件
字节缓冲流 | 所有文件（文本 / 图片 / 视频 / 压缩包等） | 通用、效率高、不依赖文件格式 | 不能直接处理文本换行，需手动控制


        
      </body></html><!--EndFragment-->
</body>
</html>
### 四、总结
字符缓冲流是文本文件复制的首选，代码简洁、效率高，适合处理纯文本内容。
字节缓冲流是万能复制方案，不管什么格式的文件都能复制，是竞赛和项目中最常用的文件复制方式。
两种方式都要注意流的关闭，推荐使用 try-with-resources 语法（JDK7+），可以自动关闭流，不用手动写 finally 块。

补充：try-with-resources 简化版代码（可选）

```// 字符缓冲流简化版
public class TextFileCopyTry {
    public static void main(String[] args) {
        try (BufferedReader br = new BufferedReader(new FileReader("source.txt"));
             BufferedWriter bw = new BufferedWriter(new FileWriter("target.txt"))) {

            String line;
            while ((line = br.readLine()) != null) {
                bw.write(line);
                bw.newLine();
            }
            System.out.println("复制完成！");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}```