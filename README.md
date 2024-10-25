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
#include <string.h>

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
    char a[MAX + 1];
    char b[MAX + 1];
    printf("请输入第一个数字：");
    scanf("%s", a);
    printf("请输入第二个数字：");
    scanf("%s", b);
    char* sum = add(a, b);
    printf("两数之和为：%s\n", sum);
    free(sum);
    return 0;
}
```

![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20184340.png)

### 实现大数加法（处理负数）
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 128

int If(char* num) 
{
    return num[0] == '-';
}

char* removefuhao(char* num) 
{
    char* a= (char*)malloc((strlen(num)) * sizeof(char));
    strcpy(a, num + 1);
    return a;
}

char* addfuhao(char* result) 
{
    int len = strlen(result);
    char* a = (char*)malloc((len + 1） * sizeof(char));
    a[0] = '-';
    strcpy(a+ 1, result);
    return a;
}

int compareabs(char* num1,char* num2) 
{
    int if1 = If(num1);
    int if2 = If(num2);
    if (if1 &&!if2) 
    return -1;
    if (!if1 && if2) 
    return 1;
    if (if1&&if2) 
    {
        char* abs1 = removefuhao((char*)num1);
        char* abs2 = removefuhao((char*)num2);
        int x = strcmp(abs1, abs2);
        free(abs1);
        free(abs2);
        return x == 0? 0 : (x < 0)? -1 : 1;
    } 
    else 
    {
        int x=strcmp(num1, num2);
        return x==0?0 : (x<0)? 1 : -1;
    }
}

char* add(char* num1, char* num2) 
{
    int if1 = If(num1);
    int if2 = If(num2);
    if (if1 && if2) 
    {
        char* abs1 = removefuhao(num1);
        char* abs2 = removefuhao(num2);
        char* sum = add(abs1, abs2);
        free(abs1);
        free(abs2);
        return addfuhao(sum);
    } 
    else if (if1) 
    {
        char* abs1 = removefuhao(num1);
        int x = compareabs(abs1, num2);
        if (x == 0) 
        return "0";
        if (x == 1) 
        {
            char* ans = add(num2, abs1);
            addfuhao(ans);
            return ans;
        } 
        else 
        {
            char* ans = add(abs1, num2);
            return ans;
        }
    } 
    else if (if2) 
    {
        char* abs2 = removefuhao(num2);
        int x = compareabs(num1, abs2);
        if (x == 0) 
        return "0";
        if (x == 1) 
        {
            char* ans = add(num1, abs2);
            return ans;
        } 
        else 
        {
            char* ans = add(abs2, num1);
            addfuhao(ans);
            return ans;
        }
    } 
    else 
    {
        int len1 = strlen(num1);
        int len2 = strlen(num2);
        int maxLen = (len1 > len2)? len1 : len2;
        int carry = 0;
        char* result = (char*)malloc((maxLen + 2) * sizeof(char));
        int i = len1 - 1;
        int j = len2 - 1;
        int k = 0;
        while (i >= 0 || j >= 0 || carry > 0) 
        {
            int digit1 = (i >= 0)? num1[i] - '0' : 0;
            int digit2 = (j >= 0)? num2[j] - '0' : 0;
            int sum = digit1 + digit2 + carry;
            carry = sum / 10;
            result[k++] = sum % 10 + '0';
            i--;
            j--;
        }
        result[k] = '\0';
        int left = 0;
        int right = k - 1;
        while (left < right) {
            char temp = result[left];
            result[left] = result[right];
            result[right] = temp;
            left++;
            right--;
        }
        return result;
    }
}

int main() 
{
    char a[MAX];
    char b[MAX];
    printf("请输入第一个数字：");
    scanf("%s", a);
    printf("请输入第二个数字：");
    scanf("%s", b);
    char* x = add(a, b);
    printf("两数之和为：%s\n", x);
    free(x);
    return 0;
}
```

![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20191705.png)

### 从表达式中提取操作数和操作符
```c
#include <stdio.h>

#define MAX 100

void get(char *in, char *front, char *behind, char *mid) 
{
    int i = 0, j = 0;

    while (in[i]!= '+' && in[i]!= '-' && in[i]!= '*' && in[i]!= '/') 
    {
        front[j++] = in[i++];
    }
    front[j] = '\0';

    mid[0] = in[i];
    mid[1] = '\0';

    j = 0;
    i++;
    while (in[i]!= '\0') 
    {
        behind[j++] = in[i++];
    }
    behind[j] = '\0';
}

int main() 
{
    char in[MAX];
    char front[MAX];
    char behind[MAX];
    char mid[2];

    printf("请输入表达式: ");
    scanf("%s", in);
    get(in, front,behind, mid);
    printf("操作数1: %s\n", front);
    printf("操作数2: %s\n", behind);
    printf("操作符: %s\n", mid);
    return 0;
}
```

