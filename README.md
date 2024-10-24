# 2024090914022-赖伊卡-CS-04
## 理解大数运算
1. 因为当数据大于数据类型的最大范围是，就无法存储已经运算这些数据了。而大数运算则巧妙的一位一位的加，很大的数转化成了一位一位的小的数相加，避免了这个问题。并且用字符数组表示大叔更加直接，比起二进制或者其他除了十的进制。
2. 进位：预先定义一个整型变量a=0，从对低位开始向高位依次每一位数相加如果结果小于十则把a赋值为0，如果大于等于十，就让结果%10成为新的结果，同时把a赋值为1。高一位相加时两位相加之外还要加上a，以此类推，在最高位时，如果a=1，就在结果前面多加个1，反之，不做处理。

   借位：与进位类似，只不过更高一位的处理为多减去1.
3.提取负号，再根据负号个数，决定实际运算类型。比如说，(-1)+2实际可以看为2-1。然后就可以类似正数处理。
## 初步实现大数运算
### 尝试储存大数
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

#define MAX 128

int main() 
{
    char largeNumber[MAX + 1];
    printf("请输入一个数字：");
    scanf("%s", largeNumber);

    printf("输入的数字为：%s\n", largeNumber);

    return 0;
}
```
![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20183334.png)

### 实现大数加法
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 128

char* add(char* num1,char* num2)
{
    int len1 = strlen(num1);
    int len2 = strlen(num2);
    int maxLen = (len1 > len2)? len1 : len2;
    int carry = 0;
    char* result = (char*)malloc((maxLen + 1) * sizeof(char));
    int i = len1 - 1;
    int j = len2 - 1;
    int k = 0;
    while (i >= 0 || j >= 0 || carry > 0) 
    {
        int digit1 = (i >= 0)? num1[i] - '0' : 0;
        int digit2 = (j >= 0)? num2[j] - '0' : 0;
        int sum = digit1 + digit2 + carry;
        carry = sum / 10;
        result[k] = sum % 10 + '0';
        k++;
        i--;
        j--;
    }
    result[k] = '\0';
    int left= 0;
    int right = k - 1;
    while (left < right) 
    {
        char temp = result[left];
        result[left] = result[right];
        result[right] = temp;
        left++;
        right--;
    }
    return result;
}

int main() 
{
    char largeNumber1[MAX + 1];
    char largeNumber2[MAX + 1];
    printf("请输入第一个数字：");
    scanf("%s", largeNumber1);
    printf("请输入第二个数字：");
    scanf("%s", largeNumber2);
    char* sum = add(largeNumber1, largeNumber2);
    printf("两数之和为：%s\n", sum);
    free(sum);
    return 0;
}
```

![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20184340.png)


