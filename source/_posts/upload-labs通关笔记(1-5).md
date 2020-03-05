---
 title: upload-labs通关笔记(1-5)
 date: 2020-03-04 17:34:51
 tags: 文件上传
 categories: 文件上传
---

Upload-labs是一个帮你总结所有类型的上传漏洞的靶场
项目地址：https://github.com/c0ny1/upload-labs

## 第一关
### 思路发现
1. 随意上传php木马，发现前端报错，响应速度超快，且数据包未达到服务器，猜测前端js验证
![2020-3-4-17-52-19](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-17-52-19)
### 源码解读
```php
function checkFile() {
    var file = document.getElementsByName('upload_file')[0].value;
    if (file == null || file == "") {
        alert("请选择要上传的文件!");
        return false;
    }
    //定义允许上传的文件类型
    var allow_ext = ".jpg|.png|.gif";
    //提取上传文件的类型
    var ext_name = file.substring(file.lastIndexOf("."));
    //判断上传文件类型是否允许上传
    if (allow_ext.indexOf(ext_name + "|") == -1) {
        var errMsg = "该文件不允许上传，请上传" + allow_ext + "类型的文件,当前文件类型为：" + ext_name;
        alert(errMsg);
        return false;
    }
}
```
- 逻辑大致是识别上传文件的后缀名 并查看是否是jpg、png、gif中的一个，否则不允许上传

<!--more-->
### bypass
1.修改前端不使用该checkfile函数
![2020-3-4-17-52-5](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-17-52-5)
2. 直接可上传php文件成功
## 第二关
### 思路发现
1. 上传php显示文件类型不正确
![2020-3-4-17-55-0](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-17-55-0)
2. 抓包查看文件类型字段，猜测会判断Content-Type字段
### 源码解读
```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        if (($_FILES['upload_file']['type'] == 'image/jpeg') || ($_FILES['upload_file']['type'] == 'image/png') || ($_FILES['upload_file']['type'] == 'image/gif')) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH . '/' . $_FILES['upload_file']['name']            
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '文件类型不正确，请重新上传！';
        }
    } else {
        $msg = UPLOAD_PATH.'文件夹不存在,请手工创建！';
    }
}
```
- 逻辑大致是识别上传文件的类型 并查看是否是image/jpge、image/png、image/gif中的一个，否则不允许上传

* $_FILES全局变量

|$_FILES['myFile']['name']|上传文件的原名称|
|:----:|:----:|
|$_FILES['myFile']['type']|文件的 MIME 类型|
|$_FILES['myFile']['size']|已上传文件的大小，单位为字节|
|$_FILES['myFile']['tmp_name']|文件被上传后在服务端储存的临时文件名，一般是系统默认。可以在php.ini的upload_tmp_dir 指定|
|$_FILES['myFile']['error']|和该文件上传相关的错误代码|

### bypass
直接抓包改Content-Type为image/png,上传成功
![2020-3-4-17-59-20](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-17-59-20)
## 第三关
### 思路发现
1. 直接上传php脚本显示提示：不允许上传.asp,.aspx,.php,.jsp后缀文件！
![2020-3-4-18-6-56](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-18-6-56)
2. 猜测设置了后缀名黑名单
### 源码解读
```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array('.asp','.aspx','.php','.jsp');
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.'); // 截取'.'后的后缀名
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //收尾去空

        if(!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;// 随机命名
            if (move_uploaded_file($temp_file,$img_path)) {
                 $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '不允许上传.asp,.aspx,.php,.jsp后缀文件！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```
- 逻辑大致是识别上传文件的类型 并查看是否是'.asp','.aspx','.php','.jsp'中的一个，否则不允许上传
### bypass
1. 尝试使用和php一样解析效果的后缀名，如php3、php4、php5、phtml等后缀名
![2020-3-4-18-29-30](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-18-29-30)
2. 上传成功，并成功解析
![2020-3-4-18-29-22](https://raw.githubusercontent.com/bbkali/picbad/master/2020-3-4-18-29-22)

## 第四关
### 思路发现
1. 直接上传php脚本显示提示：此文件不允许上传! 
2. 需抓包验证检验的参数
### 源码解读
```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".php1",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".pHp1",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".ini");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //收尾去空

        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.$file_name;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件不允许上传!';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```
- 逻辑大致大致和上一关类似，只是增加了黑名单量，识别上传文件的类型 并查看是否是黑名单量中的一个，否则不允许上传，可见大小写都包括了，常见的一些后缀也包括了，通过查看资料发现.htaccess后缀文件并未在这个黑名单中，可以进行利用。
### bypass
1. 上传一个.htaccess文件

2. 内容如下，意思为所有文件都用php来解析
```
AddType application/x-httpd-php .png
或者
<FilesMatch "文件名">
SetHandler application/x-httpd-php
</FilesMatch>
```
3. 上传一个图片马hacker.png

4. 直接访问该图片地址，均成功！
## 第五关
### 思路发现
1. 和上一关类似
### 源码解读
```php
$is_upload = false;
$msg = null;
if (isset($_POST['submit'])) {
    if (file_exists(UPLOAD_PATH)) {
        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess");
        $file_name = trim($_FILES['upload_file']['name']);
        $file_name = deldot($file_name);//删除文件名末尾的点
        $file_ext = strrchr($file_name, '.');
        $file_ext = strtolower($file_ext); //转换为小写
        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
        $file_ext = trim($file_ext); //首尾去空
        
        if (!in_array($file_ext, $deny_ext)) {
            $temp_file = $_FILES['upload_file']['tmp_name'];
            $img_path = UPLOAD_PATH.'/'.$file_name;
            if (move_uploaded_file($temp_file, $img_path)) {
                $is_upload = true;
            } else {
                $msg = '上传出错！';
            }
        } else {
            $msg = '此文件类型不允许上传！';
        }
    } else {
        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
    }
}
```
- 逻辑大致大致和上一关类似，只是增加了.htaccess黑名单量，识别上传文件的类型 并查看是否是黑名单量中的一个，否则不允许上传，细看大小写过滤并不全，可以混合大小写进行利用。
### bypass
1. 上传shell.PhP上传成功