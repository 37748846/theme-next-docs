---
abbrlink: ''
categories: []
date: '202300329'
excerpt: OpenSSL自建CA、自签SSL证书 自签证书 首先约定好文件后缀  .key 私钥文件 Private Key .csr 证书请求文件,csr
  = Certificate Signing Requ...
tags:
- OpenSSL
- CA
- SSL
- 证书
title: OpenSSL自建CA、自签SSL证书
updated: Sun, 02 Apr 2023 17:45:33 GMT
---
# OpenSSL自建CA、自签SSL证书

### 自签证书

#### 首先约定好文件后缀

* .key 私钥文件 Private Key
* .csr 证书请求文件,csr = Certificate Signing Requests
* .crt 证书文件 crt = Certificate
* .crl 证书吊销列表 crl = Certificate Revocation List
* .pem 包含私钥的证书,但是自动生成的不包含

#### 自建CA

生成`根`的私钥


| 1 | openssl genrsa -des3 -out ca/root.key 2048 |
| - | ------------------------------------------ |

生成`根`的证书请求文件,这时会提示交互输入一些信息


| 1 | openssl req -new -key ca/root.key -out root.csr |
| - | ----------------------------------------------- |

也可以


| 1 | openssl req -new -key ca/root.key -out ca/root.csr -subj "/C=CN/O=Test/OU=Test CA/CN=Test CA/emailAddress=ca@test.com" |
| - | ---------------------------------------------------------------------------------------------------------------------- |

自签名


| 1 | openssl x509 -req -days 3650 -signkey ca/root.key -in ca/root.csr -out ca/root.crt |
| - | ---------------------------------------------------------------------------------- |

合成包含私钥的pem证书(文件)


| 1 | cat ca/root.key ca/root.crt > ca/root.pem |
| - | ----------------------------------------- |

这个ca/root.crt和ca/root.pem就是自签名CA,用它可以对其它证书进行签名

RootCA也可以添加扩展信息,比如 basicConstraints = CA:TRUE,下面会提到

#### 用自建CA进行签名

生成`server1`的key


| 1 | openssl genrsa -des3 -out keys/server1.key 2048 |
| - | ----------------------------------------------- |

生成`server1`的证书请求文件


| 1 | openssl req -new -key keys/server1.key -out csrs/server1.csr -subj "/C=CN/O=Test/OU=dev/CN=server1.test.com/emailAddress=ops@test.com" |
| - | -------------------------------------------------------------------------------------------------------------------------------------- |

用CA给server.csr签名


| 1 | openssl ca -in csrs/server1.csr -out certs/server1.crt -config openssl.cnf |
| - | -------------------------------------------------------------------------- |

查验server1.crt证书

| 1<br/>2 | openssl x509 -in certs/server1.crt -noout -text | less<br/>openssl x509 -in certs/server1.crt -noout -serial -subject |
| ----- | ------------------------------------------------------------------------------------------------------------------- |

openssl.cnf


| 1<br/>2<br/>3<br/>4<br/>5<br/>6<br/>7<br/>8<br/>9<br/>10<br/>11<br/>12<br/>13<br/>14<br/>15<br/>16<br/>17<br/>18<br/>19<br/>20<br/>21<br/>22 | [ ca ]<br/>default\_ca = TEST\_CA<br/><br/>[ TEST\_CA ]<br/>dir = .<br/>private\_key = \$dir/ca/root.key<br/>certificate = \$dir/ca/root.crt<br/>new\_certs\_dir = \$dir/newcerts<br/>certs = \$dir/certs<br/>database = \$dir/index.txt<br/>serial = \$dir/serial<br/>default\_md = sha256<br/>default\_days = 365<br/>policy = policy\_match<br/><br/>[ policy\_match ]<br/>countryName = match<br/>stateOrProvinceName = optional<br/>organizationName = match<br/>organizationalUnitName = optional<br/>commonName = supplied<br/>emailAddress = supplied |
| -------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

目录结构tree

| 1<br/>2<br/>3<br/>4<br/>5<br/>6<br/>7<br/>8<br/>9<br/>10<br/>11<br/>12<br/>13<br/>14<br/>15<br/>16<br/>17<br/>18<br/>19<br/>20<br/>21<br/>22 | .<br/>|-- ca<br/>| |-- root.crt<br/>| |-- root.csr<br/>| |-- root.key<br/>| `-- root.pem<br>\|-- certs<br>\|<span> </span>`-- server1.crt<br/>|-- csrs<br/>| `-- server1.csr<br>\|-- index.txt<br>\|-- index.txt.attr<br>\|-- index.txt.old<br>\|-- keys<br>\|<span> </span>`-- server1.key<br/>|-- newcerts<br/>| `-- 01.pem<br>\|-- openssl.cnf<br>\|-- serial<br>`-- serial.old<br/><br/>5 directories, 14 files |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

