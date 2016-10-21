��һ���� ��������RSA��js���ļ������Ե��������� http://www.ohdave.com/rsa/��

�ڶ����� ��ȡ��Կ

 Ҫ���ɻ��֤���������Կ���ļ�
	1 openssl genrsa -des3 -out server.pem 1024
	2 openssl req -new -key server.pem -out server.csr
	3 openssl rsa -in server.pem -out server.pem
	
	ʹ�����������ͻᴴ��һ��֤�����룬�������ǻ�Ҫ��������ҡ���֯����������Ϣ�����ǲ���Ҫ������֤����Ч������
	��Ϊ֤����Ч������CA��֤���ĸ����ǵģ�Ȼ�����ǻ��������ɺõ�cert.csr��Certificate Signing Request (CSR)��֤��ǩ�����룩
	����CA��֤���ġ�CA��֤����ͨ���󣬻ᷴ����ͨ�����ʼ���������֤����Ϣ���ٵ��뼴�ɡ�

	  ���������ɵ��ļ������ύ��CA�����ɻ�ȡ֤�飻������������:
	  
	  openssl x509 -req -days 365 -in server.csr -signkey server.pem -out server.crt
 

     ��ȡʮ�����Ƶ���Կ
	 
	 ��������ASN.1������ģ����Կ�����openssl�������Կ�ļ���key��pem����ȡ��Կ
	 
	 1 openssl asn1parse -out temp.ans -i -inform PEM < server.pem >server.pem16.log
	 
	 �����ͼ1��

	




	
	 =====================================================================================================================
	 4. javascript ���ܴ���
function rsa_pwd(content){

//ʮ�����ƹ�Կ 
var rsa_n = "BC6539FEAD9E086D02E1268FF36E46A2843797559D15BE09....";

setMaxDigits(131);

 //131 => n��ʮ������λ��/2+3 

var key = new RSAKeyPair("10001", '', rsa_n); //10001 => e��ʮ������ 

content_rsa = encryptedString(key, content);

//��֧�ֺ��� 

return content_rsa;

 }
 5. php ����/���ܴ���

<?php

/** * ��Կ����

 * * @param string ����

 * @param string ֤���ļ���.crt��

 * @return string ���ģ�base64���룩

 */

function publickey_encodeing($sourcestr, $fileName) {

      $key_content = file_get_contents($fileName);

      $pubkeyid = openssl_get_publickey($key_content);

      if (openssl_public_encrypt($sourcestr, $crypttext, $pubkeyid)) {

            return base64_encode("".$crypttext);

       }

}

/** * ˽Կ����

* * @param string ���ģ������Ƹ�ʽ��base64���룩

* @param string ��Կ�ļ���.pem / .key��

* @param string �����Ƿ���Դ��JS��RSA����

* @return string ����
*/

function privatekey_decodeing($crypttext, $fileName, $fromjs = FALSE) {

          $key_content = file_get_contents($fileName);

          $prikeyid = openssl_get_privatekey($key_content);

          $crypttext = base64_decode($crypttext);

          $padding = $fromjs ? OPENSSL_NO_PADDING : OPENSSL_PKCS1_PADDING;

          if (openssl_private_decrypt($crypttext, $sourcestr, $prikeyid, $padding)) {

                     return $fromjs ? rtrim(strrev($sourcestr)) : "".$sourcestr;

           }

           return '';

}

 ?>
6. ���Դ���

//JS->PHP ���� $_POST['password']��js���ܺ����Ϣ

$txt_en = $_POST['password'];

$txt_en = base64_encode(pack("H*", $txt_en));

$file = 'ssl/server.pem';

$txt_de = privatekey_decodeing($txt_en, $file, TRUE);

var_dump($txt_de);

//PHP->PHP ����  $data = "����:1a2b3c";

$config = Core::getInstance()->config;

$file1 = 'ssl/server.crt';

$file2 = 'ssl/server.pem';

$a = publickey_encodeing($data, $file1);

$b = privatekey_decodeing($a, $file2);

var_dump($b);
7. ��ע��
PHP��openssl��չ��˽Կ���ܺ���ֻ֧��С���ݣ�����ʱ117�ֽڣ�����ʱ128�ֽڡ�����Ȼ���Լ�ѭ�����ܺ�ϲ���

SSL����Ҳֻ����RSA��������Կ���ܣ����ݼ�����������������ܵ���Կ���жԳƼ��ܣ��Ա�֤�ٶȡ������򲻿ɽ������ڴ����������ܣ�

8. �����������ŵ�
1����ȫ�Ըߡ����ڷǶԳƵ�RSA�㷨�������ݣ�ֻҪ��˽Կ������¶��ǰ���£���Կ�����㹻������ʱ���ڻ������޷��ƽ�ġ�

2��ʹ�÷��㡣ǰ��ʹ���ֳɵ�JS����ʵ�ּ��ܣ�PHP�����ֱ��ʹ���ֳɵ�openssl��չ��������RSA��PHPԴ��ʵ�ֻ��Լ�������չ��

3���ٶȿ��ס�����RSA�����㷨�൱���ӣ����ò�������PHP����չ��ʵ�֣�Ч���ϱ����ϵ�PHP����Ҫ�����ࡣ

4��������������Կ��ֱ�Ӵ�linux��openssl�������ɵ�֤���л�ȡ����������������Կ���ɹ��ߣ�Ҳ������������������HTTPS��