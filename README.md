# Android反编译指南

 本文主要提供了各种Android版本的代码结构，以及反编译工具的使用举例。各种工具打包下载地址：
 https://github.com/zhangjizxc/AndroidHackTools
### 怎么获取想找的文件
- fwk部分
```
adb pull system/framework/ ./fwk
```
- app部分(无论系统预置还是自己安装的app)
```
1.手机上打开App.
2.通过命令 adb shell dumpsys window windows |grep -i focus 查看当前的包名。
3.通过命令 adb shell dumpsys package 包名 | grep -i path 获取apk所在路径。
4.通过命令 adb pull apk路径 ./app 获取apk到路径

```
### apktool(Apk反编译)
>主要反编译apk无人不知的利器，反向编译正向打包，重新签名。基本大家都会用，简单举例。 通过apktool d 解压APK包查看对应的AndroidManifest.xml 、smali文件、res xml等。

工具usage:
```
Apktool v2.3.1 - a tool for reengineering Android apk files
with smali v2.2.2 and baksmali v2.2.1
Copyright 2014 Ryszard Wiśniewski <brut.alll@gmail.com>
Updated by Connor Tumbleson <connor.tumbleson@gmail.com>

usage: apktool
 -advance,--advanced   prints advance information.
 -version,--version    prints the version then exits
usage: apktool if|install-framework [options] <framework.apk>
 -p,--frame-path <dir>   Stores framework files into <dir>.
 -t,--tag <tag>          Tag frameworks using <tag>.
usage: apktool d[ecode] [options] <file_apk>
 -f,--force              Force delete destination directory.
 -o,--output <dir>       The name of folder that gets written. Default is apk.out
 -p,--frame-path <dir>   Uses framework files located in <dir>.
 -r,--no-res             Do not decode resources.
 -s,--no-src             Do not decode sources.
 -t,--frame-tag <tag>    Uses framework files tagged by <tag>.
usage: apktool b[uild] [options] <app_path>
 -f,--force-all          Skip changes detection and build all files.
 -o,--output <dir>       The name of apk that gets written. Default is dist/name.apk
 -p,--frame-path <dir>   Uses framework files located in <dir>.

For additional info, see: http://ibotpeaches.github.io/Apktool/ 
For smali/baksmali info, see: https://github.com/JesusFreke/smali
```
举例：
```
./apktool d -o ~/tt_code/ NewsArticle-release.apk
```

### baksmali.jar与smali.jar
> 一对方法，odex/oat文件 与 smali文件进行互相转化。

工具usage:
```
usage: baksmali deodex [<options>] <file>
Deodexes an odex/oat file

Options:
  -a,--api <api> - The numeric api level of the file being disassembled. (default: 15)
  --accessor-comments,--ac <boolean> - Generate helper comments for synthetic accessors. True by default, use --accessor-
      comments=false to disable. (default: True)
  --allow-odex-opcodes - Allows odex opcodes to be disassembled, even if the result won't be able to be reassembled.
  -b,--bootclasspath,--bcp <classpath> - A colon separated list of the files to include in the bootclasspath when analyzing the 
      dex file. If not specified, baksmali will attempt to choose an appropriate default. When analyzing oat files, this can 
      simply be the path to the device's boot.oat file. A single empty string can be used to specify that an empty bootclasspath 
      should be used. (e.g. --bootclasspath "") See baksmali help classpath for more information.
  -c,--classpath,--cp <classpath> - A colon separated list of additional files to include in the classpath when analyzing the dex 
      file. These will be added to the classpath after any bootclasspath entries.
  --check-package-private-access,--package-private,--checkpp,--pp - Use the package-private access check when calculating vtable 
      indexes. This is enabled by default for oat files. For odex files, this is only needed for odexes from 4.2.0. It was 
      reverted in 4.2.1.
  --classes <classes> - A comma separated list of classes. Only disassemble these classes
  --code-offsets,--offsets,--off - Add a comment before each instruction with it's code offset within the method.
  -d,--classpath-dir,--cpd,--dir <dir> - A directory to search for classpath files. This option can be used multiple times to 
      specify multiple directories to search. They will be searched in the order they are provided.
  --debug-info,--di <boolean> - Whether to include debug information in the output (.local, .param, .line, etc.). True by 
      default, use --debug-info=false to disable. (default: True)
  -h,-?,--help - Show usage information for this command.
  --implicit-references,--implicit,--ir - Use implicit method and field references (without the class name) for methods and 
      fields from the current class.
  --inline-table,--inline,--it <file> - Specify a file containing a custom inline method table to use. See the "deodexerant" tool 
      in the smali github repository to dump the inline method table from a device that uses dalvik.
  -j,--jobs <n> - The number of threads to use. Defaults to the number of cores available. (default: 4)
  -l,--use-locals - When disassembling, output the .locals directive with the number of non-parameter registers instead of the .
      registers directive with the total number of registers.
  --normalize-virtual-methods,--norm,--nvm - Normalize virtual method references to use the base class where the method is 
      originally declared.
  -o,--output <dir> - The directory to write the disassembled files to. (default: out)
  --parameter-registers,--preg,--pr <boolean> - Use the pNN syntax for registers that refer to a method parameter on method 
      entry. True by default, use --parameter-registers=false to disable. (default: True)
  -r,--register-info <register info specifier> - Add comments before/after each instruction with information about register 
      types. The value is a comma-separated list of any of ALL, ALLPRE, ALLPOST, ARGS, DEST, MERGE and FULLMERGE. See "baksmali 
      help register-info" for more information.
  --resolve-resources,--rr <resource prefix> <public.xml file> - This will attempt to find any resource id references within the 
      bytecode and add a comment with the name of the resource being referenced. The parameter accepts 2 values:an arbitrary 
      resource prefix and the path to a public.xml file. For example: --resolve-resources android.R framework/res/values/public.
      xml. This option can be specified multiple times to provide resources from multiple packages.
  --sequential-labels,--seq,--sl - Create label names using a sequential numbering scheme per label type, rather than using the 
      bytecode address.
  <file> - A dex/apk/oat/odex file. For apk or oat files that contain multiple dex files, you can specify the specific entry to 
      use as if the apk/oat file was a directory. e.g. "app.apk/classes2.dex". For more information, see "baksmali help input".

```
举例：
```
java -jar baksmali-2.2.2.jar de -b MIUI/framework/arm64/boot.oat -o MIUI/decode/ MIUI/QPerformance.odex
```
### oat2dex.jar
> 从oat/odex文件转成 dex文件

