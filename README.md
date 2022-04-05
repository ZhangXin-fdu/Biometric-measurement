# Biometric-measurement



### 测试情况

**根据CryptoObject设置情况：**

- 设为null：直接绕过（hook authenticate函数，调用onAuthenticationSucceeded）
- 设为cipher/signature：
    - 当setUserAuthenticationRequired设为false时，可将hook authenticate函数得到的参数Crypto包装后传给成功回调，可使用密钥加解密/签名
    - 当setUserAuthenticationRequired设为true时，未经用户认证无法使用密钥，两种情况：
        - 在开启指纹前就可hook，直接hook setUserAuthenticationRequired，将其参数改为false，后续可使用密钥
        - 已开启指纹后，无法hook setUserAuthenticationRequired，可以通过hook DoFinal/update/sign等加解密/签名函数来使得操作时不出错，但无法使用密钥，若后续对结果有检查无法通过

总结：

- 对于回调中未检查加解密或签名结果的情况，可以通过hook相应的加解密或签名函数来绕过；

- 对于会检查结果的情况，无法使用密钥，只能通过在创建密钥时hook掉setUserAuthenticationRequired来绕过

    

**指纹变化敏感测试**

- 设置setUserAuthenticationRequired为true
    - 未设置setInvalidatedByBiometricEnrollment：指纹变化敏感
    - 设置setInvalidatedByBiometricEnrollment为false：指纹变化不敏感

总结：只要设置了setUserAuthenticationRequired为true，默认情况下就是新增指纹或删除所有指纹就密钥失效