![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20212304.png)

### 封装大数四则运算
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 128

int compare(char* num1, char* num2) 
{
    int len1 = strlen(num1);
    int len2 = strlen(num2);
    if (len1 < len2) return -1;
    if (len1 > len2) return 1;
    return strcmp(num1, num2);
}


char* jia(char* num1, char* num2) 
{
    int len1 = strlen(num1);
    int len2 = strlen(num2);
    int maxLen = (len1 > len2)? len1 : len2;
    int carry = 0;
    char* result = (char*)malloc((maxLen + 2) * sizeof(char));
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
    int left = 0;
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

char* jian(char* num1, char* num2) 
{
    int sign = 1;
    if (compare(num1, num2) < 0) 
    {
        char* temp = num1;
        num1 = num2;
        num2 = temp;
        sign = -1;
    }
    int len1 = strlen(num1);
    int len2 = strlen(num2);
    int borrow = 0;
    char* result = (char*)malloc((len1 + 1) * sizeof(char));
    int i = len1 - 1;
    int j = len2 - 1;
    int k = 0;
    while (i >= 0 || j >= 0) 
    {
        int digit1 = (i >= 0)? num1[i] - '0' : 0;
        int digit2 = (j >= 0)? num2[j] - '0' : 0;
        int x = digit1 - digit2 - borrow;
        if (x < 0) 
        {
            x += 10;
            borrow = 1;
        } 
        else 
        {
            borrow = 0;
        }
        result[k] = x + '0';
        k++;
        i--;
        j--;
    }

    while (k > 1 && result[k - 1] == '0')
    k--;
    result[k] = '\0';
    if (sign == -1) 
    {
        char* y = (char*)malloc((k + 2) * sizeof(char));
        y[0] = '-';
        strcpy(y + 1, result);
        free(result);
        return y;
    }
    return result;
}

char* cheng(char* num1, char* num2) 
{
    int len1 = strlen(num1);
    int len2 = strlen(num2);
    char* result = (char*)malloc((len1 + len2 + 1) * sizeof(char));
    for (int i = 0; i < len1 + len2; i++) 
    {
    result[i] = '0';
    result[len1 + len2] = '\0';
    }
    for (int i = len1 - 1; i >= 0; i--) 
    {
        int carry = 0;
        for (int j = len2 - 1; j >= 0; j--) 
        {
            int x = num1[i] - '0';
            int y = num2[j] - '0';
            int sum = (result[i + j + 1] - '0') + x * y + carry;
            carry = sum / 10;
            result[i + j + 1] = sum % 10 + '0';
        }
        if (carry > 0)
        result[i] = carry + '0';
    }

    int k = 0;
    while (k < len1 + len2 && result[k] == '0')
    k++;
    if (k == len1 + len2) 
    return "0";
    char* z = (char*)malloc((len1 + len2 - k + 1) * sizeof(char));
    strcpy(z, result + k);
    free(result);
    return 
    z;
}

char* chu(char* num1, char* num2) 
{
    if (strcmp(num2, "0") == 0) 
    {
        printf("错误\n");
        return NULL;
    }
    int sign = 1;

    if (compare(num1, num2) < 0) 
    {
        sign = -1;
        return "0";
    } 
    else if (compare(num1, num2) == 0)
    return "1";
    char* shang = (char*)malloc((MAX + 1) * sizeof(char));
    char* remainder = (char*)malloc((MAX + 1) * sizeof(char));
    strcpy(remainder, num1);
    int quotientIndex = 0;
    while (compare(remainder, num2) >= 0) 
    {
        char* subResult = jian(remainder, num2);
        free(remainder);
        remainder = subResult;
        shang[quotientIndex++] = '1';
    }
    shang[quotientIndex] = '\0';
    if (sign == -1) 
    {
        char* newQuotient = (char*)malloc((quotientIndex + 2) * sizeof(char));
        newQuotient[0] = '-';
        strcpy(newQuotient + 1, quotient);
        free(quotient);
        return newQuotient;
    }
    return quotient;
}

int main() 
{
    char first[MAX + 1];
    char second[MAX + 1];
    char a;
    printf("请输入第一个数字：");
    scanf("%s", first);
    printf("请输入运算符（+、-、*、/）：");
    scanf(" %c", &a);
    printf("请输入第二个数字：");
    scanf("%s", second);
    char* result;
    switch (a) 
    {
    case '+':
        result = jia(first, second);
        break;
    case '-':
        result = jian(first, second);
        break;
    case '*':
        result = cheng(first, second);
        break;
    case '/':
        result = chu(first, second);
        break;
    default:
        printf("错误\n");
        return 1;
    }
    printf("结果为：%s\n", result);
    free(result);
    return 0;
}
```

![image](https://github.com/Taylor-Lai/2024090914022-laiyika-CS-04/blob/main/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202024-10-24%20212733.png)