配置文件中提到的文件或目录都是手工创建,其中还需要


| 1 | echo '01' > serial |
| - | ------------------ |

配置文件中未提到的文件系自动生成

* index.txt.attr 签发证书时的属性, unique\_subject = yes; 当然也可以写到配置文件中
* index.txt.old 最近一次的index.txt
* serial.old 最近一次签发证书的serial number

#### 中间过程可能遇到的问题

error while loading serial number

serial需初始化一个值


| 1 | echo '01' > serial |
| - | ------------------ |

failed to update database
TXT\_DB error number 2


| 1<br/>2 | index.txt.attr<br/>unique\_subject = no |
| ------- | --------------------------------------- |

#### 证书吊销

openssl.conf增加一些配置(同步创建crls目录及echo ‘01’ > crls/crlnumber)


| 1<br/>2<br/>3 | crlnumber =\$dir/crls/crlnumber<br/>crl = \$dir/crls/certificate.crl<br/>default\_crl\_days = 30 |
| ------------- | ------------------------------------------------------------------------------------------------ |

吊销证书


| 1 | openssl ca -revoke certs/server1.crt -config openssl.cnf |
| - | -------------------------------------------------------- |

生成吊销证书列表


| 1 | openssl ca -gencrl -out crls/certificate.crl -config openssl.cnf |
| - | ---------------------------------------------------------------- |

查看吊销列表

| 1 | openssl crl -in crls/certificate.crl -noout -text | less |
| --- | ---------------------------------------------------------- |

CA发布CRL一般都会有周期性,所以客户端不能基于CRL实时检查某证书是否过期

现在一般通过OCSP(Online Certificate Status Protocol),在线证书状态协议

#### 证书扩展

生成待签名的server2.csr


| 1<br/>2 | openssl genrsa -des3 -out keys/server2.key 2048<br/>openssl req -new -key keys/server2.key -out csrs/server2.csr -subj "/C=CN/O=Test/OU=dev/CN=server2.test.com/emailAddress=ops@test.com" |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

增加openssl.cnf相关配置


| 1<br/>2<br/>3<br/>4<br/>5<br/>6<br/>7<br/>8<br/>9<br/>10<br/>11<br/>12<br/>13<br/>14<br/>15<br/>16<br/>17<br/>18<br/>19<br/>20<br/>21<br/>22<br/>23<br/>24<br/>25 | diff --git a/openssl.cnf b/openssl.cnf<br/>index 2bb77dd..d99eb1e 100644<br/>--- a/openssl.cnf<br/>+++ b/openssl.cnf<br/>@@ -14,7 +14,8 @@ default\_days = 365<br/>crlnumber = \$dir/crls/crlnumber<br/>crl = \$dir/crls/crl.pem<br/>default\_crl\_days = 30<br/>-policy = policy\_match<br/>+x509\_extensions = usr\_cert<br/>+policy = policy\_match<br/>-<br/>[ policy\_match ]<br/>countryName = match<br/>@@ -24,6 +25,13 @@ organizationalUnitName = optional<br/>commonName = supplied<br/>emailAddress = supplied<br/>-<br/>+[ usr\_cert ]<br/>+basicConstraints = CA:FALSE<br/>+#nsCertType = server<br/>+nsCertType = client, email<br/>+keyUsage = digitalSignature, keyEncipherment<br/>+nsComment = "Certificate Generated by Test CA"<br/>+ |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

如果想给一个Web站点签发,可按需添加


| 1<br/>2<br/>3 | extendedKeyUsage = serverAuth, clientAuth<br/>#subjectAltName = IP:192.168.7.1<br/>subjectAltName = DNS:\\\*.test.com DNS:test.com |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- |

用CA对server2.csr签名


| 1 | openssl ca -in csrs/server2.csr -out certs/server2.crt -config openssl.cnf |
| - | -------------------------------------------------------------------------- |

可以对比查看server2.crt和server1.crt的区别

| 1 | openssl x509 -in certs/server2.crt -noout -text | less |
| --- | -------------------------------------------------------- |

#### 二级CA

实际生产环境中,很少直接用RootCA直接签发业务证书,一般是二级或多级CA去签发

新创建一工作目录,并复用RootCA的目录结构和配置文件


| 1<br/>2 | mkdir /root/second\_ca\_test<br/>cd /root/second\_ca\_test |
| ------- | ---------------------------------------------------------- |

生成二级CA的key及其证书请求文件


| 1<br/>2 | openssl genrsa -des3 -out ca/second.key 2048<br/>openssl req -new -key ca/second.key -out ca/second.csr -subj "/C=CN/O=Test/OU=Test SecondCA/CN=Test SecondCA/emailAddress=ca@test.com" |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

使用RootCA签发二级CA前,RootCA的openssl.cnf配置文件需要更改

