# SDES
SDES: RSA + 6DES
<div style="text-align:right">14307130360</div>
<div style="text-align:right">王丹青</div>
[toc]

###项目目录
```
SDES                         
├── Bootstrap		# Bootstrap网页框架资源
│   ├── css				# Bootstrap的css样式
│   ├── fonts			# Bootstrap的fonts样式
│   └── js            	# Bootstrap的js脚本
├── Example			# 测试样例文本
│   ├── clear.txt		# 明文文本
│   ├── key.txt			# DES密钥
│   ├── cipherfile.txt	# 加密之后的密文文本
│   └── clearfile.txt  	# 初始密钥文本
├── BigInt.js		# js大数运算库
├── DES.js			# DES算法脚本
├── RSA.js			# RSA算法脚本
├── SDES.js			# DES&RSA网站
└── 说明文档.pdf		# 说明文档
```

---
###实现功能
- 模拟发送方A和接收方B进行通讯传输
- 主要数据内容由CBC文本模式进行DES加密
- DES的密钥由RSA进行加密

----

###功能说明
#### - 程序运行
- 选择“SDES.html”文件，使用chrome浏览器打开（部分内容不兼容IE）
#### - 选择“发送方A”进行数据加密发送
1. 点击“**获取用户B的密钥**”，自动生成随机的密钥对及模数（e和n显示在发送方A处，d和n显示在接收方B处）
2. 输入DES密钥（64bit），点击“**RSA加密**”进行加密，加密结果显示在对应文本框中
3. 点击“**浏览**”，上传DES明文数据
4. 点击“**DES加密**”进行加密，加密结果显示在对应文本框中
5. （*可选*）点击“下载密文文本”进行下载
#### - 选择“接收方B”进行数据获取解密
1. 刚才生成的RSA的私钥d和模数n自动显示，相应的RSA密文也显示在密文框中
2. 点击“**RSA快速解密**”进行解密
3. 点击“**检验RSA**”，检测RSA的明文和解密之后的文本是否相同，如果正确将得到“RSA is correct！”提示框
4. DES密文也显示在对应密文框中（也可以自行浏览上传）
5. 点击“**DES解密**”进行解密，加密结果显示在对应文本框中
6. 点击“**检验DES**”，检测DES的明文和解密之后的文本是否相同，如果正确将得到“DES is correct！”提示框
7. （*可选*）点击“下载密文文本”进行下载

---

###注意事项
- 模拟发送方A和接收方B进行通信，A加密之后的相应文本***无需复制***自动出现在B文本框中。
- RSA使用128bit的密钥（即模数n）
- DES密钥必须为64bit
- 上传只支持单一文件上传，同一个上传框内只包含最后一次上传的结果
- 下载的文本为当次运行的结果，多次加解密之后再进行下载，只包含最后一次的结果

---

###关键代码简要说明
#### - 大数运算模块
*主要在BigInt.js文件中*
- js的大数计算库来自网站[http://www.ohdave.com/rsa/](http://www.ohdave.com/rsa/)的开源代码
- 主要参考了大数的加，减，乘，除，模乘，模除等运算

#### - RSA加解密模块
*主要在RSA.js文件中*
*以“RSA_”开头的函数是和html文件的接口，主要实现字符串转换和调用加解密函数*
1. **isPrime(n)**：素数检测，使用Miller-Rabin检验法，检验100次
	- 为了加速检验，首先判断n是否能够整除10000以内的素数，快速排除不合法的数
	- 10000以内的素数表可见RSA.js最后数组
2. **createP(bit)**：生成长度为bit的随机大素数
3. **createK()**：生成RSA密钥对（e，d）以及模数n
	- 不符合规范的密钥对将反复生成，直到符合要求
	- 确保n为128bit
	- p，q作为全局变量保存，以供后续快速解密使用
4. **pre_cal(m, r, a, n)**：预计算幂，计算 $a^{b} mod$ $n$  
5. **fast_cal(m, r, e, n)**：通过幂运算，快速加密
6. **encrypt(m, n, e)**：RSA加密函数
7. **decrypt_fast(c, d, n, p, q)**：RSA快速解密函数
8. **decrypt(c, d, n)**：RSA普通解密函数
	- 因为速度太慢被舍弃

#### - DES加解密模块
*主要在DES.js文件中*
*以“show_”开头的函数是和html文件的接口，主要实现字符串转换和调用加解密函数*
1. **createKey(initk)**：按照DES密钥生成规则，返回生成的6个密钥
2. **F(R, K)**：DES轮函数
3. **DES_encrypt(m, initk)**：DES加密函数
4. **DES_decrypt(c, initk)**：DES解密函数
5. **CBC(m, initk)**：CBC模式的分组链接加密函数
6. **CBC_inv(c, initk)**：CBC模式的分组链接解密函数