---
title: AES base64
date: 2019-03-21 22:51:52
tags:
---

## sample code

```
/**
https://aesencryption.net/
https://www.clickssl.net/blog/128-bit-ssl-encryption-vs-256-bit-ssl-encryption
https://www.quora.com/Is-AES256-more-secure-than-AES128-Whats-the-different
https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
https://stackoverflow.com/questions/273396/aes-encryption-what-are-public-and-private-keys
*/

import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;

import java.nio.charset.StandardCharsets;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URLDecoder;
import java.net.URLEncoder;
import java.security.InvalidKeyException;
import java.security.Key;
import java.security.NoSuchAlgorithmException;

public class AESEncryptionUtil {

// http://www.allkeysgenerator.com/Random/Security-Encryption-Key-Generator.aspx
 private static String key = “customized key”;
 public static BASE64Encoder enc = new BASE64Encoder();
 public static BASE64Decoder dec = new BASE64Decoder();


 // eccrypt by AES, and encode by base64 to transfer by http
 public static String encrypt(String clearText) throws Exception {
 String encodedEncryptText = “”;
 try {
 Key aesKey = new SecretKeySpec(key.getBytes(), “AES”);
 Cipher cipher = Cipher.getInstance(“AES”);

cipher.init(Cipher.ENCRYPT_MODE, aesKey);
 byte[] encrypted = cipher.doFinal(clearText.getBytes(“UTF-8”));

encodedEncryptText = enc.encode(encrypted);

} catch (NoSuchAlgorithmException e) {
 throw new Exception(e.getMessage());
 } catch (NoSuchPaddingException e) {
 throw new Exception(e.getMessage());
 } catch (BadPaddingException e) {
 throw new Exception(e.getMessage());
 } catch (UnsupportedEncodingException e) {
 throw new Exception(e.getMessage());
 } catch (IllegalBlockSizeException e) {
 throw new Exception(e.getMessage());
 } catch (InvalidKeyException e) {
 throw new Exception(e.getMessage());
 }
 return encodedEncryptText;

}

// encode by base64, decrypt by AES
 public static String decrypt(String encodedEncryptText) throws Exception {
 String decrypted = “”;
 try {
 Key aesKey = new SecretKeySpec(key.getBytes(), “AES”);

Cipher cipher = Cipher.getInstance(“AES”);
 cipher.init(Cipher.DECRYPT_MODE, aesKey);

byte[] decodeBufferArray = dec.decodeBuffer(encodedEncryptText);

decrypted = new String(cipher.doFinal(decodeBufferArray), StandardCharsets.UTF_8);

} catch (NoSuchAlgorithmException e) {
 throw new Exception(e.getMessage());
 } catch (NoSuchPaddingException e) {
 throw new Exception(e.getMessage());
 } catch (BadPaddingException e) {
 throw new Exception(e.getMessage());
 } catch (IllegalBlockSizeException e) {
 throw new Exception(e.getMessage());
 } catch (InvalidKeyException e) {
 throw new Exception(e.getMessage());
 } catch (IOException e) {
 throw new Exception(e.getMessage());
 }
 return decrypted;

}

public static void main(String[] args) {
 String a = null;
 try {
 a = encrypt(“mengxu1128@gmail.com”);
 } catch (Exception e) {
 e.printStackTrace();
 }
 System.out.println(a);

String b = null;
 try {
 b = decrypt(a);

} catch (Exception e) {
 e.printStackTrace();
 }
 System.out.println(b);
 }
}


```