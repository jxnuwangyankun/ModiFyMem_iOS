动态修改app的内存地址
使用的是Monkey-Dev，可以直接安装到手机中。
如果没有Monkey-Dev，则需要手动编译此.m文件，移动至手机的/usr/bin中，运行，具体流程如下：
<h1 align=center>在iOS上运行c/cpp文件</h1>

1. 编写C/C++文件，demo.c

   ```
   #include <stdio.h>
   int main()
   {
       printf("Hello, world!\n");
       return 0;
   }
   ```

2. 用clang编译，打包。

   ```
   clang -isysroot `xcrun --sdk iphoneos --show-sdk-path` -arch armv7 -arch arm64 -mios-version-min=10.2 -framework Foundation -o HippocampHairSalon_iOS main.m
   ```

   其中的选项和参数可以理解

3. 用jtool/ldid/codesign签名，并根据权限指定可执行文件的"权限"
   权限：ent.xml

   ```
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
           <key>com.shumei.Xcode-9</key>
           <true/>
           <key>get-task-allow</key>
           <true/>
           <key>proc_info-allow</key>
           <true/>
           <key>task_for_pid-allow</key>
           <true/>
           <key>run-unsigned-code</key>
           <true/>
   </dict>
   </plist>
   
   ```

   - 用jtool签名可执行文件：

     ```
     ARCH=arm64 jtool --sign --ent ent.xml helloworld
     ```

   - 用ldid签名可执行文件

     ```
     ldid -Sent.xml HippocampHairSalon_iOS
     ```

   - 用CodeSign签名执行文件

     ```
     codesign codesign -s - --entitlements ent.xml (-s "Developer ID Application: pingsuun wei (RKUDB433U9)") -f HippocampHairSalon_iOS
     ```

4. **将可执行文件移至手机的/usr/bin下**

5. 进入设备中的/usr/bin下，chmod a+x 可执行文件

6. sh 可执行文件

实现参考了[iOS上的内存修改器](https://www.baidu.com/s?wd=HippocampHairSalon%20内存修改&rsv_spt=1&rsv_iqid=0xa551062b0000c5e4&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&rqlang=cn&tn=baiduhome_pg&rsv_enter=1&rsv_dl=tb&oq=Hooper%2520%25E5%2586%2585%25E5%25AD%2598%25E4%25BF%25AE%25E6%2594%25B9&rsv_btype=t&inputT=419&rsv_t=f76b6xi07kYagdoH1vnx9ptoJC%2FYTV6k8L7unBWpbkCtr5NutdTquNXLDOO9YYNG8vfu&rsv_pq=a6ae362c00017522&rsv_sug3=36&rsv_sug2=0&rsv_sug4=419),[readmem](https://github.com/gdbinit/readmem)
