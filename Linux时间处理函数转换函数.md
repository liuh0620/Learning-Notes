# Linux时间处理转换函数

## 1.Linux下存储时间常见的有两种存储方式：

- 从1970从1970年到现在经过了多少秒  
time_t 这种类型就是用来存储从1970年到现在经过了多少秒，要想更精确一点，可以用结构struct timeval，它精确到微妙。
```c
struct timeval
{
    long tv_sec; /*秒*/
    long tv_usec; /*微秒*/
};
```

- 用一个结构体来分别存储年月日时分秒  
```c
struct tm
{
    int tm_sec; /*秒，正常范围0-59， 但允许至61*/
    int tm_min; /*分钟，0-59*/
    int tm_hour; /*小时， 0-23*/
    int tm_mday; /*日，即一个月中的第几天，1-31*/
    int tm_mon; /*月， 从一月算起，0-11*/
    int tm_year; /*年， 从1900至今已经多少年*/
    int tm_wday; /*星期，一周中的第几天， 从星期日算起，0-6*/
    int tm_yday; /*从今年1月1日到目前的天数，范围0-365*/
    int tm_isdst; /*日光节约时间的旗标*/
};
```
需要特别注意的是，年份是从1900年起至今多少年，而不是直接存储如2008年，月份从0开始的，0表示一月，星期也是从0开始的， 0表示星期日，1表示星期一。


## 2.常用的时间函数介绍
```c
#include <time.h>

char *asctime(const struct tm* timeptr); //将结构中的信息转换为真实世界的时间，以字符串的形式显示；

char *ctime(const time_t *timep); //将timep转换为真是世界的时间，以字符串显示，它和asctime不同就在于传入的参数形式不一样；

double difftime(time_t time1, time_t time2); //返回两个时间相差的秒数；

int gettimeofday(struct timeval *tv, struct timezone *tz); //返回当前距离1970年的秒数和微妙数，后面的tz是时区，一般不用；

struct tm* gmtime(const time_t *timep); //将time_t表示的时间转换为没有经过时区转换的UTC时间，是一个struct tm结构指针；

stuct tm* localtime(const time_t *timep); //和gmtime类似，但是它是经过时区转换的时间。

time_t mktime(struct tm* timeptr); //将struct tm 结构的时间转换为从1970年至今的秒数；

time_t time(time_t *t); //取得从1970年1月1日至今的秒数。
```


## 3.实战来看看函数的用法

- 用gmtime将time_t类型的时间转化为struct tm类型的时间，然后用asctime转化为我们常见的格式

```c
/*gettime1.c*/
#include <time.h>
int main()
{
  time_t timep;
  time(&timep);   /*获取time_t类型的当前时间*/
  /*用gmtime将time_t类型的时间转换为struct tm类型的时间按，
    然后再用asctime转换为我们常见的格式 Thu Sep  6 04:07:32 2018
  */
  printf("%s", asctime(gmtime(&timep)));
  return 0;
}
```

编译运行：
```c
~ gcc gettime1.c -o gettime1
~ ./gettime1                
Thu Sep  6 04:07:32 2018
```

- 直接把time_t类型转化为常见的字符串格式
```c
/* gettime2.c*/
#include <time.h>
int main()
{
time_t timep;
time(&timep); /*获取time_t类型当前时间*/
/*转换为常见的字符串：Thu Sep  6 04:07:32 2018*/
printf("%s", ctime(&timep));
return 0;
}
编译并运行：
$gcc -o gettime2 gettime2.c
$./gettime2
Thu Sep  6 04:07:32 2018
```
