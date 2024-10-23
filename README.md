# 2024090914015---Glimmer-CS-MEDIUM-01
微光招新题cs4
## Part1
1. 为什么使用字符数组来表示大数？
避免整数溢出：
许多编程语言中的整数类型（如 int, long, long long）都有固定的范围。如果数值超过这个范围，就会发生溢出。
字符数组可以动态分配内存，理论上可以表示任意大小的数字，只要内存足够。
逐位操作：
大数运算通常需要逐位处理，字符数组可以方便地访问和修改每一位数字。
字符数组可以看作是一种字符串形式的数字表示，便于逐位操作和算法实现。
易于实现算法：
大数运算的常见算法（如加法、减法、乘法、除法）都是基于逐位操作的，使用字符数组可以更直观地实现这些算法。
2. 如何处理大数运算中的进位和借位问题？
加法
逐位相加并处理进位：
从最低位开始逐位相加。
如果某一位的和大于等于10，则需要进位。
进位值加到下一位的和中。
减法
逐位相减并处理借位：
从最低位开始逐位相减。
如果某一位的差小于0，则需要借位。
借位值减去10，并将借位标志传递给上一位。
3. 如何处理负数？
符号标记：
可以用一个额外的变量或字符来标记数字的正负。
对于负数，可以在最前面添加一个负号 '-'。
加法和减法：
对于负数的加法和减法，需要根据符号进行相应的处理。
如果两个数的符号相同，可以将它们转换成正数进行加法或减法，然后根据符号确定最终结果。
如果两个数的符号不同，可以将其中一个数视为另一个数的相反数进行加法或减法。
## Part2
### step1
1. 运行结果
   [![pAantGq.png](https://s21.ax1x.com/2024/10/20/pAantGq.png)](https://imgse.com/i/pAantGq)
2. 代码
```c
#include <stdio.h>  
#include <string.h>  
#include <stdlib.h>  
#include<math.h>
int main()
{
    char num[1000];
    scanf("%s",num);
    printf("%s",num);
}
```
### step2
1. 运行结果
[![pAan3ZQ.png](https://s21.ax1x.com/2024/10/20/pAan3ZQ.png)](https://imgse.com/i/pAan3ZQ)
2. 代码：
```c
#include <stdio.h>  
#include <string.h>  
#include <stdlib.h>  
#include<math.h>
// 辅助函数：反转字符串
void reverse(char *str) {
    int len = strlen(str);
    for (int i = 0; i < len / 2; i++) {
        char temp = str[i];
        str[i] = str[len - i - 1];
        str[len - i -1] = temp;
    }
}
// 辅助函数：将两个字符表示的数字相加，并返回结果和进位
void add_digits(char a, char b, char *cur, int *carry) {
    int sum = (a - '0') + (b - '0') + *carry;//在这个位上的两个数加上进位的数就是当前位的答案
    *carry = sum / 10;
    *cur  = (sum % 10) + '0';
}
// 大数相加  
char* add(char *num1, char *num2) {  
    int len1 = strlen(num1),len2 = strlen(num2),carry=0;
    // 反转字符串以便从低位开始相加
    reverse(num1);
    reverse(num2);

    // 分配结果字符串空间，这里+2是因为考虑到进位可能增加一位
    char *result = (char *)malloc(sizeof(char) * (fmax(len1, len2) + 2));

    result[0] = '\0';// 初始化为空字符串
    for (int i=0;i < len1 || i < len2 || carry;i++) {
        char digit1 = (i < len1) ? num1[i] : '0',digit2 = (i < len2) ? num2[i] : '0';
        char current;
        add_digits(digit1, digit2, &current, &carry);
        // 将结果字符追加到结果字符串
        int result_len = strlen(result);
        result[result_len] = current;
        result[result_len + 1] = '\0';//永远让他的最后一位是‘\0’，俗称给字符串关个门
    }
    if(carry) result[strlen(result)]=carry;
    result[strlen(result)]='\0';
    // 反转结果字符串以得到正确的顺序
    reverse(result);
    return result;
}
int main() {
    char num1[1000], num2[1000];
    scanf("%s", num1);
    scanf("%s", num2);
    char *result = add(num1, num2);
    printf("%s", result);
    free(result);
    return 0;  
}
```
### step3
1. 运行结果
[![pAa0f6U.png](https://s21.ax1x.com/2024/10/20/pAa0f6U.png)](https://imgse.com/i/pAa0f6U)
2. 代码
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <stdbool.h>
#include<math.h>
// 反转字符串  
void reverse(char *str) {  
    int len = strlen(str);  
    for (int i = 0; i < len / 2; i++) {  
        char temp = str[i];  
        str[i] = str[len - 1 - i];  
        str[len - 1 - i] = temp;  
    }  
}
//比较这两个数(已逆序的)的大小
int compare(const char* num1, const char* num2) {  
    int len1 = strlen(num1),len2 = strlen(num2);
    if (len1 > len2) return 1;
    else if(len1 < len2) return 0;
    // 比较每一位  
    for (int i = 0; i < len1; i++) { 
        if (num1[len1 - 1 - i] > num2[len2 - 1 - i]) return 1;
        else if (num1[len1 - 1 - i] < num2[len2 - 1 - i]) return 0;
    }
    return 2;
}
// 去除大数字符串中的前导零  
char* removezeros(const char* num)
{  
    int len = strlen(num),start = 0;  
    // 找到第一个非零字符  
    while (start < len && num[start] == '0') {  
        start++;  
    }

    // 如果整个字符串都是零，则返回一个表示零的字符串  
    if (start == len) {  
        char* zeroStr = (char*)malloc(2 * sizeof(char));  
        zeroStr[0] = '0';  
        zeroStr[1] = '\0';  
        return zeroStr;  
    }  
  
    // 分配内存并复制非零部分到新字符串  
    char* re= (char*)malloc((len - start + 1) * sizeof(char));  
    strncpy(re, num + start, len - start);  
    re[len - start] = '\0';
    return re;  
}
//大数相减  
void sub(char *num1, char *num2, char *result) {  
    bool neg1 = (num1[0] == '-'),neg2 = (num2[0] == '-');
    // 去除符号并反转字符串
    char n1[1000], n2[1000];
    strcpy(n1, neg1 ? num1 + 1 : num1);//如果第一位是“-”就将num指针指向下一位
    strcpy(n2, neg2 ? num2 + 1 : num2);
    reverse(n1);
    reverse(n2);
    int com=compare(n1,n2);
    if(com==2&&neg1!=neg2)
    {
        result[0]='0';
        result[1]='\0';
        return ;
    }
    // 初始化结果数组和进位
    int carry = 0, len1 = strlen(n1), len2 = strlen(n2), max_len = len1 > len2 ? len1 : len2;
    char res[2000] = {0};

    // 按位相加
    if(neg1==neg2)//同号
    {
        for (int i = 0; i < max_len; i++) {
            int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
            int sum = digit1 + digit2 + carry;
            res[i] = (sum % 10) + '0';
            carry = sum / 10;
        }
    }
    else
    {
        if(com)
        {
            for (int i = 0; i < max_len; i++) {
                int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
                int sum = digit1 - digit2 + carry;
                if(sum>=0)
                    carry = sum / 10;
                else if(sum<0)
                {
                    carry=-1;
                    sum+=10;
                }
                res[i] = (sum % 10) + '0';
            }
        }
        else
        {
            for (int i = 0; i < max_len; i++) {
                int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
                int sum = digit2 - digit1 + carry;
                if(sum>=0) carry = sum / 10;
                else
                {
                    carry=-1;
                    sum+=10;
                }
                res[i] = (sum % 10) + '0';
            }
        }
    }
    
    // 处理最后一位进位  
    if (carry) {  
        res[max_len] = carry + '0';  
        res[++max_len]='\0';  
    }  
      
    // 反转结果并添加符号
    reverse(res);
    if ((neg1 && neg2)||(neg1==1&&com==1)||(neg2==1&&com==0)) {
        result[0] = '-';  
        strcpy(result + 1, res);  
    } else {  
        strcpy(result, res);  
    }  
}
int main()
{
    char num1[1000], num2[1000], result[2000];
    scanf("%s", num1);
    scanf("%s", num2);
    sub(num1, num2, result);
    printf("%s", result);
    return 0;  
}
```
## Part3
1. 运行结果
[![pAaD2sU.png](https://s21.ax1x.com/2024/10/20/pAaD2sU.png)](https://imgse.com/i/pAaD2sU)
2. 代码
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <stdbool.h>
#include<math.h>
char num1[1000],num2[1000],result[1000000],symbol;
void tiqu(char *str)
{
    int i=0,j=0;
    for(i=0;(str[i]!=' ')&&(str[i]!='+')&&(str[i]!='*')&&(str[i]!='/');i++)
    {
        if( (str[i]=='-') && (i!=0) ) break;
        num1[i]=str[i];
    }
    while(1)
    {
        if(str[i]=='+'||str[i]=='-'||str[i]=='*'||str[i]=='/')
        {
            symbol=str[i];
            break;
        }
        i++;
    }
    while(1)
    {
        i++;
        if( (str[i]!='(') && (str[i]!=')') && (str[i]!=' ') )
        {
            num2[j]=str[i];
            j++;
        }
        if((str[i]==')')||(!str[i])) break;
    }
}
int main()
{
    char str[3000];
    fgets(str, sizeof(str), stdin);
    tiqu(str);
    printf("操作数1:%s\n",num1);
    printf("操作数2:%s\n",num2);
    printf("操作符: %c\n",symbol);
    return 0;
}
```
## Part4
1. 运行结果
[![pAdBIyT.png](https://s21.ax1x.com/2024/10/23/pAdBIyT.png)](https://imgse.com/i/pAdBIyT)
[![pAdBfWq.png](https://s21.ax1x.com/2024/10/23/pAdBfWq.png)](https://imgse.com/i/pAdBfWq)
[![pAdB5lV.png](https://s21.ax1x.com/2024/10/23/pAdB5lV.png)](https://imgse.com/i/pAdB5lV)
[![pAdB4S0.png](https://s21.ax1x.com/2024/10/23/pAdB4S0.png)](https://imgse.com/i/pAdB4S0)
2. 代码（唉纯手敲代码，因为某个小错误卡了两天半呜呜呜呜呜，快难受死了）
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <stdbool.h>
#include<math.h>
char num1[1000],num2[1000],symbol,n1[1000], n2[1000];
bool neg1,neg2;
void tiqu(char *str)//提取两个数和操作符
{
    int i=0,j=0;
    for(i=0;(str[i]!=' ')&&(str[i]!='+')&&(str[i]!='*')&&(str[i]!='/');i++)
    {
        if( (str[i]=='-') && (i!=0) ) break;
        num1[i]=str[i];
    }
    while(1)
    {
        if(str[i]=='+'||str[i]=='-'||str[i]=='*'||str[i]=='/')
        {
            symbol=str[i];
            break;
        }
        i++;
    }
    while(1)
    {
        i++;
        if( (str[i]!='(') && (str[i]!=')') && (str[i]!=' ') && (str[i]!='\n'))
        {
            num2[j]=str[i];
            j++;
        }
        if((str[i]==')')||(!str[i])) break;
    }
}

// 反转字符串  
void reverse(char *str) {
    int len = strlen(str);
    for (int i = 0; i < len / 2; i++) {
        char temp = str[i];
        str[i] = str[len - 1 - i];
        str[len - 1 - i] = temp;
    }  
}

//比较这两个数(已逆序的)的大小
int compare(char* num1, char* num2) {  
    int len1 = strlen(num1),len2 = strlen(num2);
    if (len1 > len2) return 1;
    else if(len1 < len2) return 0;
    // 比较每一位  
    for (int i = 0; i < len1; i++) { 
        if (num1[len1 - 1 - i] > num2[len2 - 1 - i]) return 1;
        else if (num1[len1 - 1 - i] < num2[len2 - 1 - i]) return 0;
    }
    return 2;
}

// 去除大数字符串中的前导零  
void removezeros(char* num)
{  
    int len = strlen(num),start = 0;  
    // 找到第一个非零字符  
    while (start < len && num[start] == '0') {  
        start++;  
    }

    // 如果整个字符串都是零，则返回一个表示零的字符串  
    if (start == len) {
        num[0] = '0';  
        num[1] = '\0';
        return;
    }
    // 分配内存并复制非零部分到新字符串  
    char* re= (char*)malloc((len - start + 1) * sizeof(char));  
    strncpy(re, num + start, len - start);
    re[len - start] = '\0';
    strcpy(num,re);
}

//大数相加减  
void add(char *n1, char *n2, char *result) {
    // 反转字符串
    reverse(n1);
    reverse(n2);
    int com=compare(n1,n2);
    if(com==2&&neg1!=neg2)
    {
        result[0]='0';
        result[1]='\0';
        return ;
    }
    // 初始化结果数组和进位
    int carry = 0, len1 = strlen(n1), len2 = strlen(n2), max_len = len1 > len2 ? len1 : len2;
    char res[3000] = {0};

    // 按位相加
    if(neg1==neg2)//同号
    {
        for (int i = 0; i < max_len; i++) {
            int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
            int sum = digit1 + digit2 + carry;
            res[i] = (sum % 10) + '0';
            carry = sum / 10;
        }
    }
    else
    {
        if(com)
        {
            for (int i = 0; i < max_len; i++) {
                int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
                int sum = digit1 - digit2 + carry;
                if(sum>=0)
                    carry = sum / 10;
                else if(sum<0)
                {
                    carry=-1;
                    sum+=10;
                }
                res[i] = (sum % 10) + '0';
            }
        }
        else
        {
            for (int i = 0; i < max_len; i++) {
                int digit1 = (i < len1) ? n1[i] - '0' : 0,digit2 = (i < len2) ? n2[i] - '0' : 0;
                int sum = digit2 - digit1 + carry;
                if(sum>=0) carry = sum / 10;
                else
                {
                    carry=-1;
                    sum+=10;
                }
                res[i] = (sum % 10) + '0';
            }
        }
    }
    
    // 处理最后一位进位   
    res[max_len] = carry + '0';  
    res[++max_len]='\0';  
      
    // 反转结果,去除前导零并添加符号
    reverse(res);
    removezeros(res);
    if ((neg1 && neg2)||(neg1==1&&com==1)||(neg2==1&&com==0)) {
        result[0] = '-';  
        strcpy(result + 1, res);  
    } else {  
        strcpy(result, res);  
    }
}

//大数乘法
void mul(char *n1, char *n2, char *result) {
    if(n1[0]=='0'||n2[0]=='0')
    {
        result="0";
        return;
    }
    int len1 = strlen(n1),len2 = strlen(n2),sum,res[3000]={0};
    int result_len = len1 + len2;
    char result1[3000];
    // 从低位到高位进行乘法运算并存到整数数组里
    for (int i = len1 - 1; i >= 0; i--) {  
        for (int j = len2 - 1; j >= 0; j--) { 
            sum = (n1[i] - '0') * (n2[j] - '0') + res[i + j + 1];  
            res[i + j + 1] = sum % 10;  // 当前位的值  
            res[i + j] += sum / 10;     // 进位  
        }  
    }
    //存到字符串里
    int i = result_len - 1;  
    if(neg1==neg2)
    {
        while (i >= 0) { 
                result1[result_len - 1 - i] = res[i] + '0';
                i--;
        }
        result1[result_len] = '\0';  
    }
    else
    {
        while (i >= 0) { 
                result1[result_len - 1 - i] = res[i] + '0';
                i--;
        }
        result1[result_len+1] = '\0';  
    }
    //反转并去除前导0
    reverse(result1);
    removezeros(result1);
    // 添加符号
    if(neg1!=neg2)
    {
        result[0] = '-';
        strcpy(result+1, result1);
    }
    else
        strcpy(result, result1);
}


// 大数除法
void div_sub( char *num1, char *num2, char *res) { //大数除法里的辅助减法，在大数减法的基础上改了下
    int carry = 0, len1 = strlen(num1), len2 = strlen(num2);
    if(strcmp(num1,num2)==0)
    {
        res[0]='0';
        res[1]='\0';
        return;
    }
    reverse(num1);
    reverse(num2);
    for (int i = 0; i < len1; i++) {
        int digit1 = (i < len1) ? num1[i] - '0' : 0,digit2 = (i < len2) ? num2[i] - '0' : 0;
        int sum = digit1 - digit2 + carry;
        if(sum>=0)
            carry = sum / 10;
        else if(sum<0)
        {
            carry=-1;
            sum+=10;
        }
        res[i] = (sum % 10) + '0';
    }
    // 处理最后一位进位 
    res[len1] = carry + '0';  
    res[++len1]='\0';
    // 反转结果,去除前导零
    reverse(res);
    removezeros(res);
    // 添加字符串结束符  
    res[strlen(res)] = '\0';
    reverse(num2);//把num2变回去
}
int div_compare(char *num1,char *num2) {
    int len1 = strlen(num1),len2 = strlen(num2);
    if (len1 != len2) {
        return (len1 - len2);
    }
    return strcmp(num1, num2);
}
void divide(char *num1, char *num2, char *result) {  
    int len1 = strlen(num1),len2 = strlen(num2);
    char temp[2000],temp_result[2000],quotient[2000]; //用于存储被除数的部分
    // 初始化quotient
    memset(quotient, '0', len1 - len2 + 2);
    quotient[len1 - len2 + 1] = '\0';
    // for(int i=0;i<len2;i++) temp[i]=num1[i];
    // temp[strlen(temp)]='\0';
    strncpy(temp,num1,len2);
    // 逐位计算商
    for (int i = len1 - len2; i >= 0; i--) {
        int divisor_multiple = 0; // 除数的倍数（商的当前位）
        // 不断减去除数，直到temp小于除数
        while (div_compare(temp, num2)>=0) {
            divisor_multiple++; // 更新倍数
            div_sub(temp, num2 , temp_result);
            strncpy(temp,temp_result, strlen(temp_result)+1);
            temp[strlen(temp)]='\0';
        }
        // 将商的当前位添加到quotient中  
        quotient[len1 - len2 - i] = divisor_multiple + '0';
        //temp不够减了，往下多取一位
        int len_temp=strlen(temp);
        temp[len_temp]=num1[len1-i];
        temp[++len_temp]='\0';
        removezeros(temp);
    }
    // 移除quotient中的前导零  
    removezeros(quotient);
    // 如果quotient为空（即被除数小于除数），则将其设置为"0"  
    if (quotient[0] == '\0') {  
        strcpy(quotient, "0");
    }
    if ((neg1 != neg2)) {
        result[0] = '-';  
        strcpy(result + 1, quotient);  
    } else {  
        strcpy(result, quotient);  
    }
}

int main()
{
    char str[3000],result[2000];
    fgets(str, sizeof(str), stdin);
    tiqu(str);
    neg1 = (num1[0] == '-'),neg2 = (num2[0] == '-');
    // 去除符号
    strcpy(n1, neg1 ? num1 + 1 : num1);//如果第一位是“-”就将num指针指向下一位
    strcpy(n2, neg2 ? num2 + 1 : num2);
    if(symbol=='+') add(n1,n2,result);
    else if(symbol=='-')
    {
        neg2=(!neg2);
        add(n1, n2, result);
    }
    else if(symbol=='*') mul(n1,n2,result);
    else if(symbol=='/') divide(n1,n2,result);
    printf("%s",result);
    return 0;
}
```