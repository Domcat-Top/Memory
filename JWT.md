## JWT

> 概念啥的我就不写了，感兴趣自行百度

### 1. 结构

- x.y.z

- x：头，令牌的**类型**和**所使用的签名算法**

  ```json
  {
      # 加密算法类型  
      "alg": "HS256",
      "typ": "JWT",
  }
  ```

- y：payload，有效负载，其中包含声明，声明是有关实体（也就是用户）和其他数据的声明。同样的，它会使用Base64编码组成JWT的第二部分

  - 放一些用户的信息，比如昵称、头像、权限啥的（建议放一些不敏感的信息）

  ```json
  {
      “sub”: "1234567890",
      "name": "Tom",
      "admin": "true",
  }
  ```

- z：签名，其实就是你的一个乱码，随便输入你的规则即可，为了防止token被破解了

> 先验证算法、再验证签名，都通过才可以