usr\_cert 只保留以下配置即可


| 1<br/>2 | [ usr\_cert ]<br/>basicConstraints = CA:FALSE |
| ------- | --------------------------------------------- |

签发二级CA: 遇到路径问题自行修改,因为’dir = .’没有使用绝对路径


| 1 | openssl ca -in ca/second.csr -out ca/second.crt -config ../openssl/openssl.cnf |
| - | ------------------------------------------------------------------------------ |

二级CA签发的证书一般需要合并,RootCA一般需导入或已内置(浏览器、系统)


| 1<br/>2<br/>3<br/>4<br/>5<br/>6<br/>7<br/>8<br/>9 | -----BEGIN CERTIFICATE-----<br/>server\_crt<br/>------END CERTIFICATE------<br/>-----BEGIN CERTIFICATE-----<br/>lower\_rt<br/>------END CERTIFICATE------<br/>-----BEGIN CERTIFICATE-----<br/>second\_crt<br/>------END CERTIFICATE------ |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

证书校验


| 1 | openssl verify -verbose -CAfile ... |
| - | ----------------------------------- |

#### 建议

* 提前规划,比如多少级CA,分别做什么
* 私钥一定要加密保存,养成良好习惯
* 根据实际情况,脚本化签发证书
* 按需对证书有效期进行监控并预警

#### 配置文件示例

openssl.cnf


| 1<br/>2<br/>3<br/>4<br/>5<br/>6<br/>7<br/>8<br/>9<br/>10<br/>11<br/>12<br/>13<br/>14<br/>15<br/>16<br/>17<br/>18<br/>19<br/>20<br/>21<br/>22<br/>23<br/>24<br/>25<br/>26<br/>27<br/>28<br/>29<br/>30<br/>31<br/>32<br/>33<br/>34<br/>35<br/>36<br/>37<br/>38<br/>39<br/>40<br/>41<br/>42<br/>43<br/>44<br/>45<br/>46<br/>47<br/>48<br/>49<br/>50<br/>51<br/>52<br/>53<br/>54<br/>55<br/>56<br/>57 | [ ca ]<br/>default\_ca = TEST\_CA<br/><br/>[ TEST\_CA ]<br/>dir = .<br/>private\_key = \$dir/ca/root.key<br/>certificate = \$dir/ca/root.crt<br/>new\_certs\_dir = \$dir/newcerts<br/>certs = \$dir/certs<br/>database = \$dir/index.txt<br/>serial = \$dir/serial<br/>default\_md = sha256<br/>default\_days = 365<br/>crlnumber = \$dir/crls/crlnumber<br/>crl = \$dir/crls/crl.pem<br/>default\_crl\_days = 30<br/>x509\_extensions = usr\_cert<br/>policy = policy\_match<br/><br/>[ policy\_match ]<br/>countryName = match<br/>stateOrProvinceName = optional<br/>organizationName = match<br/>organizationalUnitName = optional<br/>commonName = supplied<br/>emailAddress = supplied<br/><br/>[ usr\_cert ]<br/>basicConstraints = CA:FALSE<br/>#nsCertType = server<br/>nsCertType = client, email<br/>keyUsage = digitalSignature, keyEncipherment<br/>extendedKeyUsage = serverAuth, clientAuth<br/>#subjectAltName = IP:192.168.7.1<br/>subjectAltName = DNS:\\\*.test.com DNS:test.com<br/>nsComment = "Certificate Generated by Test CA"<br/><br/>[ req ]<br/>distinguished\_name = req\_distinguished\_name<br/><br/>[ req\_distinguished\_name ]<br/>countryName = Country Name (2 letter code)<br/>countryName\_default = CN<br/>countryName\_min = 2<br/>countryName\_max = 2<br/><br/>stateOrProvinceName = State or Province Name (full name)<br/>stateOrProvinceName\_default =<br/>localityName = Locality Name (eg, city)<br/>localityName\_default =<br/>0.organizationName = Organization Name (eg, company)<br/>0.organizationName\_default = Test<br/>organizationalUnitName = Organizational Unit Name (eg, section)<br/>organizationalUnitName\_default = Dev<br/>commonName = Common Name (e.g. server FQDN or YOUR name)<br/>emailAddress = Email Address<br/>commonName\_max = 64 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

#### 其它

去除key的密码


| 1 | openssl rsa -in keys/server1.key -out keys/server1\_nopass.key |
| - | -------------------------------------------------------------- |

生成公钥


| 1 | openssl rsa -in keys/server1\_nopass.key -pubout > server1.pub |
| - | -------------------------------------------------------------- |

#### 更多

`man x509`

`man x509v3_config`或’[https://www.openssl.org/docs/manmaster/man5/x509v3\_config.html'](https://www.openssl.org/docs/manmaster/man5/x509v3_config.html')

`man req`

...
