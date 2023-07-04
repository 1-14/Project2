# Project2

## 算法原理

![image](https://user-images.githubusercontent.com/104854836/181996882-f0f99b36-935c-4af5-90ea-11be7798cb26.jpg)

pollard rho算法并不是专门用来寻找哈希碰撞的算法，但是他的思路可以借鉴用于寻找哈希碰撞.当对一个值进行多次哈希时，就会发生如图中的现象，图中的H4和H10解释我们要寻找的一对碰撞。根据哈希函数的特性，我们知道总会有图中的现象出现。

## 代码思路

由于要寻找对初值的碰撞，我们首先用hash_buf将初值的一次哈希值存储下来，然后用hash_buf2存储二次哈希值，之后不断循环对hash_buf2中的值进行哈希，直到找到和一次哈希值相等的值，算法结束

## 关键代码展示

```cpp

// 在SM3哈希算法中查找碰撞
int sm3_rho_collide() {
    int hash_buf[8]; // 用于存储明文的哈希值的数组
    int hash_buf2[8]; // 用于存储中间步骤的哈希值的数组
    int hash_buf3[8]; // 用于存储碰撞信息的哈希值的数组
    cout << "待碰撞信息为：";
    char p[] = "202100180118";
    cout << p << endl;

    sm3(p, hash_buf); // 计算明文的哈希值
    cout << "待碰撞信息的哈希值为：" << endl;
    dump_buf((char*)hash_buf, 32); // 显示哈希值

    sm3(p, hash_buf2); // 再次计算明文的哈希值
    while (true) {
        memcpy(hash_buf3, hash_buf2, 32); // 复制中间步骤的哈希值

        sm3((char*)hash_buf2, hash_buf2); // 计算中间步骤的哈希值

        // 检查中间步骤的哈希值是否与原始哈希值相匹配
        if (!memcmp(hash_buf, hash_buf2, 2)) { //第三个参数2代表2个字节，即16比特
            cout << "碰撞到的信息为：" << endl;
            dump_buf((char*)hash_buf3, 32); // 显示碰撞信息
            cout << "碰撞到的信息的哈希值为：" << endl;
            dump_buf((char*)hash_buf2, 32); // 显示碰撞信息的哈希值
            break;
        }
    }

    cout << "可以碰撞" << 2*8 << "位信息" << endl; // 输出可碰撞信息的大小

    return 0;
}

```

## 结果展示

8比特的碰撞运行时间很短，仅有0.009s

![image](https://github.com/1-14/Project2/blob/main/2.png)

16比特的碰撞运行时间是8比特的80倍以上，0.769s

![image](https://github.com/1-14/Project2/blob/main/1.png)

上述结果为多次实验后选取的有代表性的结果

## 结果分析

由于在密码学引论课上多次接触过 pollard rho算法，所以实现思路并不难找，但是代码的速度属实不堪入目，当碰撞长度提升到24比特时，运行时间简直是大跨步飞跃，只运行了一次，就不再展示了，代码写完后也想到还有可以改进的地方，可以将hash_buf中的值也进行哈希，逐步增大差距寻找碰撞，思路写下来留待以后修改。