工具usage:
```
Easy oat2dex 0.88
Usage:
 java -jar oat2dex.jar [options] <action>
[options]
 Api level (for raw odex): -a <integer>
 Output folder: -o <folder path>
 Print detail : -v
<action>
 Get dex from boot.oat: boot <boot.oat/boot-folder>
 Get dex from oat/odex: <oat/odex-file/folder> <boot-class-folder>
 Get raw odex from oat: odex <oat-file/folder>
 Get raw odex smali   : smali <oat/odex-file>
 Deodex framework     : devfw [empty or path of /system/framework/]
```
举例：
```
 java -jar ../../oat2dex.jar -o HwPowerGenieEngine3/odex_decode/ odex HwPowerGenieEngine3/oat/arm64/HwPowerGenieEngine3.odex
```
```
java -jar ../oat2dex.jar -o mateS_5.1.1/framework_code_decode/ boot mateS_5.1.1/arm/boot.oat
```

### dex-tools-2.1
> 从dex文件转问jar文件，然后通过jd-jui 打开jar文件查看

工具usage:
```
d2j-dex2jar -- convert dex to jar
usage: d2j-dex2jar [options] <file0> [file1 ... fileN]
options:
 -d,--debug-info              translate debug info
 -e,--exception-file <file>   detail exception file, default is $current_dir/[fi
                              le-name]-error.zip
 -f,--force                   force overwrite
 -h,--help                    Print this help message
 -n,--not-handle-exception    not handle any exceptions thrown by dex2jar
 -nc,--no-code
 -o,--output <out-jar-file>   output .jar file, default is $current_dir/[file-na
                              me]-dex2jar.jar
 -os,--optmize-synchronized   optimize-synchronized
 -p,--print-ir                print ir to System.out
 -r,--reuse-reg               reuse register while generate java .class file
 -s                           same with --topological-sort/-ts
 -ts,--topological-sort       sort block by topological, that will generate more
                               readable code, default enabled
version: reader-2.1-20171001-lanchon, translator-2.1-20171001-lanchon, ir-2.1-20171001-lanchon
```
举例：
```
./dex-tools-2.1-20171001-lanchon/d2j-dex2jar.sh -o Huawei/mateS_5.1.1/hwframework_jar_decode/ Huawei/mateS_5.1.1/framework_code_decode/hwframework.dex 
```
### dextra
> 不太常用。主要是odex/oat/vdex/dex 文件的dump
>  用法：http://newandroidbook.com/tools/dextra.html

### sdat2img.py与img2simg
>不太常用。当没有真机，只有ROM包的时候才会用到。从ROM包中解出需要的fwk/app文件时才会用。
>用法：https://forum.xda-developers.com/android/software-hacking/how-to-conver-lollipop-dat-files-to-t2978952

### vdexExtractor
Android8.0 以后的vdex文件反编译工具。
用法：https://github.com/anestisb/vdexExtractor

