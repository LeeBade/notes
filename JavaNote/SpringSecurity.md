## 加密与安全

加密与安全目标：防窃听、防篡改、防伪造

###  编码算法

编码解码：字符与字节序列的相互转换

URL编码
- 许多服务器仅识别ASCII字符，而在URL的参数部分可能出现非ASCII字符
- URL编码规则
  - A~Z、a~z、0~9、-、_、.、*保持不变；
  - 其他字符先转换为UTF-8编码，每个字节表示为%XX
- java.net.URLEncoder的静态方法decode、encode提供字符串的URL编码解码功能

Base64编码：将二进制序列变更为纯文本
- Base64编码规则
  - 读取6bit数据
  - A~Z对应索引0~25、a~z对应索引26~51、0~9对应索引52~61、62对应+、63对应/
- Java提供java.util.Base64在byte[]和String之间相互转换
  - 若二进制序列长度不是3的倍数，则会自动在末尾添加1个或2个0x00，并且在字符串末尾添加一个=或两个==
- 因为标准的Base64编码会出现+、/、=，所以不适合把Base64编码后的字符串放到URL中
  - 在URL中使用针对URL的Base64编码
  - 编码规则：将+变成-，/变成_：

```JAVA
public class Main {
    public static void main(String[] args) {
        byte[] input = new byte[] { 0x01, 0x02, 0x7f, 0x00 };
        String b64encoded = Base64.getUrlEncoder().encodeToString(input);
        System.out.println(b64encoded);
        byte[] output = Base64.getUrlDecoder().decode(b64encoded);
        System.out.println(Arrays.toString(output));
    }
}
```

- Base64编码的目的：将二进制数据变成文本格式，在文本中处理二进制数据
  - 缺点：传输效率降低
- 如果把Base64的64个字符编码表换成32个、48个或者58个，就可以使用Base32编码，Base48编码和Base58编码
  - 字符越少，编码的效率就会越低

### 哈希算法

通过哈希算法将无限的输入集合映射到有限的输出集合，输出的长度固定
- 目的：验证原始数据是否被篡改
- 相同的输入一定得到相同的输出；
- 不同的输入大概率得到不同的输出，因为碰撞一定会发生
- 安全的哈希算法要求：碰撞概率低、不能根据输入到输出的映射猜测输出
  - 输出长度越长，有限输出集合越大，越难产生碰撞，越安全

消息摘要Message digests：安全的单向哈希函数，接受任意大小的数据并输出固定长度的哈希值。
- 常用的哈希算法

| 算法       | 输出长度（位） | 输出长度（字节） |
|------------|---------------|-----------------|
| MD5        | 128 bits      | 16 bytes        |
| SHA-1      | 160 bits      | 20 bytes        |
| RipeMD-160 | 160 bits      | 20 bytes        |
| SHA-256    | 256 bits      | 32 bytes        |
| SHA-512    | 512 bits      | 64 bytes        |


`java.security`提供MessageDigest抽象类，包含多种消息摘要算法
- 根据哈希算法获取一个MessageDigest实例
- 可反复调用update(byte[])输入数据
- 输入结束后，调用digest()方法获得byte[]数组表示的摘要
- 将摘要转换为十六进制的字符串即标准哈希输出

```JAVA

MessageDigest md = MessageDigest.getInstance("MD5");

md.update("Hello".getBytes("UTF-8"));
md.update("World".getBytes("UTF-8"));

byte[] result = md.digest(); // 16 bytes: 68e109f0f40ca72a15e05cc22786f8e6

System.out.println(new BigInteger(1, result).toString(16));

```

哈希算法用途

- 验证文件是否被篡改
  - 向用户展示正确的哈希输出
  - 在用户结束下载后，本地计算哈希值
  - 如果两个哈希值相同，则文件一定正确
- 存储用户口令
  - 目标不存储明文口令
    - 避免数据库管理员看到用户明文口令；
    - 避免数据库数据泄漏后，用户明文口令被第三方获取
  - 系统计算用户输入的明文口令，并计算其MD5，并与数据库存储的MD5相对比
- 彩虹表攻击
  - 黑客注册多个账号，并存储常用明文口令及其加密口令
  - 如果用户使用常用口令，在得到加密口令后就能得到明文
  - 解决办法：对每个明文口令添加随机数（称为加盐），并且在数据库中存储该随机数

| username | salt  | password                            |
|----------|-------|-------------------------------------|
| bob      | H1r0a | a5022319ff4c56955e22a74abcc2c210   |
| alice    | 7$p2w | e5de688c99e961ed6e560b972dab8b6a   |
| tim      | z5Sk9 | 1eee304b92dc0d105904e7ab58fd2f64   |

### BouncyCastle

BouncyCastle作为第三方库提供哈希算法和加密算法；
- 将jar包放到classpath中
- 使用java.security中的Security.addProvider方法注册BouncyCastle
- 正常调用

```JAVA
// 注册BouncyCastle:
Security.addProvider(new BouncyCastleProvider());

// 按名称正常调用:
MessageDigest md = MessageDigest.getInstance("RipeMD160");  
```

### Hmac算法



Hmac算法：基于密钥的消息认证码算法
- Hash-based Message Authentication Code，是一种更安全的消息摘要算法
- Hmac算法是一种标准算法，必须和其它基础哈希算法配合使用
  - 例如HmacMD5算法，等价于加盐的MD5算法

使用步骤
- ；
；

；
；
。

```JAVA
// 1. 通过名称HmacMD5获取KeyGenerator实例
KeyGenerator keyGen = KeyGenerator.getInstance("HmacMD5");

// 2. 通过KeyGenerator创建一个SecretKey实例
SecretKey key = keyGen.generateKey();
byte[] skey = key.getEncoded();

// 3. 通过名称HmacMD5获取Mac实例；
Mac mac = Mac.getInstance("HmacMD5");

// 4. 用SecretKey初始化Mac实例
mac.init(key);

// 5. 对Mac实例反复调用update(byte[])输入数据
mac.update("HelloWorld".getBytes("UTF-8"));

// 6. 调用Mac实例的doFinal()获取最终的哈希值
byte[] result = mac.doFinal();

```

数据库存储如下

| username | secret_key (64 bytes) | password |
|----------|-----------------------|----------|
| bob      | a8c06e05f92e...5e16   | 7e0387872a57c85ef6dddbaa12f376de |
| alice    | e6a343693985...f4be   | c1f929ac2552642b302e739bc0cdbaac |
| tim      | f27a973dfdc0...6003   | af57651c3a8a73303515804d4af43790 |

### 对称加密算法

对称加密算法：使用密码转换明文和密文
  - 加密：明文+密码->密文
  - 解密：密文+密码->明文
- 因此密钥长度直接决定加密强度
- 对称加密算法可选择工作模式和填充模式；可视为选择对称加密算法的参数和格式

Java标准库提供对称加密接口，使用方法
- 根据算法名称/工作模式/填充模式获取Cipher实例；
- 根据算法名称初始化一个SecretKey实例，密钥必须是指定长度；
- 使用SecretKey初始化Cipher实例，并设置加密或解密模式；
- 传入明文或密文，获得密文或明文。

AES加密

```JAVA

public static void main(String[] args) throws Exception {
    // 原文
    String message = "Hello, world!";
    // 128位密钥 
    byte[] key = "1234567890abcdef".getBytes("UTF-8");

    // 加密
    byte[] data = message.getBytes("UTF-8");
    byte[] encrypted = encrypt(key, data);
    
    // 解密:
    byte[] decrypted = decrypt(key, encrypted);
}

// 加密:
public static byte[] encrypt(byte[] key, byte[] input) throws GeneralSecurityException {
    // 1. 根据算法名称/工作模式/填充模式获取Cipher实例；
    Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");

    // 2. 根据算法名称初始化一个SecretKey实例，密钥必须是指定长度；
    SecretKey keySpec = new SecretKeySpec(key, "AES");

    // 3. 使用SecretKey初始化Cipher实例，并设置加密或解密模式；
    
    cipher.init(Cipher.ENCRYPT_MODE, keySpec);

    // 4. 传入明文或密文，获得密文或明文
    return cipher.doFinal(input);
}

// 解密:
public static byte[] decrypt(byte[] key, byte[] input) throws GeneralSecurityException {

    Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");

    SecretKey keySpec = new SecretKeySpec(key, "AES");

    cipher.init(Cipher.DECRYPT_MODE, keySpec);

    return cipher.doFinal(input);
}

```

AES加密模式
- ECB模式：只需一个固定长度的密钥，固定的明文生成固定的密文
- CBC模式：需要一个固定长度的密钥和一个随机数，对同一份明文，每次生成的密文都不同


口令加密算法
- AES加密模式中，密钥的长度固定为128/192/256位，用户决定的口令不仅位数不够且随机，而且其口令有一定规律
- 因此需要PBE算法，将用户口令和一个安全随机的口令映射到密钥，再进行AES加密

PBE：Password Based Encryption
- 注册BouncyCastle，并指定算法为PBEwithSHA1and128bitAES-CBC-BC

```JAVA
public static void main(String[] args) throws Exception {
    // 注册BouncyCastle
    Security.addProvider(new BouncyCastleProvider());
    // 原文
    String message = "Hello, world!";
    // 用户口令
    String password = "hello12345";
    // 随机生成salt
    byte[] salt = SecureRandom.getInstanceStrong().generateSeed(16);
    
    // 加密:
    byte[] data = message.getBytes("UTF-8");
    byte[] encrypted = encrypt(password, salt, data);
    
    // 解密:
    byte[] decrypted = decrypt(password, salt, encrypted);

}
// 加密:
public static byte[] encrypt(String password, byte[] salt, byte[] input) throws GeneralSecurityException {
    PBEKeySpec keySpec = new PBEKeySpec(password.toCharArray());

    SecretKeyFactory skeyFactory = SecretKeyFactory.getInstance("PBEwithSHA1and128bitAES-CBC-BC");

    SecretKey skey = skeyFactory.generateSecret(keySpec);

    //指定循环次数；循环次数越多，暴力破解需要的计算量就越大
    PBEParameterSpec pbeps = new PBEParameterSpec(salt, 1000);

    // 指定使用PBEwithSHA1and128bitAES-CBC-BC算法
    Cipher cipher = Cipher.getInstance("PBEwithSHA1and128bitAES-CBC-BC");

    // 同时传入SecretKey和PBEParameterSpec生成AES密钥
    cipher.init(Cipher.ENCRYPT_MODE, skey, pbeps);

    // 使用密钥生成密文
    return cipher.doFinal(input);
}

// 解密:
public static byte[] decrypt(String password, byte[] salt, byte[] input) throws GeneralSecurityException {

    PBEKeySpec keySpec = new PBEKeySpec(password.toCharArray());

    SecretKeyFactory skeyFactory = SecretKeyFactory.getInstance("PBEwithSHA1and128bitAES-CBC-BC");

    SecretKey skey = skeyFactory.generateSecret(keySpec);

    PBEParameterSpec pbeps = new PBEParameterSpec(salt, 1000);

    Cipher cipher = Cipher.getInstance("PBEwithSHA1and128bitAES-CBC-BC");

    cipher.init(Cipher.DECRYPT_MODE, skey, pbeps);

    return cipher.doFinal(input);
}


```

同时保存随机生成的salt，即使用户口令非常弱，安全性也非常高


### 密钥交换算法

Diffie-Hellman密钥协商算法：在不安全信道上，双方协商一个共同的密钥，这个密钥不会通过网络传输
- 方法：双方各自生成一个私钥和一个公钥，私钥仅对自己可见，互相交换公钥，并根据自己的私钥和对方的公钥，生成最终的密钥，DH算法保证双方各自计算出的密钥相同

Java使用DH算法
- 生成私钥和公钥，计算出私钥，并且使用该密钥加密AES
- 使用时请另找代码记录在此处

### 非对称加密算法

假设有N个人相互传递文件，那么需要协商N*(N-1)/2次，因此需要RSA算法
- 做法：接收方向发送方发送公钥，发送方使用公钥加密信息，接收方使用私钥解密；
  - 优点：不需要协商密钥
  - 缺点：运算速度非常慢
- 受限于上述特点，非对称加密算法在交换密钥时使用

1. 接收方向发送方发送公钥
2. 发送方使用公钥加密口令
3. 接收方使用私钥解密口令
4. 双方使用口令进行AES加密通信

- 使用时请另找代码记录在此处

### 签名解密

签名：私钥加密，公钥解密
- 所有人都能获取信息，但是只有一个人能发送信息，即没有人可以伪造消息，发送方也不能抵赖
- MD5withRSA、SHA1withRSA、SHA256withRSA

```JAVA

// 对信息的哈希进行签名，而不是信息本身
signature = encrypt(privateKey, sha256(message))
对签名进行验证实际上就是用公钥解密：

// 使用公钥对签名进行解密
hash = decrypt(publicKey, signature)


```

### 数字证书

数字证书=摘要算法+非对称加密算法+签名算法
- 摘要算法：确保数据没有被篡改
- 非对称加密算法：对数据进行加解密
- 签名算法：确保数据完整性和抗否认性

数字证书可防止中间人攻击
- 采用链式签名认证，即通过根证书（Root CA）去签名下一级证书，这样层层签名，直到最终的用户证书
- 而Root CA证书内置于操作系统中，所以，任何经过CA认证的数字证书都可以对其本身进行校验，确保证书本身不是伪造的。
- HTTPS协议是数字证书的应用；浏览器会自动验证证书的有效性